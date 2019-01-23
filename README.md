# Serverless Openwhisk Intro
 
Learn about serverless through 10 minutes labs done solely in a browser, and later in the terminal. If you like it give me a star. Find the short link to this site here: [ibm.biz/sv-serverless-2019](http://ibm.biz/sv-serverless-2019)

Feel free to learn more from [the presentation attached - click here](Sadowski-Cloud-Functions-SSA-small.pdf).

You need a LITE Cloud account to do the labs - here comes the signup link to use cloud functions with the free of charge light account (IBM wants just your email) - [ibm cloud](https://ibm.biz/BdYtWL)

You might want to take **a full 101 Serverless / Cloud Function Bootcamp** - the link is here: [github.com/IBM-Cloud/openwhisk-workshops](https://github.com/IBM-Cloud/openwhisk-workshops)

## the browser based examples - no installation!

The following links provide you with some simple examples on using Serverless from the Cloud Function starter kit. The only thing required to start it is a signup for the Cloud to leverage serverless with the lite cloud account. 

- Here comes the signup link to use cloud functions with the free of charge light account - [ibm cloud](https://ibm.biz/BdYtWL)
- check this first - [the link to the starter kit](https://console.bluemix.net/openwhisk)

## the terminal based examples

Here comes some simple examples to run in your terminal. Please check the following pages to test the serverless:

- **Java** - [link to Cloud Function documentation](https://console.bluemix.net/docs/openwhisk/openwhisk_actions.html#creating-java-actions)
- other languages tbc.

### JAVA based terminal example
Add ibmcloud CLI: https://console.bluemix.net/openwhisk/learn/cli

In this section you will create a serverless action, and invoke it in the cloud.
In order to preceed furhter you need [JVM8 - check it out here](http://openjdk.java.net/install/). To make sure just write:
```bash
$ java -version
```

and the possible output:

```bash
java version "1.8.0_181"
Java(TM) SE Runtime Environment (build 1.8.0_181-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.181-b13, mixed mode)
```

Then create a file ```Hello.java```:
```bash
$ cat > Hello.java
```

And copy - paste the following file:
```java
 import com.google.gson.JsonObject;
 public class Hello {
     public static JsonObject main(JsonObject args) {
         String name = "stranger";
         if (args.has("name"))
             name = args.getAsJsonPrimitive("name").getAsString();
         JsonObject response = new JsonObject();
         response.addProperty("greeting", "Hello " + name + "!");
         return response;
     }
 }
```

Before you can compile it, you need to add gson to the ```CLASSPATH``` from [maven repo - hit download](https://search.maven.org/artifact/com.google.code.gson/gson/2.8.5/jar)


```bash
#assuming that you downloaded gson.jar to the current directory:
$ pwd
/your-path-to-the-file/

$ export CLASSPATH=/your-path-to-the-file/gson-2.8.5.jar
```

And the final steps include compiling the file:
```bash
$ javac Hello.java
$ jar cvf hello.jar Hello.class
```

If that finished without the errors you can now create the action in the Cloud:
```bash

$ ibmcloud login 
# I think you do not need this: $ ibmcloud target --cf
$ ibmcloud fn action create helloJava hello.jar --main Hello
```

You can see the action being registered in your dashboard - [link](https://console.bluemix.net/openwhisk/actions)

Finally you can invoke the action:

```bash
$ ibmcloud fn action invoke --result helloJava --param name SVJUG

{
    "greeting": "Hello SVJUG!"
}

```

More information on this example you will find here: https://console.bluemix.net/docs/openwhisk/openwhisk_actions.html#creating-java-actions

## mobile serverless backend as a service

Find the following guide for building the mobile application with the serverless backend for mobile applications with Watson and Mobile Notifications:
- [https://console.bluemix.net/docs/tutorials/serverless-mobile-backend.html?programming_language=swift#mobile-application-with-a-serverless-backend](https://console.bluemix.net/docs/tutorials/serverless-mobile-backend.html?programming_language=swift#mobile-application-with-a-serverless-backend)

## the full examples for further hacking:

Check out this examples to get into the advanced subjects with 
- video analytics with AI in serverless batch jobs - [check Dark Vision app](https://github.com/IBM-Cloud/openwhisk-darkvisionapp)
- https://github.com/csantanapr/ato-serverless-swift
- serverless for mobile backend - https://www.ibm.com/blogs/bluemix/2018/01/performant-swift-serverless-actions/
- [an example for Android](https://github.com/IBM-Cloud/serverless-followupapp-android)

Please follow me on Twitter @blumareks

