# grails-wildfly15
grails 3.3.11 deployment in wildfly-15.0.1.Final

### Step by step
  #### 1. Environment
 grails-3.3.11 framework library, JBoss wildfly-15.0.1.Final already installed.

  #### 2. Create a Grails application, then edit [grails-wildfly15/build.gradle] file dependencies like below

```
  dependencies {
      compile "org.springframework.boot:spring-boot-starter-logging"
      compile "org.springframework.boot:spring-boot-autoconfigure"
      compile "org.grails:grails-core"
      compile "org.springframework.boot:spring-boot-starter-actuator"
      // compile "org.springframework.boot:spring-boot-starter-tomcat"
      provided "org.springframework.boot:spring-boot-starter-tomcat"
      compile "org.grails:grails-plugin-url-mappings"
      compile "org.grails:grails-plugin-rest"
      compile "org.grails:grails-plugin-codecs"
      compile "org.grails:grails-plugin-interceptors"
      compile "org.grails:grails-plugin-services"
      compile "org.grails:grails-plugin-datasource"
      compile "org.grails:grails-plugin-databinding"
      compile "org.grails:grails-web-boot"
      compile "org.grails:grails-logging"
      compile "org.grails.plugins:cache"
      compile "org.grails.plugins:async"
      compile "org.grails.plugins:events"
      compile "org.grails.plugins:hibernate5"
      compile "org.hibernate:hibernate-core:5.1.16.Final"
      compile "org.grails.plugins:views-json"
      compile "org.grails.plugins:views-json-templates"
      console "org.grails:grails-console"
      profile "org.grails.profiles:rest-api"
      runtime "org.glassfish.web:el-impl:2.1.2-b03"
      runtime "com.h2database:h2"
      runtime "org.apache.tomcat:tomcat-jdbc"
      testCompile "org.grails:grails-gorm-testing-support"
      testCompile "org.grails:grails-datastore-rest-client"
      testCompile "org.grails:grails-web-testing-support"
      testCompile "org.grails.plugins:geb:1.1.2"
      testRuntime "org.seleniumhq.selenium:selenium-chrome-driver:2.47.1"
      testRuntime "org.seleniumhq.selenium:selenium-htmlunit-driver:2.47.1"
      testRuntime "net.sourceforge.htmlunit:htmlunit:2.18"

      // lib for jboss xml api support
      runtime 'javax.xml.bind:jaxb-api:2.2.12'
  }
  ```

  Change org.springframework.boot:spring-boot-starter-tomcat, compile to provided
  Add runtime 'javax.xml.bind:jaxb-api:2.2.12' dependency.

  Add the following at the end of the file

  ```
  war {
      archiveName = "grails-webapp.war"
  }
  ```

  #### 3. Edit [grails-wildfly15/grails-app/conf/application.yml] file. just add the below content
  ```
    server:
		   context-path: /grails-webapp
  ```

  #### 4. Go to [grails-wildfly15/src/main/webapp]
  And create a folder **[/WEB-INF]** then within that folder create a file **[jboss-web.xml]** and in that file place below content

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <jboss-web version="10.0"
               xmlns="http://www.jboss.com/xml/ns/javaee"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xsi:schemaLocation="http://www.jboss.com/xml/ns/javaee http://www.jboss.org/j2ee/schema/jboss-web_10_0.xsd">
               <context-root>/grails-webapp</context-root>
  </jboss-web>
```

  #### 5. Create another file in  [grails-wildfly15/src/main/webapp/WEB-INF] named  **[jboss-deployment-structure.xml]** and add the content like below

  ```xml
  <?xml version='1.0' encoding='UTF-8'?>
  <jboss-deployment-structure xmlns="urn:jboss:deployment-structure:1.1">
      <deployment>
          <exclusions>
              <module name="org.hibernate.validator"></module>
          </exclusions>
      </deployment>
  </jboss-deployment-structure>
```
  JBoss uses its own hibernate library that may conflict with Grails hibernate library.

  #### 6. Edit the file [grails-wildfly15/grails-app/init/grails/wildfly15/Application.groovy] with content like below

  ```
    package grails.wildfly15

    import grails.boot.GrailsApp
    import grails.boot.config.GrailsAutoConfiguration
    import org.springframework.boot.autoconfigure.EnableAutoConfiguration
    import org.springframework.boot.autoconfigure.transaction.jta.*

    @EnableAutoConfiguration(exclude=[JtaAutoConfiguration])
    class Application extends GrailsAutoConfiguration {
        static void main(String[] args) {
            GrailsApp.run(Application, args)
        }
    }
  ```
  #### 7. Deployment grails-3.3.9 in JBoss wildfly-15.0.1.Final container
  Go to the Grails app root folder, then apply the commands

    grails clean
    grails compile
    grails war prod

  The war file will be created in folder **[grails-wildfly15/build/libs/grails-webapp.war]**

  #### 8. Start wildfly-15.0.1.Final server from terminal

     /opt/wildfly-15.0.1.Final/bin/standalone.sh

  And open the browser and go to the url http://127.0.0.1:9990/console/index.html
  Then click on Deployment tab, then browse the **[grails-webapp.war]** file and upload it.

  You can also visit http://127.0.0.1:8080/grails-webapp
  
