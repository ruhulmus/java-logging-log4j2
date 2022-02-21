# Java logging with log4j2

Apache Log4j 2, is the fastest Java logging framework and provides significant improvements.
**Log4J2**  supports JSON,XML and YAML in addition to properties file configuration . Here I use XML file for the configuration.

### Add log4j Dependancy :

Add `log4j` Dependancy in  `pom.xml` file:

```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.17.1</version>
</dependency>

<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.17.1</version>
</dependency>
```

Add `maven` Plugin in  `pom.xml` file for Build:

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.0</version>
    <configuration>
        <source>${java.version}</source>
        <target>${java.version}</target>
    </configuration>
</plugin>

<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-shade-plugin</artifactId>
    <version>3.2.0</version>
    <executions>
        <!-- Attach the shade into the package phase -->
        <execution>
            <phase>package</phase>
            <goals>
                <goal>shade</goal>
            </goals>
            <configuration>
                <transformers>
                    <transformer
                            implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>com.log4j2.Main</mainClass>
                    </transformer>
                </transformers>
            </configuration>
        </execution>
    </executions>
</plugin>
```


###  log4j2.xml configuration

Path : `src/resources/log4j2.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="DEBUG">
    <Appenders>
        <Console name="LogToConsole" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>

        <RollingFile name="LogToRollingFile" fileName="logs/app.log"
                     filePattern="logs/$${date:yyyy-MM}/app-%d{MM-dd-yyyy}-%i.log.gz">
            <PatternLayout>
                <Pattern>%d %p %c{1.} [%t] %m%n</Pattern>
            </PatternLayout>
            <Policies>
                <TimeBasedTriggeringPolicy/>
                <SizeBasedTriggeringPolicy size="30 MB"/>
            </Policies>
            <DefaultRolloverStrategy max="3"/>
        </RollingFile>


        <RollingRandomAccessFile name="LogToRollingRandomAccessFile" fileName="logs/app.log"
                                 filePattern="logs/$${date:yyyy-MM}/app-%d{MM-dd-yyyy}-%i.log.gz">
            <PatternLayout>
                <Pattern>%d %p %c{1.} [%t] %m%n</Pattern>
            </PatternLayout>
            <Policies>
                <TimeBasedTriggeringPolicy/>
                <SizeBasedTriggeringPolicy size="3 MB"/>
            </Policies>
            <DefaultRolloverStrategy max="3"/>
        </RollingRandomAccessFile>

    </Appenders>
    <Loggers>
        <!-- avoid duplicated logs with additivity=false -->
        <Logger name="com.log4j2" level="debug" additivity="false">
            <AppenderRef ref="LogToRollingRandomAccessFile"/>
            <AppenderRef ref="LogToConsole"/>
        </Logger>
        <Root level="error">
            <AppenderRef ref="LogToConsole"/>
        </Root>
    </Loggers>
</Configuration>
```

### Configure `Main.java` Class :
Now create a logger class `Main.java` that uses the Log4J2 API to log the messages.

```java
package com.log4j2;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class Main {

    private static final Logger logger = LogManager.getLogger(Main.class);

    public static void main(String[] args) {

        logger.debug("Log4j 2 hello");
        // with Java 8, we can do this, no need to check the log level
        while (true)//test rolling file
            logger.debug("hello {}", () -> getValue());

    }

    static int getValue() {
        return 5;
    }

}
```

### Configure `Error.java` Class :
Create another logger class `Error.java` to create and check exceptions.
```java
package com.log4j2;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class Error {

    private static final Logger logger = LogManager.getLogger(Error.class);

    public static void main(String[] args) {

        try {
            System.out.println(getException());
        } catch (IllegalArgumentException e) {
            logger.error("{}", e);
        }
    }

    static int getException() throws IllegalArgumentException {
        throw new IllegalArgumentException("Hello Exception!");
    }

}
```

### Source Code Run and Test
```cmd
$ git clone https://github.com/ruhulmus/java-logging-log4j2.git
$ cd java-logging-log4j2
$ mvn clean install
$ java -Dlog4j2.contextSelector=org.apache.logging.log4j.core.async.AsyncLoggerContextSelector -jar target/log4j2-1.0.0.jar
```

For more detials about apache log4j2 you can check this link.
[https://logging.apache.org/log4j/2.x/
](https://logging.apache.org/log4j/2.x/)
