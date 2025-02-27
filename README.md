Download link :https://programming.engineering/product/56528/


# Project-1-Part-2-IaaS-CSE-546-Cloud-Computing
Project 1 (Part 2): IaaS [CSE 546] Cloud Computing
Summary

In Part 2 of Project 1, we will complete the development of the elastic face recognition application using the IaaS resources from AWS. We will implement the App Tier and Data Tier of our multi-tiered cloud application, use a machine learning model to perform face recognition and implement autoscaling to allow the App Tier to dynamically scale on demand.

Description

We will strictly follow the architecture below to develop the application.

Web Tier

You MUST use the same web tier EC2 instance from Part 1 of the project. The web tier Must listen for requests on port number 8000.

It should take images received from users as input and forward it to the App Tier for model inference. It should also return the recognition result from the App Tier as output to the users. The input from each request is a .jpg file, and the output in each response is the recognition result. Find more details below.

Input:

The key to the HTTP payload MUST be defined as “inputFile” and should be used as the same. In the case of a Python backend, it denotes a standard Python file object.

For example, the user uploads an image named “test_00.jpg”.

Use the provided workload generator to generate requests to your Web Tier.

Output:

The Web Tier will handle HTTP POST requests to the root endpoint (“/”).

The output MUST be in plain text, and the format <filename>:<classification_results>

For the above example request, the output should be “test_00:Paul” in plain text.

You need to implement the handling of concurrent requests in your Web Tier.

To facilitate the testing, a standard face dataset and the expected recognition output of each image are provided to you at

Input: visa-lab/CSE546-Cloud-Computing/face_images_1000.zip

Output: visa-lab/CSE546-Cloud-Computing/classification_face_images_1000.csv

The Web Tier uses only one instance. You MUST name your web-tier instance “web-instance”

The Web Tier sends requests to the App Tier and receives results using two SQS Queues. You MUST follow the following naming convention in naming your S3 Buckets:

Request Queue: <ASU ID>-req-queue

Response Queue: <ASU ID>-resp-queue

For example, if your ASU ID is “12345678910”, your queue names will be 12345678910-req-queue and 12345678910-resp-queue

The Web Tier also runs the autoscaling controller, which determines how to scale the App Tier. You are not allowed to use AWS features for autoscaling. You MUST implement your own auto scaling algorithm.

App Tier

The App Tier will use the provided deep learning model for model inference. The deep learning model code is available here. The first step is to create an AMI, which will be used to launch App Tier instances.

Launch a base EC2 instance. You can use the AWS Linux or Ubuntu AMI.

Install the required package on the instance using the following command.

pip3 install torch torchvision torchaudio –index-url https://download.pytorch.org/whl/cpu

Copy the provided deep learning model code and model weights folder to the EC2 instance using scp.

Refer to the README.md on how to use the deep learning model code.


e. Create an AMI using this EC2 instance, following the instructions here. Now, you can use this AMI to create your App Tier instances. You MUST name your app-tier instances “app-tier-instance-<instance#>”

The App Tier should automatically scale out when the request demand increases and automatically scale in when the demand drops. The number of App Tier instances should be 0 when there are no requests being processed or waiting to be processed. The number of App Tier instances can scale to at most 20 because we have limited resources from the free tier.

Data Tier

All the inputs (images) and outputs (recognition results) should be stored in separate buckets on S3 for persistence.

S3 stores all the objects as key-value pairs. For the Input bucket, each object’s key is the input file’s name, e.g., test_00.jpg, and the value is the image file. For the Output bucket, the key is the image name (e.g., test_00), and the value is the classification result (e.g., “Paul”).

You MUST follow the following naming convention in naming your S3 Buckets

Input Bucket: <ASU ID>-in-bucket

Output Bucket: <ASU ID>-out-bucket

For example, if your ASU ID is “12345678910”, your bucket names will be 12345678910-in-bucket and 12345678910-out-bucket

Testing & Grading

Use the provided workload generator to test your app thoroughly. Check the following:

The output of the workload generator is correct.

The contents in the S3 buckets are correct;

While processing the workload, the number of EC2 instances is correct.

All the requests are processed within a reasonable amount of time. As a reference point, for a workload of 100 concurrent requests using the TAs’ implementation of the project, completed within 80 seconds (average of 5 iterations).

Test your application using the provided workload generator and grading script. If they fail to execute, you will receive 0 points. Refer to the Readme on how to use the grading script.


We will use the same Grading IAM user credentials we collected in Part 1 of the project. You MUST update the Grading IAM user with the following permissions:

AmazonEC2ReadOnlyAccess

AmazonS3FullAccess

