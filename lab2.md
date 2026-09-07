# Lab 2 — HDFS File Operations Using Java FileSystem API

## Aim
Use Hadoop `FileSystem` API to: write, read, seek, get metadata, `listStatus()`, `globStatus()`.

---

## Phase 0 — Java-enabled Hadoop image

```powershell
docker run -dit --name hadoop apache/hadoop:3.3.6 bash
docker exec -it -u root hadoop bash
```

```bash
sed -i 's|^mirrorlist=|#mirrorlist=|g' /etc/yum.repos.d/CentOS-*.repo
sed -i 's|^#baseurl=http://mirror.centos.org/centos/\$releasever|baseurl=http://vault.centos.org/7.6.1810|g' /etc/yum.repos.d/CentOS-*.repo
yum clean all
yum install -y java-1.8.0-openjdk-devel
yum install -y nano
javac -version
nano --version
exit
```

```powershell
docker commit hadoop apache/hadoop:withjava
docker images
```

---

## Phase 1 — Project files

```powershell
cd C:\Users\rahul\Desktop\bda-labs
mkdir lab2-hdfs
cd lab2-hdfs
notepad docker-compose.yml
```

```yaml
services:
  namenode:
    image: apache/hadoop:withjava
    hostname: namenode
    command: ["hdfs", "namenode"]
    ports:
      - "9870:9870"
    env_file: ./config
    environment:
      ENSURE_NAMENODE_DIR: "/tmp/hadoop-root/dfs/name"

  datanode:
    image: apache/hadoop:withjava
    command: ["hdfs", "datanode"]
    env_file: ./config
```

```powershell
notepad config
```

```text
CORE-SITE.XML_fs.default.name=hdfs://namenode
CORE-SITE.XML_fs.defaultFS=hdfs://namenode
HDFS-SITE.XML_dfs.namenode.rpc-address=namenode:8020
HDFS-SITE.XML_dfs.replication=1
```

> Notepad saves as `config.txt` — rename it:
```powershell
Rename-Item config.txt config
ls   # expect: config, docker-compose.yml
```

---

## Phase 2 — Start HDFS

```powershell
docker compose up -d
docker compose ps
docker compose exec namenode bash
```

```bash
hdfs dfs -mkdir -p /user/student
hdfs dfs -ls /user
```

The listing shows `/user/student`. Open `http://localhost:9870` in a browser — the NameNode web UI confirms HDFS is live.

---

## Phase 3 — Java Program

```bash
nano HdfsDemo.java
```

```java
import java.net.URI;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileStatus;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.FSDataInputStream;
import org.apache.hadoop.fs.FSDataOutputStream;
import org.apache.hadoop.fs.Path;

public class HdfsDemo {
    public static void main(String[] args) throws Exception {
        String uri = "hdfs://namenode:8020";
        Configuration conf = new Configuration();
        FileSystem fs = FileSystem.get(URI.create(uri), conf);

        Path file = new Path("/user/student/quangle.txt");

        FSDataOutputStream out = fs.create(file);
        out.writeBytes("On the top of the Crumpetty Tree\n");
        out.writeBytes("The Quangle Wangle sat\n");
        out.close();
        System.out.println("Wrote file: " + file);

        FSDataInputStream in = fs.open(file);

        byte[] buffer = new byte[32];
        in.read(buffer);
        System.out.println("From start : " + new String(buffer).trim());

        in.seek(33);
        byte[] buf2 = new byte[32];
        in.read(buf2);
        System.out.println("After seek : " + new String(buf2).trim());
        in.close();

        FileStatus st = fs.getFileStatus(file);
        System.out.println("Length     : " + st.getLen() + " bytes");
        System.out.println("Replication: " + st.getReplication());

        System.out.println("--- listStatus /user/student ---");
        for (FileStatus s : fs.listStatus(new Path("/user/student"))) {
            System.out.println("  " + s.getPath().getName());
        }

        System.out.println("--- globStatus *.txt ---");
        for (FileStatus s : fs.globStatus(new Path("/user/student/*.txt"))) {
            System.out.println("  " + s.getPath().getName());
        }

        fs.close();
    }
}
```

Save: `Ctrl+O`, `Enter`, `Ctrl+X`

---

## Phase 4 — Compile, Package, Run

```bash
cd /opt/hadoop
ls -l HdfsDemo.java

rm -rf classes hdfsdemo.jar
export HADOOP_CLASSPATH=$(hadoop classpath)
mkdir classes

javac -classpath $HADOOP_CLASSPATH -d classes HdfsDemo.java
ls -l classes/

jar -cvf hdfsdemo.jar -C classes .
jar -tf hdfsdemo.jar

hadoop jar hdfsdemo.jar HdfsDemo
```

Expected:
```text
Wrote file: /user/student/quangle.txt
From start : On the top of the Crumpetty Tree
After seek : The Quangle Wangle sat
Length     : 56 bytes
Replication: 1
--- listStatus /user/student ---
  quangle.txt
--- globStatus *.txt ---
  quangle.txt
```

---

## Phase 5 — Verify on HDFS

```bash
hdfs dfs -cat /user/student/quangle.txt
```

```text
On the top of the Crumpetty Tree
The Quangle Wangle sat
```

`in.seek(33)` jumps to byte 33 → start of second line.

---

## Phase 6 — Second File Test

```bash
echo "hello hdfs" | hdfs dfs -put - /user/student/notes.txt
hadoop jar hdfsdemo.jar HdfsDemo
```

`listStatus()` / `globStatus()` now show both `quangle.txt` and `notes.txt`.

---

## Phase 7 — Cleanup

```bash
exit
```

```powershell
docker compose down
```

> No persistent volumes — this wipes HDFS data. Recreate `/user/student` before next run.

---

## Viva

Demonstrates HDFS `FileSystem` API: write (`create`), read (`open`), random-access (`seek`), metadata (`getFileStatus`), directory listing (`listStatus`), wildcard matching (`globStatus`).