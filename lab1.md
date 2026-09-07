# Experiment 1: Word Count Using Hadoop MapReduce

## Aim

To implement and execute the Word Count program using Hadoop MapReduce inside a Docker container.

---

# Step 1: Create and Start the Hadoop Container

Open **PowerShell** and run:

```powershell
cd C:\bda-labs
mkdir lab1-wordcount
cd lab1-wordcount
```

```powershell
docker run -dit --name hadoop apache/hadoop:3.3.6 bash
```

Verify that the container is running:

```powershell
docker ps
```

---

# Step 2: Install Required Tools

The `apache/hadoop:3.3.6` image does not include:

- `nano`
- `javac`

Enter the container as root:

```powershell
docker exec -it -u root hadoop bash
```

## Fix CentOS Repository Issue

If `yum install` gives repository or mirror errors, run:

```bash
sed -i 's|^mirrorlist=|#mirrorlist=|g' /etc/yum.repos.d/CentOS-*.repo

sed -i 's|^#baseurl=http://mirror.centos.org/centos/\$releasever|baseurl=http://vault.centos.org/7.6.1810|g' /etc/yum.repos.d/CentOS-*.repo

yum clean all
```

## Install Nano and Java Compiler

```bash
yum install -y nano java-1.8.0-openjdk-devel
```

Verify Java compiler:

```bash
javac -version
```

Expected output:

```text
javac 1.8.0_xxx
```

Exit the root shell:

```bash
exit
```

---

# Step 3: Enter the Container Normally

From PowerShell:

```powershell
docker exec -it hadoop bash
```

Your prompt may look like:

```text
bash-4.2$
```

This is normal and means you are inside the container.

---

# Step 4: Create the WordCount Program

Create the Java file:

```bash
nano WordCount.java
```

Paste the following code:

```java
import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;

import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;

import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class WordCount {

    public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable> {

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context)
                throws IOException, InterruptedException {

            StringTokenizer itr =
                    new StringTokenizer(value.toString());

            while (itr.hasMoreTokens()) {
                word.set(itr.nextToken());
                context.write(word, one);
            }
        }
    }

    public static class IntSumReducer
            extends Reducer<Text, IntWritable, Text, IntWritable> {

        private IntWritable result = new IntWritable();

        public void reduce(
                Text key,
                Iterable<IntWritable> values,
                Context context
        ) throws IOException, InterruptedException {

            int sum = 0;

            for (IntWritable val : values) {
                sum += val.get();
            }

            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {

        Configuration conf = new Configuration();

        Job job = Job.getInstance(conf, "word count");

        job.setJarByClass(WordCount.class);

        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);

        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        FileInputFormat.addInputPath(
                job,
                new Path(args[0])
        );

        FileOutputFormat.setOutputPath(
                job,
                new Path(args[1])
        );

        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

Save and exit Nano:

```text
Ctrl + O
Enter
Ctrl + X
```

Verify the file:

```bash
ls
```

You should see:

```text
WordCount.java
```

---

# Step 5: Create Input Files

Create the input directory:

```bash
mkdir input
```

Create the first input file:

```bash
echo "the quick brown fox the lazy dog the fox" > input/file1.txt
```

Create the second input file:

```bash
echo "a quick dog and a brown fox jumped" > input/file2.txt
```

Verify:

```bash
ls input
```

Expected output:

```text
file1.txt
file2.txt
```

---

# Step 6: Compile the Program

Add Hadoop libraries to the Java classpath:

```bash
export HADOOP_CLASSPATH=$(hadoop classpath)
```

Create a directory for compiled classes:

```bash
mkdir classes
```

Compile the program:

```bash
javac -classpath $HADOOP_CLASSPATH -d classes WordCount.java
```

If there is no error, compilation was successful.

---

# Step 7: Create the JAR File

Package the compiled classes:

```bash
jar -cvf wordcount.jar -C classes .
```

Verify:

```bash
ls
```

You should see:

```text
WordCount.java
classes
input
wordcount.jar
```

---

# Step 8: Run the Hadoop MapReduce Job

Run:

```bash
hadoop jar wordcount.jar WordCount input output
```

A large amount of Hadoop log output may appear.

Wait until the job completes successfully.

---

# Step 9: Check the Output

List the output files:

```bash
hadoop fs -ls output
```

Expected files:

```text
_SUCCESS
part-r-00000
```

Display the final Word Count result:

```bash
hadoop fs -cat output/part-r-00000
```

Since the output directory is also directly accessible inside this container, the following also works:

```bash
cat output/part-r-00000
```

Expected output:

```text
a       2
and     1
brown   2
dog     2
fox     3
jumped  1
lazy    1
quick   2
the     3
```

---

# Result

The Word Count program was successfully compiled, packaged, and executed using Hadoop MapReduce.

The program successfully counted the frequency of each word from the input files.