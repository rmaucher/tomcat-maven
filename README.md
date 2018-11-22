# Building

## Maven build

Update Tomcat version number in the `pom.xml`, customize Tomcat components in the dependencies to keep the ones needed (only the main `tomcat-catalina` is mandatory).
```
mvn clean; mvn package
```

## Docker build

```
docker build -t apache/tomcat-maven:1.0 -f ./Dockerfile .
```
Docker build arguments include `namepsace` (default is `myproject`) and `port` which should match the Tomcat port in `server.xml` (default is `8080`). Other ports that need to be exposed can be added in the `Dockerfile` as needed. Webapps should be added to the `webapps` folder where they will be auto deployed by the host if using the defaults. Otherwise, the `Dockerfile` command line can be edited like below to include the necesary resources and command line arguments to run a single or multiple hardcoded web applications.

# Configuration changes over Tomcat

Configuration is located in `conf/server.xml`, `conf/web.xml`, `conf/logging.properties`, all other configuration files, resources and context files are located in `conf`, identical to standalone Tomcat.

# Running

Add a webapp as folder mywebapp (for this example, or specify another path), or a path from which a configured Host will auto deploy
```
--path: Specify a path the wepapp will use
--war: Add the spcified path (directory or war) as a webapp (if no path has been specified, it will be the root webapp)
```

The JULI logging manager configuration is optional but makes logging more readable and configurable:
`-Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djava.util.logging.config.file=conf/logging.properties`
The default JULI configuration uses `catalina.base`, so specifying the system property with `-Dcatalina.base=.` is also useful.

## Command line example with a single root webapp

```
java -Dcatalina.base=. -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djava.util.logging.config.file=conf/logging.properties -jar target/tomcat-maven-1.0.jar --war myrootwebapp
```

## Command line example with three webapps

```
java -Dcatalina.base=. -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djava.util.logging.config.file=conf/logging.properties -jar target/tomcat-maven-1.0.jar --war myrootwebapp --path /path1 --war mywebapp1 --path /path2 --war mywebapp2
```

# Deployment

If using the Kubernetes cloud clustering membership provider, the pod needs to have the persmission to view other pods. For exemple with Openshift, this is done with:
```
oc policy add-role-to-user view system:serviceaccount:$(oc project -q):default -n $(oc project -q)
```
