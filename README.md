# Using weblogic-maven-plugin

*Author: Steve Button*  

*Date: Jan/Feb 2015*

This project contains a simple Maven project that configures and uses the weblogic-maven-plugin to deploy an application to a running WebLogic Server instance.

The coordinates of the plugin you **want** to be using are:

    <groupId>com.oracle.weblogic</groupId>
    <artifactId>weblogic-maven-plugin</artifactId>
    <version>12.1.3-0-0</version>

The weblogic-maven-plugin:12.1.3-0-0 can perform most of its operations without needing any trace of a local WebLogic Server installation.  It obtains its necessary libraries as ... maven artefacts.

The maven artefacts can be retrieved from a local/corp repository that has been populated using the oracle-maven-sync:12.1.3-0-0 plugin.  This plugin will populate a specified repository with the artefacts that are present in a WebLogic Server installation, including the plugin itself.

**As of January 2015** the plugin and artefacts can also be retrieved from the     Oracle Maven Repository - see [http://maven.oracle.com](http://maven/.oracle.com)

## Configuring weblogic-maven-plugin

The canonical pom.xml to use the weblogic-maven-plugin is this:

    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
      <groupId>buttso.demo.maven</groupId>
      <artifactId>plugindemo</artifactId>
      <version>1.0</version>
      <packaging>war</packaging>
      <name>plugindemo</name>
      <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
      </properties>  
      <dependencies>
        <dependency>
          <groupId>javax</groupId>
          <artifactId>javaee-web-api</artifactId>
          <version>6.0</version>
          <scope>provided</scope>
        </dependency>
      </dependencies>
      <build>
        <plugins>
          <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>2.3.2</version>
            <configuration>
              <source>1.8</source>
              <target>1.8</target>
            </configuration>
          </plugin>
          <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>2.1.1</version>
            <configuration>
              <failOnMissingWebXml>false</failOnMissingWebXml>
            </configuration>
          </plugin>
          <plugin>
            <groupId>com.oracle.weblogic</groupId> 
            <artifactId>weblogic-maven-plugin</artifactId> 
            <version>12.1.3-0-0</version> 
            <!-- These are for convenience, can be supplied on cmd line or exec mapping -->
            <configuration> 
              <user>weblogic</user>
              <password>welcome1</password>
              <name>${project.build.finalName}</name>
              <source>${project.build.directory}/${project.build.finalName}.${project.packaging}</source>          
            </configuration>       
          </plugin>
        </plugins>
      </build>
    </project>

## Command Line Invocation

**Note:** all these goals shown below (and more) do not require a local installation of WebLogic Server to execute - no middleWare home settings is needed. 

To execute the goals successfully a server has to be running *somewhere* to use as a target for the operation :-)

To perform a **deploy** operation

    $ mvn com.oracle.weblogic:weblogic-maven-plugin:deploy
    
To perform a **redeploy** operation

    $ mvn com.oracle.weblogic:weblogic-maven-plugin:redeploy

To perform an **undeploy** operation

    $ mvn com.oracle.weblogic:weblogic-maven-plugin:undeploy
    
To see the **list of deployed** applications    

    $ mvn com.oracle.weblogic:weblogic-maven-plugin:undeploy
    
## Execution Phase Binding for Automatic Invocation

The relevant goals of the weblogic-maven-plugin can be bound to the maven phases so they are automatically executed as part of the maven lifecycle.

    <plugin>
      <groupId>com.oracle.weblogic</groupId> 
      <artifactId>weblogic-maven-plugin</artifactId> 
      <version>12.1.3-0-0</version> 
      <configuration> 
        <user>weblogic</user>
        <password>welcome1</password>
        <name>${project.build.finalName}</name>
        <source>${project.build.directory}/${project.build.finalName}.${project.packaging}</source>          
      </configuration> 
      <!-- ## Can also bind goals to execution phases for automatic execution ## -->
      <executions>
        <execution>
          <id>up</id>
          <phase>pre-integration-test</phase> 
          <goals> 
            <goal>deploy</goal> 
          </goals>
        </execution> 
        <execution>
          <id>down</id>
          <phase>post-integration-test</phase> 
          <goals> 
            <goal>undeploy</goal> 
          </goals>
        </execution> 
      </executions>
    </plugin>

When the maven verify command is issued, the deploy and undeploy goals will be executed using the common configuration values in the respective pre and post integration-test phases. 

    $ mvn verify    
    ...

    [INFO] Webapp assembled in [33 msecs]
    [INFO] Building war: /Users/sbutton/Projects/Java/weblogic-maven-plugin/plugindemo/target/plugindemo-1.0.war

    [INFO] 
    [INFO] --- weblogic-maven-plugin:12.1.3-0-0:deploy (up) @ plugindemo ---
    ...
    Target state: deploy completed on Server AdminServer
    
    [INFO] 
    [INFO] --- weblogic-maven-plugin:12.1.3-0-0:undeploy (down) @ plugindemo ---
    ...
    Target state: undeploy completed on Server AdminServer
   
##TL;DR: A Brief History of WebLogic Maven Plugins

This will provide a short overview/timeline/history of the different maven plugins for WebLogic Server.








