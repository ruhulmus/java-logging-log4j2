# Java logging with log4j2

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


*Add configuration*

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