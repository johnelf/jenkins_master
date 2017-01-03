The Jenkins Continuous Integration and Delivery server. This is based on official one, some tweakings on it, including add key pairs and use centos.

<img src="http://jenkins-ci.org/sites/default/files/jenkins_logo.png"/>


# Usage

```
docker run -p 8080:8080 jenkins
```

This will store the workspace in /var/jenkins_home. All Jenkins data lives in there - including plugins and configuration.
You will probably want to make that a persistent volume (recommended):

```
docker run -p 8080:8080 -v /your/home:/var/jenkins_home jenkins
```

This will store the jenkins data in /your/home on the host.
Ensure that /your/home is accessible by the jenkins user in container (jenkins user - uid 1000).


You can also use a volume container:

```
docker run --name myjenkins -p 8080:8080 -v /var/jenkins_home jenkins
```

Then myjenkins container has the volume (please do read about docker volume handling to find out more).

## Backing up data

If you bind mount in a volume - you can simply back up that directory
(which is jenkins_home) at any time.

This is highly recommended. Treat the jenkins_home directory as you would a database - in Docker you would generally put a database on a volume.

If your volume is inside a container - you can use ```docker cp $ID:/var/jenkins_home``` command to extract the data.
Note that some symlinks on some OSes may be converted to copies (this can confuse jenkins with lastStableBuild links etc)

# Attaching build executors

You can run builds on the master (out of the box) buf if you want to attach build slave servers: make sure you map the port: ```-p 50000:50000``` - which will be used when you connect a slave agent.

<a href="https://registry.hub.docker.com/u/maestrodev/build-agent/">Here</a> is an example docker container you can use as a build server with lots of good tools installed - which is well worth trying.

# Passing JVM parameters

You might need to customize the JVM running Jenkins, typically to pass system properties or tweak heap memory settings. Use JAVA_OPTS environment 
variable for this purpose :

```
docker run --name myjenkins -p 8080:8080 --env JAVA_OPTS=-Dhudson.footerURL=http://mycompany.com jenkins
```
