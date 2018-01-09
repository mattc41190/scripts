# Introduction

Hi my name is Matthew Cale and today I am going to show you how to set up a Groovy project via the GMaven Plus plugin.

Somethings to take note of:

* This video is assumes you have java, and mvn on your computer. If you don't please see details in video on getting these programs installed.

* This video is not tied to an IDE. In an effort to keep my list of unknowns as small as possible I will be using a simple text editor and the command line.

* This video assumes you know the very basics of setting up a project using Maven, so concepts like "Maven Lifecycle" and "POM.xml" are used without much explanation.

* This video is geared towards users on a unix based system, but most of the actual code should be system agnostic.

# Step One

Let's create a Maven project.

Create a new directory somewhere on your system. I usually use my Desktop if I am not working on anything particularly pressing. Today I am going to be making a bookstore application called "NotBadBooks". In it's first form it will simply be a Groovy class that once compiled will print out a list of books that are not bad in my opinion.

`cd Desktop`

`mkdir -p Bombalina/NotBadBooks`

# Step Two

Next we are going to create the file and subdirectories needed for a basic maven project. Maven's default settings expect the following to be present.

1. A POM.xml file in the root of the directory.

`touch pom.xml`

2. A `src/main/groovy` folder, usually, this is `src/main/java`, but since we are making a groovy project we will use `src/main/groovy` this is what GMaven Plus expects by default, but it is highly configurable.

mkdir -p src/main/groovy


3. A `src/test/groovy` folder, usually, this is `src/test/java`, but like I mentioned earlier since we are making a groovy project we will use `src/test/groovy` this is what GMaven Plus expects by default, but it is highly configurable.

`mkdir -p src/test/groovy`

3. Great, now that we have a resources let's get started filling out our POM.xml. Making a POM.xml by hand can be a pain so I am going to paste in the main chunks one by one. Don't worry though, because I will be going over what each section means.

Okay, so here are the project tags. The project is where everything lives. As you can see I have stubbed out some areas for the rest of the POM.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

  <!-- required metadata -->

  <!-- dependencies -->

  <!-- build -->

</project>
```

The first thing we should do is declare the details for our project. Maven needs these details to work.

```xml
<!-- required metadata -->
<modelVersion>4.0.0</modelVersion>
<groupId>bombalina</groupId>
<artifactId>NotBadBooks</artifactId>
<version>1.0-SNAPSHOT</version>
```
This metadata describes the projects, what group it belongs to and what it's called. Additionally, it provides version and what version of the POM model it adheres to.

Now that we have our metadata all sorted out let's add all the dependencies we need to get started.

```xml
<!-- dependencies -->
<dependencies>
  <dependency>
    <groupId>org.codehaus.groovy</groupId>
    <artifactId>groovy-all</artifactId>
    <version>2.4.12</version>
  </dependency>
</dependencies>
```

Great, the last section we will be adding is the build sections. The build section includes the plugins section which is where we will be employing the the GMaven plus plugin. This is by far the largest section. I have split it into two sections. The first is the GMaven Plus plugin itself, and the second is the shade plugin. The reason for the inclusion of the shade plugin is simple. I hate that Maven doesn't create useful artifacts by default and instead requires you to provide a class path to the JAR you just made. Shade is not the only options, to solve this problem, but in my opinion it is the quickest.

```xml
<!-- build -->
<build>
  <plugins>
    <!-- GMavenPlus -->
    <plugin>
      <groupId>org.codehaus.gmavenplus</groupId>
      <artifactId>gmavenplus-plugin</artifactId>
      <version>1.6</version>
      <executions>
        <execution>
          <goals>
            <goal>addSources</goal>
            <goal>addTestSources</goal>
            <goal>compile</goal>
            <goal>compileTests</goal>
          </goals>
        </execution>
      </executions>
    </plugin>
    <!-- Shade -->
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-shade-plugin</artifactId>
      <version>3.1.0</version>
      <executions>
        <execution>
          <phase>package</phase>
          <goals>
            <goal>shade</goal>
          </goals>
          <configuration>
            <transformers>
              <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                <mainClass>bombalina.NotBadBooks.App</mainClass>
              </transformer>
            </transformers>
          </configuration>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```

Good work, as you can see we have added the GMaven Plus plugin. We have included some configurable execution details. At this time we only need to compile Groovy into Java so the plugin is completely configured. Note: if you plan to place your Groovy source code outside of the locations specified earlier you will need to configure the plugin for that.

# Step Three

Now that we have set up the project we can create and run our Groovy application.

Start, by creating the directories for the source code. It is a Maven standard convention to preface the application classes with the directory structure `group/artifact/Class.java` or `(.groovy)`. In my case, that looks like. `bombalina/NotBadBooks/App.groovy`

`cd ~/Desktop/NotBadBooks/src/main/groovy`

`mkdirp bombalina/NotBadBooks`

`touch App.groovy`

Once we have everything set up here we should be able to write a simple Groovy class.

```
package bombalina.NotBadBooks
println "The hitchhiker's guide to the galaxy"
```

I now have an application that should print out a not bad book.

Let's use Maven to compile and run our Groovy application.

`mvn package`

`java -jar target/NotBadBooks-1.0-SNAPSHOT.jar`

If everything went as planned you should be seeing the output of our glorious application.

# Resources:

https://www.java.com/en/download/help/download_options.xml
https://maven.apache.org/install.html
https://mvnrepository.com/artifact/org.codehaus.groovy/groovy-all
https://github.com/groovy/GMavenPlus/wiki/Examples
