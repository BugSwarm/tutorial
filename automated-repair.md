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
mvn clean compile test-compile dependency:copy-dependencies
```


## Step 3: Run Astor on the Artifact

To run Astor on the artifact, run

```sh
java -jar /bugswarm-sandbox/astor.jar -location $(pwd) -dependencies $(pwd)/target/dependency -mode mutrepair
```

To use a different repair mode, change the argument to the `-mode` parameter. For instance, `-mode jgenprog` or `-mode cardumen`

Astor puts output statistics in the directory `output_astor/AstorMain-<repo_name>/` and its proposed patches in the directory `diffSolutions/`

```console
$ for path in diffSolutions/*.diff; do basename $path; cat $path; done
```

```diff
patch_11886_8.diff
--- /src/org/traccar/protocol/AquilaProtocolDecoder.java
+++ /src/org/traccar/protocol/AquilaProtocolDecoder.java
@@ -127 +127 @@
-               if (course > 0) {
+               if (course < 0) {

patch_14230_9.diff
--- /src/org/traccar/protocol/AquilaProtocolDecoder.java
+++ /src/org/traccar/protocol/AquilaProtocolDecoder.java
@@ -127 +127 @@
-               if (course > 0) {
+               if (!(course > 0)) {

patch_5189_4.diff
--- /src/org/traccar/protocol/AquilaProtocolDecoder.java
+++ /src/org/traccar/protocol/AquilaProtocolDecoder.java
@@ -127 +127 @@
-               if (course > 0) {
+               if (course == 0) {

patch_9502_7.diff
--- /src/org/traccar/protocol/AquilaProtocolDecoder.java
+++ /src/org/traccar/protocol/AquilaProtocolDecoder.java
@@ -127 +127 @@
-               if (course > 0) {
+               if (course <= 0) {

```
