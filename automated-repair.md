# Evaluations on the Effectiveness of Automated Program Repair Tools with BugSwarm 

We will test the efficacy Astor using `tananaev-traccar-82839755` as an example artifact.
To find other artifacts that can be used to evaluate Astor, you can filter for artifacts that (1) use Java and (2) have at least one failing test:

```python
api.filter_artifacts(json.dumps({
  'status': 'active',
  'lang': 'Java',
  'failed_job.num_tests_failed': {'$gt': 0}
}))
```

(It may also be useful to limit the size of the diff between the failed and passed versions of the repo, using the `metrics.changes` filter.)

## Step 1: Download and Build Astor

**You can skip this step if you've SSHed into one of the provided machines.
This step is only included for completeness.**

Clone the `SpoonLabs/astor` repo and build it with Maven. (Make sure Maven is configured to use Java 8 or later!)

```sh
git clone https://github.com/SpoonLabs/astor.git
cd astor
mvn package -DskipTests
```

Copy the resulting JAR file to the BugSwarm sandbox directory

```sh
cp target/astor-*-jar-with-dependencies.jar ~/bugswarm-sandbox/astor.jar
```


## Step 2: Build the Failed Version of the Artifact

Start a shell in the artifact using the BugSwarm CLI

```sh
bugswarm run --use-sandbox --image-tag tananaev-traccar-82839755
```

Since Astor requires Java 8 or later, ensure that the `java` and `mvn` commands use it

```sh
export JAVA_HOME=/usr/lib/jvm/java-8-oracle
sudo update-alternatives --set java $JAVA_HOME/jre/bin/java
```

Add the `mvn` executable to the path

```sh
export PATH="/usr/local/maven/bin:$PATH"
```

Change to the failed repo's directory, build it with Maven, and copy the dependencies

```sh
cd ~/build/failed/tananaev/traccar
mvn clean compile test-compile dependency:copy-dependencies
```


## Step 3: Run Astor on the Artifact

To run Astor on the artifact, run

```sh
java -jar /bugswarm-sandbox/astor.jar -location $(pwd) -dependencies $(pwd)/target/dependency -mode jgenprog
```

To use a different repair mode, change the argument to the `-mode` parameter. For instance, `-mode mutrepair` or `-mode cardumen`.

Astor puts output statistics in the directory `output_astor/AstorMain-<repo_name>/` and its proposed patches in the directory `diffSolutions/`.

```sh
for path in diffSolutions/*.diff; do basename $path; cat $path; done
```

```diff
patch_105925_364.diff
--- /src/org/traccar/protocol/AquilaProtocolDecoder.java
+++ /src/org/traccar/protocol/AquilaProtocolDecoder.java
@@ -127,3 +127,3 @@
-               if (course > 0) {
-                       position.setCourse((course - 1) * 45);
-               }
+
+
+

patch_18181_56.diff
--- /src/org/traccar/protocol/AquilaProtocolDecoder.java
+++ /src/org/traccar/protocol/AquilaProtocolDecoder.java
@@ -129 +129,2 @@
-               }
+               }               return id;
+
@@ -131 +131,0 @@
-               return position;

patch_23443_72.diff
--- /src/org/traccar/protocol/AquilaProtocolDecoder.java
+++ /src/org/traccar/protocol/AquilaProtocolDecoder.java
@@ -127,3 +127,3 @@
-               if (course > 0) {
-                       position.setCourse((course - 1) * 45);
-               }
+
+
+

patch_2842_6.diff
--- /src/org/traccar/protocol/AquilaProtocolDecoder.java
+++ /src/org/traccar/protocol/AquilaProtocolDecoder.java
@@ -128 +128 @@
-                       position.setCourse((course - 1) * 45);
+

patch_32725_104.diff
--- /src/org/traccar/model/Position.java
+++ /src/org/traccar/model/Position.java
@@ -58 +58 @@
-       public void setCourse(double course) {          this.course = course;}
+       public void setCourse(double course) {}

patch_34779_112.diff
--- /src/org/traccar/protocol/AquilaProtocolDecoder.java
+++ /src/org/traccar/protocol/AquilaProtocolDecoder.java
@@ -128 +128 @@
-                       position.setCourse((course - 1) * 45);
+

patch_39855_128.diff
--- /src/org/traccar/protocol/AquilaProtocolDecoder.java
+++ /src/org/traccar/protocol/AquilaProtocolDecoder.java
@@ -128 +128 @@
-                       position.setCourse((course - 1) * 45);
+

patch_50113_164.diff
--- /src/org/traccar/protocol/AquilaProtocolDecoder.java
+++ /src/org/traccar/protocol/AquilaProtocolDecoder.java
@@ -128 +128 @@
-                       position.setCourse((course - 1) * 45);
+

patch_54656_180.diff
--- /src/org/traccar/protocol/AquilaProtocolDecoder.java
+++ /src/org/traccar/protocol/AquilaProtocolDecoder.java
@@ -127,3 +127,3 @@
-               if (course > 0) {
-                       position.setCourse((course - 1) * 45);
-               }
+
+
+

patch_59640_196.diff
--- /src/org/traccar/protocol/AquilaProtocolDecoder.java
+++ /src/org/traccar/protocol/AquilaProtocolDecoder.java
@@ -127,3 +127,3 @@
-               if (course > 0) {
-                       position.setCourse((course - 1) * 45);
-               }
+
+
+

patch_63778_216.diff
--- /src/org/traccar/protocol/AquilaProtocolDecoder.java
+++ /src/org/traccar/protocol/AquilaProtocolDecoder.java
@@ -128 +128 @@
-                       position.setCourse((course - 1) * 45);
+

patch_93074_324.diff
--- /src/org/traccar/protocol/AquilaProtocolDecoder.java
+++ /src/org/traccar/protocol/AquilaProtocolDecoder.java
@@ -127,3 +127,3 @@
-               if (course > 0) {
-                       position.setCourse((course - 1) * 45);
-               }
+
+
+
```