AmazonSQSFullAccess

Make sure you test everything using the Grading IAM Credentials before making a submission.

To facilitate testing, you MUST use only the resources from the US-East-1 region.

The grading will be done using the workload generator and grading script and following the rubrics below:

#

Test Objective

Test Criteria

Test Command

Fail

Pass

Total

Sequence

Points

1) Single Web Tier instance

Run p2_grader.py &

There is no EC2

The EC2 instance with the name

instance with name

exists and the state of the

use selection

“web-instance” exists; and is in “running”

1

Validate EC2

web-instance is “running”

choice:1 for

“web-instance” (0)

state (2.5)

5

initial state

2) There are 0 running App

validating the

There are app tier

There are no app tier instance in

instances in

Tier instances with name

instances

“running” state (2.5)

“running” state (0)

The input and the output

Run p2_grader.py

The input and the

Validate S3

script & use selection

output buckets either

The input and the output buckets both

2

buckets must exist and

2.5

initial state

choice:2 for

do not exists or are

exists and are empty (2.5)

should be empty

validating S3 Buckets

not empty(0)

The SQS Request and

Run p2_grader.py

The request and the

Validate SQS

script & use selection

The request and the response queue

3

Response Queues must

choice:3 for

response queues

2.5

initial state

exist and have no

either do not exists

both exist and are empty (2.5)

validating SQS

messages in the queue

or are not empty(0)

Queues

1) Start the

p2_grader.py before

the workload

generator and use

Validate the

selection choice: 4

Total number of

1) Run

Total number of requests completed

completeness

requests completed

The HTTP Response code

workload_generator.

successfully stats from workload

4

of the

must be 200 for all the

py with 10 requests

successfully stats

generator is 10 (5)

5

10-request

from workload

requests

2) After the workload

+0.5 for every request completed

workload

generator is not 10

generator is over,

successfully.

(0)

check the stats “Total

number of requests

completed

successfully”


Validate the

After the workload

Total number of

The Total number of correct predictions

correctness of

generator is over,

correct predictions

All the classification

stats from workload generator.py is 10

5

the

results must be correct

check the stats “Total

stats from

(5)

classification

number of correct

workload_generator.

results

predictions”

py is not 10 (0)

+0.5 for every correct classification.

The S3 input and Output

Run p2_grader.py &

The S3 Input and

Validate S3

use selection

Output Buckets do

The S3 Input and Output Buckets have 10

Buckets should each have

5

buckets

choice:2 for

not have 10 objects.

objects (5)

10 objects

validating S3 Buckets

(5)

The # of app tier

1) The # of app tier

instances do not

gradually scale from

instances should gradually

The # of app tier instances gradually

Validate the prints

0 to 10 and reduce

scale from 0 to 10 and

from p2_grader.py

back to 0 (0)

scale from 0 to 10 and reduce back to 0

Validate

reduce back to 0

(10)

when the selection

The # of SQS

15

autoscaling

2) The # of SQS messages

The # of SQS messages in Request Queue

choice: 4 was made

messages in the

in Request Queue should

from Step-1

Request Queue do

gradually increases from 0 to 10 and

increase from 0 and then

reduce back to 0 (5)

not gradually

reduce back to 0

increase from 0 and

reduce back to 0 (0)

The Total

The Total

test

Check the “Total Test

test duration

The Total test

The total test

duration

Duration” stats from

for 10

duration for 10

Validate total

Total end to end latency

duration for 10

for 10

the

requests is

requests is

15

latency

must be reasonable

requests is greater

requests

workload_generator.

between 3

between 2 and

than 4 min (0)

is less

py

and 4 min

3 min (10)

than 2

(5)

min (15)

1) Start the

p2_grader.py before

the workload

generator and use

Validate the

selection choice: 4

1) Run

Total number of

Total number of requests completed

completeness

The HTTP Response code

workload_generator.

requests completed

successfully stats from workload

of the

must be 200 for all the

5

5

py with 50 requests

successfully stats

generator is 50 (5)

50-request

requests

2) After the workload

from workload

+0.1 for every request completed

workload

generator is over,

generator is not 50(0)

successfully.

check the stats “Total

number of requests

completed

successfully”

Validate the

All the classification

After the workload

The Total number of

Total number of correct predictions stats

5

Policies

Late submissions will absolutely not be graded (unless you have verifiable proof of emergency). It is much better to submit partial work on time and get partial credit than to submit late for no credit.

You need to work independently on this project. We encourage high-level group discussions to help others understand the concepts and principles. However, code-level discussion is prohibited, and plagiarism will directly lead to failure in this course. We will use anti-plagiarism tools to detect violations of this policy.

