# Evaluation on the Effectiveness of Test Generation tools with BugSwarm

In this section, we evaluate Randoop with BugSwarm to test it test generation capability. We will use `alibaba-fastjson2-15942144023` artifact to evaluate Randoop.

## Step 1: Run artifact's Docker container

```sh
bugswarm run --image-tag alibaba-fastjson2-15942144023
```

## Step 2: Download Randoop

Go to the home directory and download the latest version of Randoop
```sh
cd ~
wget https://github.com/randoop/randoop/releases/download/v4.3.2/randoop-all-4.3.2.jar
```

Setup environment variable
```sh
ls
export RANDOOP_JAR="$(pwd)/randoop-all-4.3.2.jar"
```

Download text editor
```sh
sudo apt-get update && sudo apt-get install vim
```

## Step 3: Reproduce failed job

```sh
run_failed.sh
```

## Step 4: Locate failed test and fault

```sh
find . -name "DateUtilsTestFormat.java"
```

The file we are looking for is in the build/failed/alibaba/fastjson2/core directory. We can open the test file and go to line 150:

```sh
vim ./build/failed/alibaba/fastjson2/core/src/test/java/com/alibaba/fastjson2/util/DateUtilsTestFormat.java
```
From here, we know that there is a fault in the `DateUtils.formatYMDHMS19` method. To verify this, we can use the BugSwarm dataset website.

Open [https://www.bugswarm.org/dataset](https://www.bugswarm.org/dataset), under Image Tag, enter the artifact ID `alibaba-fastjson2-15942144023`, click the link under `Diff URL`.

Based on the diff, we can confirm that the developer fixed the bug by changing the `formatYMDHMS19` method.

## Step 5: Setup `Randoop`

Now, let's locate this `DateUtils` file using the find command:
```sh
find . -name "DateUtils.java"
vim ./build/failed/alibaba/fastjson2/core/src/main/java/com/alibaba/fastjson2/util/DateUtils.java
```

We can see the package is `com.alibaba.fastjson2.util`, we will need this when we run Randoop.

Save dependencies to the target directory
```sh
cd ./build/failed/alibaba/fastjson2/core/
mvn dependency:copy-dependencies
```

## Step 6: Run `Randoop` and see it generated tests

```sh
java -Xmx3000m -classpath $RANDOOP_JAR:target/classes:target/dependency/* randoop.main.Main gentests --testclass=com.alibaba.fastjson2.util.DateUtils
vim ErrorTest0.java
```

Unfortunately, none of the tests Randoop generated reveal the bug.

## Step 7: Run error-revealing tests

First, download JUnit 4
```sh
wget https://repo1.maven.org/maven2/junit/junit/4.13.2/junit-4.13.2.jar
wget https://repo1.maven.org/maven2/org/hamcrest/hamcrest-core/1.3/hamcrest-core-1.3.jar
```

Compile test and code
```sh
javac -classpath .:junit-4.13.2.jar:hamcrest-core-1.3.jar:target/classes:target/dependency/* ErrorTest*.java -sourcepath .:target/classes:target/dependency/*
```

Run test
```sh
java -classpath .:junit-4.13.2.jar:hamcrest-core-1.3.jar:target/classes:target/dependency/* org.junit.runner.JUnitCore ErrorTest
```
