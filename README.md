# Intro
This project shows how to run Tensorflow model on AWS Lambda

# Build

0. Start EC2 box with AL2018.03 (aka AL1) (glibc 2.17)

1. Build https://github.com/awslabs/aws-lambda-cpp.git as described here https://aws.amazon.com/blogs/compute/introducing-the-c-lambda-runtime/

edit `/home/ec2-user/out/lib/aws-lambda-runtime/cmake/packager` and add model downloading line
```
bootstrap_script_no_libc=$(cat <<EOF
#!/bin/bash
set -euo pipefail
curl https://<bucket-host>/<folder>/model.pb -o /tmp/model.pb
...
```

2. Download `libtensorflow.so` v.1.15.0 from https://pivovaa-us-west-1.s3-us-west-1.amazonaws.com/libtensorflow-ivybridge.tar.gz
Put .so files to lib folder

3. Build this project
```
mkdir build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Release -DCMAKE_PREFIX_PATH=~/out
make

make aws-lambda-package-hello

aws s3 cp hello.zip s3://<your_bucket>/<folder>/
```
# Create lambda.

* Amazon S3 link URL - s3://<your_bucket>/<folder>/hello.zip 
* Runtime - Custom runtime
* Handler - hello
* Memory - 3008MB
* Timeout - 1 min

Run Test - you should get the following output
```
{
  "request_id": "c18b7e27-41a6-42c2-9043-c34ffea86453",
  "function_arn": "arn:aws:lambda:us-west-2:9999999999:function:hello-cpp-world",
  "fexists": "True",
  "inference_res": "477.941325"
}
```
