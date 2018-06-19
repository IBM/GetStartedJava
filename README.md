

<!--change the repos -->
<!--change the tracking number -->
[![Build Status](https://travis-ci.com/IBM/GetStartedJava.svg?branch=master)](https://travis-ci.com/IBM/GetStartedJava)

<!--Add a new Title and fill in the blanks -->
# Get Started Using Liberty on IBM Cloud with your Java Applications
In this Code Pattern, we will show you how to build Java applications using Liberty on IBM Cloud Platform.
This journey leverages multiple services:
* The Cloudant NoSQL service is used as a DB to hold _name_ entries submitted from the application
* The Redis Cloud Service is used as a cache to help filter duplicates from the Cloudant DB

This code pattern is designed for developers with an interest in creating Java applications using an open source platform such as IBM Eclipse Tools for IBM Cloud.

<p align="center">
  <kbd>
    <img src="images/GettingStarted.gif" width="300" style="1px solid">
  </kbd>
</p>

There are two parts to this pattern:
* The first part consists of deploying the code using a Cloudant backend
* The second enhances the application further to include Redis as cache broker

When the reader has completed this Code Pattern, they will understand how to:

* Set up a Liberty development environment
* Deploy a Java application locally and on IBM Cloud
* Integrate an IBM Cloud DB service with a Java application

<!--Remember to dump an image in this path-->
![](images/WithRedis.png)

## Flow
<!--Add new flow steps based on the architecture diagram-->
1. User sets up and imports code into Liberty Runtime Environment
2. User provides name for the app
3. App checks Redis cache to see if name has already been added; if name hasn't been added, store name in Cloudant

<!--Update this section-->
## Included components
* [IBM Eclipse Tools for IBM Cloud](https://developer.ibm.com/wasdev/downloads/#asset/tools-IBM_Eclipse_Tools_for_IBM_Cloud): The Tools enable rapid deployment and integration of many services from IBM Cloud into your applications, including such services as Watson and big data analytics
* [Maven](https://maven.apache.org/download.cgi): Maven is a tool that can now be used for building and managing any Java-based project
* [Redis](https://redis.io/): Redis is an open source (BSD licensed), in-memory data structure store, used as a database, cache and message broker.

Note: these services are free for those who have a Lite account.  Redis does offer a free version on IBM Cloud.


<!--Update this section-->
## Featured technologies
* [IBM Cloud](https://www.ibm.com/cloud/): The IBM Cloud offers a broad cloud computing portfolio services.
* [WebSphere Application Server Liberty](https://www.ibm.com/support/knowledgecenter/en/SSAW57_liberty/com.ibm.websphere.wlp.nd.multiplatform.doc/ae/cwlp_about.html): Liberty is a highly composable, fast to start, dynamic application server runtime environment.
* [Git Cloud Foundry](https://github.com/cloudfoundry/cli#downloads): The official command line client for Cloud Foundry

## Install Prerequisites
#### IBM Cloud CLI
To interact with the hosted offerings, the IBM Cloud CLI will need to be installed beforehand. The latest CLI releases can be found at the link [here](https://console.bluemix.net/docs/cli/reference/bluemix_cli/download_cli.html#download_install). An install script is maintained at the mentioned link, which can be executed with one of the following commands

```
# Mac OSX
curl -fsSL https://clis.ng.bluemix.net/install/osx | sh

# Linux
curl -fsSL https://clis.ng.bluemix.net/install/linux | sh

# Powershell
iex(New-Object Net.WebClient).DownloadString('https://clis.ng.bluemix.net/install/powershell')
```
After installation is complete, confirm the CLI is working by printing the version like so
```
bx -v
```

## Running the Application
Follow these steps to setup and run this code pattern manually. The steps are described in detail below.

## Steps
1. [Deploy to IBM Cloud](#1-deploy-to-ibm-cloud)
2. [Clone the Sample Application](#2-clone-the-sample-application)
3. [Manually Deploy App to the IBM Cloud](#3-manually-deploy-app-to-the-ibm-cloud)
4. [Prepare the Application for Deployment to IBM Cloud](#4-prepare-the-application-for-deployment-to-ibm-cloud)
5. [Deploy to IBM Cloud](#5-deploy-to-ibm-cloud)
6. [Add a Database](#6-add-a-database)
7. [Use the Database](#7-use-the-database)
8. [Extending this sample to use Compose for Redis](#8-extending-this-sample-to-use-compose-for-redis)
9. [Import the code into Eclipse](#9-import-the-code-into-eclipse)
10. [Create a Liberty Server Definition](#10-create-a-liberty-server-definition)
11. [Add a Maven dependency for the Jedis Redis client](#11-add-a-maven-dependency-for-the-jedis-redis-client)
12. [Update the API endpoint class](#12-update-the-api-endpoint-class)
13. [Add a factory to create a Jedis connection pool](#13-add-a-factory-to-create-a-jedis-connection-pool)
14. [Add the Properties file for use when running locally](#14-add-the-properties-file-for-use-when-running-locally)
15. [Test the Code Locally](#15-test-the-code-locally)
16. [Publish to IBM Cloud](#16-publish-to-ibm-cloud)


#### For the first part of this pattern, you have two deployment options:
1. Create an [IBM Cloud account](https://console.bluemix.net/registration/?target=%2Fdashboard%2Fapps) deploy the application directly to the IBM Cloud by using the `Deploy to IBM Cloud` button below. Proceed to the section: [Deploy to IBM Cloud](#1-deploy-to-ibm-cloud).

2. Manually deploy the application to the IBM Cloud by first proceeding to [Clone the Sample Application](#2-clone-the-sample-application) and continue through the subsequent steps.

For both options, once you complete the respective option , proceed thereafter to the section: [Add a Database](#6-add-a-database).


## 1. Deploy to IBM Cloud
<!--Update the repo and tracking id-->
[![Deploy to IBM Cloud](https://bluemix.net/deploy/button.png)](https://bluemix.net/deploy?repository=https://github.com/IBM/GetStartedJava.git)

a. Press the above `Deploy to IBM Cloud` button and then click on `Deploy`.

<!--optional step-->
b. In Toolchains, click on `Delivery Pipeline` to watch while the app is deployed. Once deployed, the app can be viewed by clicking `View app`.
![](images/toolchain.png)

<!--update with service names from manifest.yml-->
c. To see the app and services created and configured for this Code Pattern, use the IBM Cloud dashboard. The app is named `GetStartedJava` with a unique suffix.

Once you have deployed the application successfully, you can proceed onto [Add a Database](#6-add-a-database).


## 2. Clone the Sample Application

```
$ git clone https://github.com/IBM/GetStartedJava.git
```

## 3. Manually Deploy App to the IBM Cloud

On the command line, change the directory to where you cloned the sample application

```
$ cd GetStartedJava (or what ever directory structure you used)
```

Make sure you have installed [Maven](https://maven.apache.org/download.cgi). Use Maven to install dependencies and build the `.war` file

```
$ mvn clean install
```

Run the application locally on Liberty

```
$ mvn install liberty:run-server
```

When you see the message _The server defaultServer is ready to run a smarter planet_, you can view your application at http://localhost:9080/GetStartedJava".

To stop your application, press `Ctrl-C` in the command-line window where you started the application.

## 4. Prepare the application for deployment to IBM Cloud
To deploy to IBM Cloud, it can be helpful to set up a `manifest.yml` file. The `manifest.yml` includes basic information about your app, such as the name, how much memory to allocate for each instance and the route. We've provided a sample `manifest.yml` file in the `GetStartedJava` directory.

```
  applications:
   - name: GetStartedJava
     random-route: true
     path: target/GetStartedJava.war
     memory: 512M
     instances: 1
```

> Note: In this `manifest.yml` file, random-route: true generates a random route for your app to prevent your route from colliding with others. If you choose to, you can replace random-route: true with host: myChosenHostName, supplying a host name of your choice.

## 5. Deploy to IBM Cloud

Log in to your IBM Cloud account.

```
$ bx login
```

> If you cannot log in using the `cf login` or `bx login` commands because you have a federated user ID, use either the `cf login --sso` or `bx login --sso` commands to log in with your single sign on ID. See [Logging in with a federated ID](https://console.bluemix.net/docs/iam/login_fedid.html#federated_id) to learn more.

From within the `GetStartedJava` directory, push your application to IBM Cloud.

```
$ bx cf push
```

Note: if you get an error:

`No API endpoint set. Use 'cf login' or 'cf api' to target an endpoint.
FAILED`

go ahead and enter `bx target cf`, and you should be able to successfully enter the `bx cf push` command.

Deploying your application can take a few minutes. When deployment completes, you'll see a message that your app is running. You will see similar results as this:

![](images/ManuelDeploymentResults.png)


View your app at the URL listed in the output of the push command, or view both the app deployment status and the URL by running the following command:

```
$ bx cf apps
```


**Tip:** You can troubleshoot errors in the deployment process by using the `bx cf logs <Your-App-Name> --recent` command.

## 6. Add a Database

Next, we'll add a NoSQL database to this application and set up the application so that it can run locally and on IBM Cloud.

* In your browser, log in to IBM Cloud and go to the Dashboard. Select **Create Resource**.
Choose the **Data and Analytics** section, then select **Cloudant** and create your service.
Go to the **Connections** (on the left-hand navigation) view and select your application, then **Create connection**. You may need to select **connect to app**.

* Select **Restage** when prompted. IBM Cloud will restart your application and provide the database credentials directly to your application using the `VCAP_SERVICES` environment variable. Note that if you select the credentials menu on the on the left-hand navigation of the Cloudant service window, you will not see any credentials created. Again, the credentials are provided underneath the covers directly to the application.  The environment variable is available to the application only when it is running on IBM Cloud.

**Tip:** Environment variables enable you to separate deployment settings from your source code. For example, instead of hardcoding a database password, you can store this in an environment variable which you reference in your source code.

## 7. Use the Database

We're now going to update your local code to point to this database. We'll store the credentials for the services in a properties file. This file will get used ONLY when the application is running locally. When running in IBM Cloud, the credentials will be read from the `VCAP_SERVICES` environment variable.

a. In your browser, go to IBM Cloud, select Dashboard and select your Cloudant service under the Cloud Foundry Services section:
![](images/dashboard.png)

b. click **Service Credentials** on the left-hand navigation

c. If you don't see "view Credentials", select the button `New credential` to create credentials that your local application will access.

d. click on View Credentials
![](images/Getcreds.png)

e. Copy and paste just the url from the credentials to the url field of the `/src/main/resources/cloudant.properties` file (relative to the `GetStartedJava` directory), and save the changes.

f. Restart the server

```
$ bx cf push
```

Run the command `bx cf apps` to get the application URL to run. Any names you enter into the app will now get added to the database.

![](images/GettingStarted.gif)

Your local app and the IBM Cloud app are sharing the database. Names you add from either app will appear in both when you refresh the browsers.


**Tip** Remember, if you don't need your app live on IBM Cloud, stop the app so you don't incur any unexpected charges.

## 8. Extending this sample to use Compose for Redis

This pattern, so far, has a behavior that could be improved with some very simple application logic. When entering the same name twice, the application will add the name to the database a second time. How can we make this better and provide an example of combining a caching service with a data service like Cloudant?

In this part of the pattern, you'll leverage the IBM Eclipse Tools for IBM Cloud to change the code to use Redis as a cache, either locally or using the Compose for Redis service on IBM Cloud.

## 9. Import the code into Eclipse

> For developing and deploying with Eclipse, IBM® Eclipse Tools for IBM Cloud provides plug-ins that can be installed into an existing Eclipse environment to assist in integrating the developer's integrated development environment (IDE) with IBM Cloud.

> Download and install [IBM Eclipse Tools for IBM Cloud](https://developer.ibm.com/wasdev/downloads/#asset/tools-IBM_Eclipse_Tools_for_IBM_Cloud).


Import the GetStartedJava.java code into Eclipse using `File` -> `Import` -> `Maven` -> `Existing Maven Projects` option.

![](images/import-maven-project.png)

## 10. Create a Liberty Server Definition

Get to servers view by clicking from the Menu bar at the top select `Window` -> `Show View` -> `Servers`. You will see the Server view highlighted at the bottom of Eclipse platform.

![](images/ProjectExplorerView.png)


  - In the `Servers` view right-click -> `New` -> `Server`
  - Select `IBM` -> `WebSphere Application Server Liberty` (See under IBM)
  - Enter a destination path (/Users/USERNAME/liberty)
  - Select `Download and install a new runtime environment from ibm.com`
  - Choose `WAS Liberty with Java EE 7 Web Profile`
  - Continue the wizard with default options to Finish


![](images/InstallServer.png)

In a few seconds, your application should be running at [http://localhost:9080/GetStartedJava/](http://localhost:9080/GetStartedJava/)

## 11. Add a Maven dependency for the Jedis Redis client

Use Eclipse to edit the `pom.xml` file to add a Maven dependency to include Jedis.

![](images/EditXML.png)

Inside the `pom.xml` file, right after the `javax.ws.rs` dependency and before the closing `</dependencies>` tag, add the following and then save the file (around line 63):

```
<dependency>
  <groupId>redis.clients</groupId>
  <artifactId>jedis</artifactId>
  <version>2.9.0</version>
</dependency>
```

## 12. Update the API endpoint class

In this step, you'll add a little bit of code to adjust the backend logic used by the application to work with Redis as a cache. Open the `src/main/java/wasdev/sample/rest/VisitorAPI.java` file.

a.  Add imports for Jedis and JedisPool. These can go in at line 31, appending before the `import wasdev...` statements.

``` java
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisPool;
    import wasdev.sample.store.JedisPoolFactory;
```

b.  Copying the style of the example, add after the statement `VisitorStore store = VisitorStoreFactory.getInstance()` on line 42, a statement that retrieves the `JedisPool` instance.

``` java
    //Our database store
    VisitorStore store = VisitorStoreFactory.getInstance();

    //Get instance of our Jedis pool
    JedisPool pool = JedisPoolFactory.getInstance();

```

c. Update the code in the `newToDo` method marked with the `@POST` annotation to perform some checking when a `store` is available for persisting visitors. Starting near line 109, *replace* the original code:

``` Java
    store.persist(visitor);
    return String.format("Hello %s! I've added you to the database.", visitor.getName());

```

> With code that will get a Jedis instance from the    
    pool, and see if a user has already been added. If so, send a modified greeting. If not, persist the
    user to the store and add the user name as a key to
    the cache.

```java
    try {
        Jedis jedis = pool.getResource()
        /// check to see if this user is already in the cache
        if ( jedis.get(visitor.getName()) != null ) {
          return String.format("Hello %s! It's nice to see you again.", visitor.getName());
        }
        store.persist(visitor);
        jedis.set(visitor.getName(),"persisted");
        return String.format("Hello %s! I've added you to the database.", visitor.getName());

    } catch (Exception e) {
      // land here if there is no Redis service, implement default behavior
      store.persist(visitor);
      return String.format("Hello %s! I've added you to the database.", visitor.getName());
    }

```
> For the case of a cache hit, this change to the code
  avoids the cost of persisting the same data to the data store additional times. Jedis implements Closeable, so it will be auto-closed after the `return`  or the last statement in the `try`. In the case where no Redis service is available, the catch on the exception defaults to the original behavior.

d. Save the edits to the file. At this point, there will be errors reported by Eclipse since the factory method has not been created so **Cancel** instead of redeploying the application to Liberty if it is currently running.

## 13. Add a factory to create a Jedis connection pool

This step is quite a bit more involved.  JedisPool is a threadsafe pool of network connections. You can use the pool to reliably create several Jedis instances, given you return the Jedis instance to the pool when done. Doing this helps overcome strange errors that can occur when multi-threading and achieve great performance.

a. Begin by copying the `/src/main/wasdev.sample.store/VisitorStoreFactory.java` class to a class file called `/src/main/wasdev.sample.store/JedisPoolFactory.java`. Add in imports after the `package` statement.

```java
    package wasdev.sample.store;

    import java.net.URI;
    import java.net.URISyntaxException;

    import com.google.gson.JsonObject;

    import redis.clients.jedis.JedisPool;
```

b. Rename the class to `JedisPoolFactory` and replace the constructor for the class.

```java
    public class JedisPoolFactory {

        private static JedisPool pool;
        static {
            try {
                JedisPool jp = new JedisPool(getRedisURI());
                pool = jp;
            } catch (Exception e) {
                pool = null;
            }
        }
    }
```

> To keep the code changes simple, we will add to the factory class the `getRedisURI` method for creating a URI to use in the `JedisPool` constructor.

c. Update the `getInstance` method to return the `JedisPool`

```java
    public static JedisPool getInstance() {
      return pool;
    }
```

d. Using a slightly modified version of the code from `CloudantVisitorStore.java`, obtain the credentials for the Redis instance and provide them as a `URI` object. You will see in `CloudantVisitorStore.java` that this code block returns a `String`, but for the `JedisPool` constructor, a `String` argument is only parsed for a hostname, and not interpreted as a full url. Add after the `getInstance` method in the JedisPoolFactor.java file:

```java
  private static URI getRedisURI() {
    String url;
    URI uri;

    if (System.getenv("VCAP_SERVICES") != null) {
      // When running IBM Cloud, the VCAP_SERVICES env var will have the credentials for all bound/connected services
      // Parse the VCAP JSON structure looking for redis.
      JsonObject redisCredentials = VCAPHelper.getCloudCredentials("redis");
      if(redisCredentials == null){
        System.out.println("No redis cache service bound to this application");
        return null;
      }
      url = redisCredentials.get("uri").getAsString();
    } else {
      System.out.println("Running locally. Looking for credentials in redis.properties");
      url = VCAPHelper.getLocalProperties("redis.properties").getProperty("redis_url");
      if(url == null || url.length()==0){
        System.out.println("To use a database, set the Redis url in src/main/resources/redis.properties");
        return null;
      }
    }
    try {
      uri = new URI(url);
        return uri;
    } catch (URISyntaxException e) {
      // TODO Auto-generated catch block
      e.printStackTrace();
      return null;
    }
  }
```

e. Don't forget to keep the close } at the end of the JedisPoolFactor class definition and save the file.

## 14. Add the Properties file for use when running locally

Copy the `src/main/resources/cloudant.properties` file to `src/main/resources/redis.properties` and update line 3 to: `redis_url=`

> Since this will be parsed as a full URI, if you want to use a local copy of redis, instead of providing `localhost` as the value, set it to be `redis://localhost:6379/`

## 15. Test the Code Locally

a.  There should be no code errors after all of the updates are complete and the local Liberty server should automatically refresh. Access the application at http://localhost:9080/GetStartedJava

b.  Add a user in the application. And then add a user of the same name. Since there is no Redis service defined, duplicates will still be successful.

c.  In IBM Cloud, go to the Cloudant dashboard for the service, if there are any documents listed, select all of them in the `mydb` database and then delete them:

![](images/CloudantUsers.png)

d.  Create a Redis instance: Either start a local copy of Redis and update `redis.properties` with the value `redis://localhost:6379/` OR create a Compose for Redis service instance in IBM Cloud using the service catalog and copy the `URL` credential to the `redis.properties` file.

e.  After updating the `redis.properties` file, restart the application on Liberty.

f.  Use the application again. Try to use the same name twice. At this point, Redis will catch the submission and prevent duplicates.

![](images/AliceTwice.png)

## 16. Publish to IBM Cloud

a.  With the application working correctly, select the application from the navigator and select **Run As**->**Maven install** to build and updated version of the war file.

b.  If you have created a Compose for Redis service on IBM Cloud, publish the application to IBM Cloud. When using the Add application tool, select both the Cloudant and Redis services provisioned in IBM Cloud.

c.  Check status on the IBM Cloud dashboard, and then access the application to test it out. If you need to re-synchronize the cache and Cloudant instances, use the redis command-line shown in the service manage panel and the `flushdb` command to remove all inserted keys. Then from the Cloudant dashboard, remove all documents from `mydb`.


<!--Include any relevant links-->

# Links
* [Liberty for Java](https://console.bluemix.net/docs/runtimes/liberty/index.html#liberty_runtime)
* [Libery Overview](https://www.ibm.com/support/knowledgecenter/SSEQTP_liberty/com.ibm.websphere.wlp.doc/ae/cwlp_about.html)
* [Write Secure Java Web Applications](https://console.bluemix.net/docs/runtimes/liberty/secureWebApp.html#secure_java_web_app)
* [Develop Applications using IBM Eclipse Tools for IBM Cloud](https://console.bluemix.net/docs/runtimes/liberty/eclipseDevelop.html#develop-applications-using-ibm-eclipse-tools-for-ibm-cloud)


<!--keep this-->

# License
[Apache 2.0](LICENSE)
