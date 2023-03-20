---
layout: post
title: Debugging SSL in Java
subtitle: >-
  Just tips, no tricks here!
date: '2022-12-09T14:43:51.000Z'
thumbnail-img: /assets/img/springboot.png
categories: []
keywords: []
tags: [java, jvm, springboot, ssl, devops]
readtime: true
---

# Intro

Debugging Secure Socket Layer (SSL) issues can be difficult to troubleshoot as logs are often cryptic and do not contribute to the resolution of the problem. In this article, we will examine some common SSL debugging tips that can help you resolve SSL issues in your Java application.
That being said, every time I encounter the following exception, I know that resolving the issue will be a challenge!

```
caused by: javax.net.ssl.SSLHandshakeException: sun.security.validator.ValidatorException: 
PKIX path building failed:sun.security.provider.certpath.SunCertPathBuilderException: 
unable to find valid certification path to requested target
```

# Troubleshooting Tips

### Verify SSL Configuration

First things first. We need to verify that the correct keystore and truststore files are being used, and that the keystore password and alias are correct. You can also verify that the SSL configuration is being applied correctly by using the application.properties or application.yml file to set the following properties:

```
server.ssl.key-store=<path to keystore file>
server.ssl.key-store-password=<keystore password>
server.ssl.key-alias=<key alias>
server.ssl.trust-store=<path to truststore file>
server.ssl.trust-store-password=<truststore password>
```

### Turn on JVM SSL Debugging

Turn on the following debug flag in the JVM:

```
-Djavax.net.debug=ssl:handshake
```

p.s. You could set ```-Djavax.net.debug=ssl``` instead of ```ssl:handshake``` but IMO it is an overkill. The logs produced complicate the troubleshooting a lot rather than simplifying it.


### Check the certificates of the target host

Before assuming that the fault lies with the application, use openssl to check the certificate chain on the server you are trying to connect to. Sometimes, SSL issues can occur due to incorrect or expired SSL certificates.

```
openssl s_client -connect <host>:<port>
``` 
 
and check the "Certificate chain" part.


### Verify the truststore of your app

Now that you have checked the certificate chain, make sure that your truststore is properly configured. There are a few things to check here:

- Make sure you trust the ca cert presented by the target host.
```
keytool list -keystore keystore-name.jks
```

- Make sure your specify the correct truststore for your JVM to use.
```
-Djavax.net.ssl.trustStore=/path/to/your/trustststore
```
		
**Note1:** When checking the certs in your truststore, bear in mind that: 
*Certificate may not even be technically valid or it can EVEN be self signed and not validated by a Root CA, but if it exists on the specified Truststore that has been loaded into the Java application then it will still trust it*. i.e. Read [this](https://softwareengineering.stackexchange.com/questions/308532/how-is-this-java-keystore-cert-still-valid).

**Note2:** Settings above are safe for envs that have only one app running, like containers. On e.g. a VM hosting multiple Java apps you can mess up the config of other apps...

### Use sslPoke for testing

Try sslPoke (thanks Atlassian) to test the connection. This is usually the most helpful tool.

**Copy the code below and create SSLPoke.java**

```
import javax.net.ssl.SSLSocket;
import javax.net.ssl.SSLSocketFactory;
import java.io.*;

/** Establish a SSL connection to a host and port, writes a byte and
 * prints the response. See
 * http://confluence.atlassian.com/display/JIRA/Connecting+to+SSL+services
 */
public class SSLPoke {
    public static void main(String[] args) {
		if (args.length != 2) {
			System.out.println("Usage: "+SSLPoke.class.getName()+" <host> <port>");
			System.exit(1);
		}
		try {
			SSLSocketFactory sslsocketfactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
			SSLSocket sslsocket = (SSLSocket) sslsocketfactory.createSocket(args[0], Integer.parseInt(args[1]));

			InputStream in = sslsocket.getInputStream();
			OutputStream out = sslsocket.getOutputStream();

			// Write a test byte to get a reaction :)
			out.write(1);

			while (in.available() > 0) {
				System.out.print(in.read());
			}
			System.out.println("Successfully connected");

		} catch (Exception exception) {
			exception.printStackTrace();
		}
	}
}
```

Atlassian's SSL Poke source code can be found [here](https://confluence.atlassian.com/jira/files/117455/162693139/1/1214460547027/SSLPoke.java).

**Compile it**

```
javac SSLPoke.java
```

**Test it** 

```
java SSLPoke <hostname> <port>
```

or specify the truststore you want to check

```
java -Djavax.net.ssl.trustStore=/path/to/your/trustststore SSLPoke <hostname> <port>
```

### Be patient :) 

SSL debugging and certificate chain verification can be time-consuming, so grab a coffee and good luck!

### Further Reading:

<sub>
<https://matthewdavis111.com/java/poke-ssl-test-java-certs><br />
<https://stackoverflow.com/questions/5871279/ssl-and-cert-keystore><br />
<https://softwareengineering.stackexchange.com/questions/308532/how-is-this-java-keystore-cert-still-valid><br />
<https://stackoverflow.com/questions/23659564/limiting-java-ssl-debug-logging><br />
<https://confluence.atlassian.com/kb/unable-to-connect-to-ssl-services-due-to-pkix-path-building-failed-error-779355358.html><br />