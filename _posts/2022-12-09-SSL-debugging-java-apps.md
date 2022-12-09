---
layout: post
title: Tips for debugging SSL in Java
date: '2022-12-09T14:43:51.000Z'
categories: []
keywords: []
tags: [java, jvm, springboot, ssl, devops]
readtime: true
---

## Intro

I am not sure if you guys have a list of "favorite worst problems" to deal with, but SSL exceptions have a place near the top of my list. 

Certificate issues are difficult to troubleshoot and logs are often quite cryptic and do not help with the problem resolution. That said, each time I see the exception below I know that fun times are ahead!

```
caused by: javax.net.ssl.SSLHandshakeException: sun.security.validator.ValidatorException: 
PKIX path building failed:sun.security.provider.certpath.SunCertPathBuilderException: 
unable to find valid certification path to requested target
```

## Troubleshooting Tips


#### Turn on JVM SSL Debugging

Turn on the following debug flag in the JVM:

```
-Djavax.net.debug=ssl:handshake
```

p.s. You could set ```-Djavax.net.debug=ssl``` instead of ```ssl:handshake``` but IMO it is an overkill. The logs produced complicate the troubleshooting a lot rather than simplifying it.


#### Check certificate chain on the server side

Before assuming that fault is on the application side, use openssl to check the certificate chain on the server you are trying to connect to.

```
openssl s_client -connect <host>:<443>
``` 
 
and check the "Certificate chain" part.


#### Verify the truststore of your app

Now that you know how the certificate chain looks like, make sure your truststore is properly configured. Couple of things to check here:


	- Make sure you trust the ca cert presented by the target host.
	
	```
    keytool list -keystore keystore-name.jks
    ```
	
	- Make sure your specify the correct truststore for your JVM to use.
	
	```
    -Djavax.net.ssl.trustStore=/path/to/your/trustststore
    ```
	
Note1: *Certificate may not even be technically valid or it can EVEN be self signed and not validated by a Root CA, but if it exists on the specified Truststore that has been loaded into the Java application then it will still trust it**. Read [this](https://softwareengineering.stackexchange.com/questions/308532/how-is-this-java-keystore-cert-still-valid).*

Note2: Settings above are safe for envs that have only one app running, like containers. On e.g. a VM hosting multiple Java apps you can mess up the config of other apps...

#### Use sslPoke for testing

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

SSL Debugging and certificate chain verification can take time, grab a coffee and good luck!

## Useful Links:

https://matthewdavis111.com/java/poke-ssl-test-java-certs/

https://stackoverflow.com/questions/5871279/ssl-and-cert-keystore

https://softwareengineering.stackexchange.com/questions/308532/how-is-this-java-keystore-c
ert-still-valid
https://stackoverflow.com/questions/23659564/limiting-java-ssl-debug-logging

https://confluence.atlassian.com/kb/unable-to-connect-to-ssl-services-due-to-pkix-path-building-failed-error-779355358.html