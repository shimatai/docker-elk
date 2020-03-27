# ELK Stack -  ElasticSearch, Logstash and Kibana

## Updated to version 7.6.1

All stack has been updated to version 7.6.1

## Get Started
Do you wanna a simple way to set up a ELK stack?

Just pull this repository:

`git clone https://github.com/shimatai/docker-elk.git`

Then, run `docker-compose` to create docker instances:

```
$ cd docker-elk
$ docker-compose up
```

Then, access Kibana web UI at http://localhost:5601

After getting into Kibana web console, you will get the message `Create index pattern - Couldn't find any Elasticsearch data`, then you should run this command in terminal:

```bash
curl -XPOST -D- 'http://localhost:5601/api/saved_objects/index-pattern' \
 -H 'Content-Type: application/json' \
 -H 'kbn-version: 7.6.1' \
 -d '{"attributes":{"title":"logstash-*","timeFieldName":"@timestamp"}}'
```

This request will create a default index in Kibana, so your application can send data to localhost:5000 (logstash TCP port for incoming log data).

By default, this stack exposes the following ports:

5000 (TCP): Logstash input listener

9200 (TCP): Elasticsearch REST API

9300 (TCP): Elasticsearch TCP nodes communication

5601 (TCP): Kibana web UI

## Logback Configuration

Configure logback framework to send data to logstash and visualize it in Kibana web console.

[How to configure logback in your Java Maven application](https://lankydan.dev/2019/01/09/configuring-logback-with-spring-boot)

### Maven Dependency

Add the following dependency to your `pom.xml` (`<dependencies />` section):

```xml
<!-- https://mvnrepository.com/artifact/net.logstash.logback/logstash-logback-encoder -->
<dependency>
    <groupId>net.logstash.logback</groupId>
    <artifactId>logstash-logback-encoder</artifactId>
    <version>6.3</version>
</dependency>
```

### Basic Logback Configuration
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

	<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
		<encoder>
			<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} - %-5level - %msg%n</pattern>
		</encoder>
	</appender>

	<appender name="SIZE_AND_TIME_BASED_FILE"
		class="ch.qos.logback.core.rolling.RollingFileAppender">
		<file>application.log</file>
		<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
			<fileNamePattern>./logs/application.%d{yyyy-MM-dd}.%i.log</fileNamePattern>
			<maxHistory>10</maxHistory>
			<timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
				<maxFileSize>102400KB</maxFileSize>
			</timeBasedFileNamingAndTriggeringPolicy>
		</rollingPolicy>

		<encoder>
			<!-- <pattern>%relative [%thread] %-5level %logger{35} - %msg%n</pattern> -->
			<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} - %-5level - %msg%n</pattern>
		</encoder>
	</appender>

        <!-- https://github.com/logstash/logstash-logback-encoder -->
        <appender name="STASH" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
          <destination>localhost:5000</destination>
          <!-- encoder is required -->
          <encoder class="net.logstash.logback.encoder.LogstashEncoder" />

          <keepAliveDuration>5 minutes</keepAliveDuration>

          <!--
          <reconnectionDelay>1 second</reconnectionDelay>
          <writeBufferSize>8192</writeBufferSize>
          -->
        </appender>

	<!-- Levels verbose order (high to low): TRACE - DEBUG - INFO - WARN - ERROR -->
        <!-- <logger name="org.hibernate" level="TRACE">
            <appender-ref ref="STASH"/>
        </logger> -->

	<root level="info">
		<!-- <appender-ref ref="STDOUT"/> -->
		<appender-ref ref="SIZE_AND_TIME_BASED_FILE" />
		<appender-ref ref="STASH"/>
	</root>

</configuration>
```

### Advanced Logback Configuration

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

	<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
		<encoder>
			<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} - %-5level - %msg%n</pattern>
		</encoder>
	</appender>

	<appender name="SIZE_AND_TIME_BASED_FILE"
		class="ch.qos.logback.core.rolling.RollingFileAppender">
		<file>application.log</file>
		<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
			<fileNamePattern>./logs/application.%d{yyyy-MM-dd}.%i.log</fileNamePattern>
			<maxHistory>10</maxHistory>
			<timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
				<maxFileSize>102400KB</maxFileSize>
			</timeBasedFileNamingAndTriggeringPolicy>
		</rollingPolicy>

		<encoder>
			<!-- <pattern>%relative [%thread] %-5level %logger{35} - %msg%n</pattern> -->
			<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} - %-5level - %msg%n</pattern>
		</encoder>
	</appender>

        <!-- https://github.com/logstash/logstash-logback-encoder -->
        <appender name="STASH" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
            <destination>localhost:5000</destination>
            <keepAliveDuration>5 minutes</keepAliveDuration>

            <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
                <providers>
                    <mdc/> <!-- MDC variables on the Thread will be written as JSON fields-->
                    <context/> <!--Outputs entries from logback's context -->
                    <logLevel/>
                    <loggerName/>
                    <pattern>
                        <pattern>
                            {
                            "application": {
    	                        "name": "MyApplicationName",
    	                        "version": "1.0.0"
    	                        }
                            }
                        </pattern>
                    </pattern>
                    <threadName/>
                    <message/>
                    <logstashMarkers/> <!-- Useful so we can add extra information for specific log lines as Markers-->
                    <jsonMessage/>
                    <arguments/> <!--or through StructuredArguments-->
                    <stackTrace>
                        <fieldName>stackTrace</fieldName>
                        <throwableConverter class="net.logstash.logback.stacktrace.ShortenedThrowableConverter">
                            <exclude>net\.sf\.cglib\..*</exclude>
                            <maxDepthPerThrowable>30</maxDepthPerThrowable>
                            <rootCauseFirst>true</rootCauseFirst>
                        </throwableConverter>
                    </stackTrace>
                </providers>
            </encoder>
        </appender>

	<!-- Levels verbose order (high to low): TRACE - DEBUG - INFO - WARN - ERROR -->
        <!-- <logger name="org.hibernate" level="TRACE">
            <appender-ref ref="STASH"/>
        </logger> -->

	<root level="info">
		<!-- <appender-ref ref="STDOUT"/> -->
		<appender-ref ref="SIZE_AND_TIME_BASED_FILE" />
		<appender-ref ref="STASH"/>
	</root>

</configuration>
```

## Tutorial from BogoToBogo
[Docker ELK : ElasticSearch, Logstash, and Kibana](https://www.bogotobogo.com/DevOps/Docker/Docker_ELK_ElasticSearch_Logstash_Kibana.php)
