# 02 - Build a simple Spring Boot microservice

__This guide is part of the [Azure Spring Cloud training](../README.md)__

Build the simplest possible Spring Boot microservice, made with [https://start.spring.io/](https://start.spring.io/).

---

## Create a simple Spring Boot microservice

The microservice that we create in this guide is [available here](simple-microservice/).

To create our microservice, we will use [https://start.spring.io/](https://start.spring.io/) with the command line:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web -d baseDir=simple-microservice -d bootVersion=2.1.9.RELEASE | tar -xzvf -
```

> We force the Spring Boot version to be 2.1.9

Go into the `simple-microservice` directory to view what has been generated:

```bash
cd simple-microservice
```

## Add a new Spring MVC Controller

Open the project with your favorite IDE, and next to the `DemoApplication` class, create a new class called `HelloController` with the following content:

```java
package com.example.demo;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/hello")
    public String hello() {
        return "Hello from Azure Spring Cloud";
    }
}
```

The final project is available in the ["simple-microservice" folder](simple-microservice/).

## Test the project locally

Run the project:

```bash
./mvnw spring-boot:run
```

Requesting the `/hello` endpoint should return the "Hello from Azure Spring Cloud" message.

```bash
curl http://127.0.0.1:8080/hello
```

## Create and deploy the application on Azure Spring Cloud

This section shows how to create an app instance, and then deploy your
code to it.

In order to create the app instance graphically, you can use [the Azure portal](https://portal.azure.com/?WT.mc_id=azurespringcloud-github-judubois):

- Look for your Azure Spring Cloud cluster in your resource group
- Click on the "Overview" link at the top of the navigation sidebar. 
- Click on the link "Create App".  You may have to scroll to reveal the "Create App" blade.

![Create app](media/00-create-app-button.png)

- Create a new application named "simple-microservice"

![Create application](media/01-create-application.png)

- Click on "Review and Create", then "Create"

You can also use the command line to create the app instance, which is easier:

```bash
az spring-cloud app create -n simple-microservice
```

You can now build your "simple-microservice" project and send it to Azure Spring Cloud:

```bash
./mvnw clean package
az spring-cloud app deploy -n simple-microservice --jar-path target/demo-0.0.1-SNAPSHOT.jar
```

This creates a jar file on your local disk and uploads it to the app instance you created in the preceding step.  The `az` command will output a result in JSON.  You don't need to pay attention to this output right now, but in the future, you will find it useful for diagnostic and testing purposes.

## Test the project in the cloud

Go to [the Azure portal](https://portal.azure.com/):

- Look for your Azure Spring Cloud cluster in your resource group
- Click "Apps" in the "Settings" section of the navigation pane and select "simple-microservice"
- Mouse over the URL labeled as "Test Endpoint" and click the clipboard icon that appears.  This will give you something like:
   `https://primary:BBQM6nsYnmmdQREXQINityNx63kWUbjsP7SIvqKhOcWDfP6HJTqg27klMLaSfpTB@rwo1106f.test.azuremicroservices.io/simple-microservice/default/`
   Note the text between `https://` and `@`.  These are the basic authentication credentials, without which you will not be authorized to access the service.

You can now use cURL again to test the `/hello` endpoint, this time served by Azure Spring Cloud.  For example.

```
curl https://primary:BBQM6nsYnmmdQREXQINityNx63kWUbjsP7SIvqKhOcWDfP6HJTqg27klMLaSfpTB@rwo1106f.test.azuremicroservices.io/simple-microservice/default/hello/
```

Note that we have appended `hello/` to the URL.  Failure to do this will result in a "404 not found".


## Conclusion

Congratulations, you have deployed your first Spring Boot microservice to Azure Spring Cloud!

If you need to check your code, the final project is available in the ["simple-microservice" folder](simple-microservice/).

Here is the final script to build and deploy everything that was done in this guide:

```
curl https://start.spring.io/starter.tgz -d dependencies=web -d baseDir=simple-microservice -d bootVersion=2.1.9.RELEASE | tar -xzvf -
cd simple-microservice
cat > HelloController.java << EOF
package com.example.demo;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/hello")
    public String hello() {
        return "Hello from Azure Spring Cloud";
    }
}
EOF
mv HelloController.java src/main/java/com/example/demo/HelloController.java
az spring-cloud app create -n simple-microservice
./mvnw clean package
az spring-cloud app deploy -n simple-microservice --jar-path target/demo-0.0.1-SNAPSHOT.jar
```

---

⬅️ Previous guide: [01 - Create a cluster](../01-create-a-cluster/README.md)

➡️ Next guide: [03 - Configure application logs](../03-configure-application-logs/README.md)
