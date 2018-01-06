# Java client library for Salesforce Bulk API 2.0

A simple java wrapper for Salesforce Bulk API 2.0

## Requirements

Java 8 or later.

## Usage

### Create client

```java
Bulk2Client client = new Bulk2ClientBuilder()
        .withPassword("<consumer key>", "<consumer secret>", "<username>", "<password>"
        .build();
```

If you’re verifying authentication on a sandbox organization, simply add ``useSandbox``.

```java
Bulk2Client client = new Bulk2ClientBuilder()
        .withPassword("<consumer key>", "<consumer secret>", "<username>", "<password>"
        .useSandbox()
        .build();
```

### Upload CSV data using a multipart request

For data sets under 20,000 characters, you can upload the data as part of a multipart request when you create the job.

```java
TBD
```

### Upload CSV data using a separate request

```java
CreateJobResponse createJobResponse = client.createJob("Account", OperationEnum.INSERT,
        request -> request.withContentType("CSV"));
String jobId = createJobResponse.getId();

String csv = "Name,Description,NumberOfEmployees\n" +
        "TestAccount1,Description of TestAccount1,30\n" +
        "TestAccount2,Another description,40\n" +
        "TestAccount3,Yet another description,50";
client.uploadJobData(jobId, csv);

// When using a separate request to upload data, make sure to close the job
JobInfo closeJobResponse = client.closeJob(jobId);

while (true) {
    TimeUnit.SECONDS.sleep(1);

    GetJobInfoResponse jobInfo = client.getJobInfo(jobId);
    if (jobInfo.isFinished()) {
        break;
    }
}
```

### Retrieve the results of the completed job

```java
try (BufferedReader reader = new BufferedReader(client.getJobSuccessfulRecordResults(jobId))) {
    reader.lines().forEach(System.out::println);
}
```

## Build

```
$ ./gradlew build
```