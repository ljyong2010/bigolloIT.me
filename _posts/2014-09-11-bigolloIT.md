---
layout: default
title: 编译hadoop2.2在linux64位上使用
category: 技术文档
comments: true
----


1） Install developer tools and dependencies:
 1a) for ubuntu
apt-get install gcc g++ make maven cmake zlib zlib1g-dev

for RedHat environments, you can probably use a similar yum line:

yum install gcc g++ make maven cmake zlib zlib-devel

There may be some other dependencies or slightly different package names depending on what you already have installed and what OS you are running. If so, some google-able errors will pop up during the rest of the process.

1b) Protocol Buffers From Source:

mkdir /tmp/protobuf

cd /tmp/protobuf
wget http:// protobuf.googlecode.com/files/protobuf-2.5.0.tar.gz

tar -xvzf ./protobuf-2.5.0.tar.gz

cd protobuf-2.5.0

./configure --prefix=/usr

make

sudo make install

cd java

mvn install

mvn package

sudo ldconfig

cd /tmp

rm -rf protobuf
2) download hadoop source:

mkdir /tmp/hadoop-build

cd /tmp/hadoop-build

wget http:// apache.petsads.us/hadoop/common/hadoop-2.2.0/hadoop-2.2.0-src.tar.gz

tar -xvzf ./hadoop-2.2.0-src.tar.gz

cd hadoop-2.2.0-src

3) Edit the hadoop-auth pom file.

vi hadoop-common-project/hadoop-auth/pom.xml

add the following dependency:

<dependency>

   <groupId>org.mortbay.jetty</groupId>

  <artifactId>jetty-util</artifactId>

  <scope>test</scope>

</dependency>

You should see an already existing dependency that looks very similar if you search for "org.mortbay.jetty", add this dependency above or below it.
3) Compile it:

export Platform=x64

cd /tmp/hadoop-build/hadoop-2.2.0-src

mvn clean install -DskipTests

cd hadoop-mapreduce-project

mvn package -Pdist,native -DskipTests=true -Dtar

cd /tmp/hadoop-build/hadoop-2.2.0-src

mvn package -Pdist,native -DskipTests=true -Dtar

4) Copy your natively compiled distribution somewhere to be saved:

cp /tmp/hadoop-build/hadoop-2.2.0-src/hadoop-dist/target/hadoop-2.2.0.tar.gz /my/distribution/share/hadoop-2.2.0.tar.gz

5) Delete the build files (once you are satisfied that everything is working properly):

cd /tmp

rm -rf hadoop-build

Now any fresh installations based on this build will include native 64 bit libraries. You can set up a new instance of hadoop locally, or you can simply overwrite the files in the $HADOOP-INSTALL/lib/native directory with those in your hadoop-2.2.0.tar.gz file.