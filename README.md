# <div align="center">Spring-kafka using Schema Registry on Confluent Cloud</div>
## <div align="center">Lab Guide</div>
<br>

## **Agenda**
1. [Log into Confluent Cloud](#step-1)
2. [Create an Environment and Cluster](#step-2)
3. [Create an API Key Pair](#step-3)
4. [Create Kafka Client to Produce and Consume using Schema Registry](#step-4)
5. [How Schema Registry handle mismatch format data](#step-5)
6. [How Kafka Client Dynamically evolve the data](#step-6)
7. [CSFLE for Producer and Consumer](#step-7)
8. [Clean Up Resources](#step-8)
9. [Confluent Resources and Further Testing](#step-9)

***

## **Prerequisites**
<br>

1. Create a Confluent Cloud Account.
    - Sign up for a Confluent Cloud account [here](https://www.confluent.io/confluent-cloud/tryfree/).
    - Once you have signed up and logged in, click on the menu icon at the upper right hand corner, click on “Billing & payment”, then enter payment details under “Payment details & contacts”. A screenshot of the billing UI is included below.

> _**Note:** You will create resources during this workshop that will incur costs. When you sign up for a Confluent Cloud account, you will get free credits to use in Confluent Cloud. This will cover the cost of resources created during the workshop. More details on the specifics can be found [here](https://www.confluent.io/confluent-cloud/tryfree/)._

<div align="center" padding=25px>
    <img src="images/survey1.png" width=75% height=75%>
</div>

<div align="center" padding=25px>
    <img src="images/survey2.png" width=75% height=75%>
</div>

<div align="center" padding=25px>
    <img src="images/survey3.png" width=75% height=75%>
</div>

2. Confluent Cloud cluster with Advanced Stream Governance package
3. For clients, Confluent Platform 7.4.5, 7.5.4, 7.6.1 or higher are required.

***

## **Objective**

<br>

In this workshop, you will have learned how to to use Spring-kafka in Spring framework to produce and consume the data on Confluent Cloud by using the Schema Registry to govern the data on streaming manner.
***


## <a name="step-1"></a>Log into Confluent Cloud

1. Log into [Confluent Cloud](https://confluent.cloud) and enter your email and password.

<div align="center" padding=25px>
    <img src="images/login.png" width=50% height=50%>
</div>

2. If you are logging in for the first time, you will see a self-guided wizard that walks you through spinning up a cluster. Please minimize this as you will walk through those steps in this workshop. 

***

## <a name="step-2"></a>Create an Environment and Cluster

An environment contains clusters and its deployed components such as Apache Flink, Connectors, ksqlDB, and Schema Registry. You have the ability to create different environments based on your company's requirements. For example, you can use environments to separate Development/Testing, Pre-Production, and Production clusters. 

1. Click **+ Add cloud environment**. Specify an **Environment Name** and choose the **Essential Package** for stream governance, then Click **Create**. 

>Stream Governance have two option **Essential** and **Advanced**, which in Advanced it will give you more rich features over the governance such as Stream Lineage up to 7 days, Data Catalog, Business Metadata, and unlimited Schema to be registered on Confluent Cloud.

>_**Note:** There is a *default* environment ready in your account upon account creation. You can use this *default* environment for the purpose of this workshop if you do not wish to create an additional environment._

<div align="center" padding=25px>
    <img src="images/environment1.png" width=50% height=50%>
</div>

<div align="center" padding=25px>
    <img src="images/environment2.png" width=50% height=50%>
</div>

2. Now that you have an environment, click **Create Cluster**. 

> **Note:** Confluent Cloud clusters are available in 3 types: Basic, Standard, and Dedicated. Basic is intended for development use cases so you will use that for the workshop. Basic clusters only support single zone availability. Standard and Dedicated clusters are intended for production use and support Multi-zone deployments. If you are interested in learning more about the different types of clusters and their associated features and limits, refer to this [documentation](https://docs.confluent.io/current/cloud/clusters/cluster-types.html).

3. Chose the **Basic** cluster type. 

<div align="center" padding=25px>
    <img src="images/cluster-type.png" width=50% height=50%>
</div>

4. Click **Begin Configuration**. 
5. Choose **AWS** as your preferred Cloud Provide on **ap-southeast-1 (Singapore)** region, and **Single-AZ** availability zone. 
6. Specify a **Cluster Name**. For the purpose of this lab, any name will work here. 

<div align="center" padding=25px>
    <img src="images/create-cluster.png" width=50% height=50%>
</div>

7. View the associated *Configuration & Cost*, *Usage Limits*, and *Uptime SLA* information before launching. 
8. Click **Launch Cluster**. 

***

## <a name="step-3"></a>Create an API Key

1. Click **API Keys** on the navigation menu. 
2. Click **Create Key** in order to create your first API Key. If you have an existing API Key select **+ Add Key** to create another API Key.

<div align="center" padding=25px>
    <img src="images/create-apikey.png" width=75% height=75%>
</div>

3. Select **Global Access** and then click **Next**. 
4. Copy or save your API Key and Secret somewhere. You will need these later on in the lab, you will not be able to view the secret again once you close this dialogue. 
5. After creating and saving the API key, you will see this API key in the Confluent Cloud UI in the **API Keys** section. If you don’t see the API key populate right away, refresh the browser.
6. Go back to the environment page and select Schema Registry (SR) and copy the Schema Registry endpoint
<div align="center" padding=25px>
    <img src="images/create-srapikey.png" width=75% height=75%>
</div>
<div align="center" padding=25px>
    <img src="images/create-srapikey2.png" width=75% height=75%>
</div>

7. Click the API Key to **+Add API Key**
8. Then copy and save your **SR API Key** and **SR API Secret**.

***

## <a name="step-4"></a>Create Kafka Client to Produce and Consume using Schema Registry

1. Back to your directory and ensure you already clone the github, acess through terminal to edit your **application.properties** for each of your spring client for the endpoint and api key and api secret we have created.
for example:
```bash
cd spring-kafka-schemaregistry/SpringAvroProducer/main/resources/application.properties
```

2. Go to first directory **SpringAvroProducer** to run the kafka client.
```bash
./gradlew generateAvroJava
./gradlew bootRun
```

3. Now check back to the Confluent Cloud Console on the **avro** topic.

4. Stop the producer by using **ctrl+c** to continue with next step on Consumer side.

```bash
cd ../SpringAvroConsumer
./gradlew generateAvroJava
./gradlew bootRun
```
5. If the consumer running well, it will consume all of the message on the topic with exact schematize data.
***

## <a name="step-5"></a>How Schema Registry handle mismatch format data

1. Back to your directory **spring-kafka-schemaregistry** and go to the **SpringAvroFakeProducer** and run the client.
```bash
cd ../SpringAvroFakeProducer
./gradlew generateAvroJava
./gradlew bootRun
```

2. What is the return after running the client?

3. What makes the client return the error?
***

## <a name="step-6"></a>How Kafka Client Dynamically evolve the data
1. Back to your directory **spring-kafka-schemaregistry** and go to the **SpringAvroProducerv2** and run the client.
```bash
cd ../SpringAvroProducerv2
./gradlew generateAvroJava
./gradlew bootRun
```

2. Back to your **Confluent Cloud Console** and click topic to check **avro** topic and check the data contract. 

3. Do you see any difference between the previous producer and new producer?
4. Now try with two different terminal console and go to the **spring-kafka-schemaregistry** directory to access the **SpringAvroConsumer** and **SpringAvroConsumerv2** directory
```bash
cd ../SpringAvroConsumer
./gradlew bootRun
```

```bash
cd ../SpringAvroConsumerv2
./gradlew generateAvroJava
./gradlew bootRun
```

5. Why both consume can still consume the data even though the producer already change the schema that being produce?
***

## <a name="step-7"></a>CSFLE for Producer and Consumer
## Google KMS

Create a Key Ring and add a key to it. Copy the key's resource name as shown.

![](images/create_keyring.jpg)

![](images/create_key.jpg)

![](images/key_resource_name.jpg)

:warning: **Important:** Ensure you grant your Service Account the `Cloud KMS CryptoKey Encrypter/Decrypter` role on the Google KMS key you just created. Otherwise you will not be able to use the key to Encrypt/Decrypt your data!

![](images/gcp_kms_grant_role.jpg)

## Download Service Account Credentials

Download the JSON credentials file for the service account you'd like to use (If you don't already have one, just create a new Key in JSON format and it will automatically be downloaded to your computer). 

You will use the content found here in the Producer/Consumer properties files for `client.id`, `client.email`, `private.key.id`, and `private.key`

![](images/create_SA_Key.jpg)

![](images/create_json.jpg)

## Register Tag and Schema
1. First we need to create Schema Registry authentication in base64 encoded.
```shell
echo -n "<SR API Key:SR API Secret>" | base64
```
2. Save the output because we will need this later.

3. Register the tag using this cli
```shell
curl --request POST --url 'https://<SR endpoint>/catalog/v1/types/tagdefs' --header 'Authorization: Basic <base64 encoded SR Key:Secret>' --header 'Content-Type: application/json' \
--data '[ { "entityTypes" : [ "cf_entity" ],"name" :  "PII", "description" : "PII"} ]'
```

4. Now, register the schema with setting `PII` to the birthday field and defining the encryption rule

```shell
curl --request POST --url 'https://<SR endpoint>/subjects/csfle-test-value/versions' \
  --header 'Authorization: Basic <base64 encoded SR Key:Secret>' \
  --header 'content-type: application/vnd.schemaregistry.v1+json' \
  --data '{"schemaType": "AVRO","schema": "{\"fields\": [{\"name\": \"cust_id\",\"type\": {\"avro.java.string\": \"String\",\"type\":\"string\"}},{\"name\": \"trans_id\",\"type\": {\"avro.java.string\": \"String\",\"type\": \"string\"}},{\"name\": \"card_number\",\"type\": {\"avro.java.string\": \"String\",\"type\": \"string\"}, \"confluent:tags\": [ \"PII\"]},{\"name\": \"amount\",\"type\": {\"avro.java.string\": \"String\",\"type\": \"string\"}}],\"name\": \"Transaction\",\"namespace\": \"io.confluent.developer.avro\",\"type\": \"record\"}","metadata": {"properties": {"owner": "<your name>","email": "<your email>"}}}'
```

## Register Rule

```shell
curl --request POST --url 'https://<SR endpoint>/subjects/csfle-test-value/versions' --header 'Authorization: Basic <base64 encoded SR Key:Secret>' --header 'Content-Type: application/vnd.schemaregistry.v1+json' \
  --data '{
        "ruleSet": {
        "domainRules": [
      {
        "name": "encryptPII",
        "kind": "TRANSFORM",
        "type": "ENCRYPT",
        "mode": "WRITEREAD",
        "tags": ["PII"],
        "params": {
           "encrypt.kek.name": "csfle-gcp",
           "encrypt.kms.key.id": "<KMS source ID",
           "encrypt.kms.type": "gcp-kms"
          },
        "onFailure": "ERROR,NONE"
        }
        ]
      } 
    }'
```

We can check that everything is registered correctly by either executing
```shell
curl --request GET --url 'https://<SR endpoint>/subjects/csfle-test-value/versions/latest' --header 'Authorization: Basic <base64 encoded SR Key:Secret>' | jq
```

or in the CC UI

![](images/CCEncryptionRule.png)

## Producer configuration
1. Back to the main directory of this github project and access the directory **SpringAvroProducerCSFLE**
2. You need to fill all of the application.properties including the credentials from GCP service account that has KMS encryptor and decryptor to be used.
3. Once all is being set, run the code
```shell
./gradlew generateAvroJava
./gradlew bootRun
```

## Consumer configuration
1. Back to the main directory of this github project and access the directory **SpringAvroConsumerCSFLE**
2. You need to fill all of the application.properties including the credentials from GCP service account that has KMS encryptor and decryptor to be used.
3. Once all is being set, run the code
```shell
./gradlew generateAvroJava
./gradlew bootRun
```

Check what is the different between the first topic **avro** with the new one **avro_csfle**

## <a name="step-8"></a>Clean Up Resources

Deleting the resources you created during this workshop will prevent you from incurring additional charges. 

1. The first item to delete is the Apache Flink Compute Pool. Select the **Delete** button under **Actions** and enter the **Application Name** to confirm the deletion. 

<div align="center">
    <img src="images/delete1.png" width=50% height=50%>
</div>

2. Next, delete the Datagen Source connectors for **shoe_orders**, **shoe_products** and **shoe_customers**. Navigate to the **Connectors** tab and select each connector. In the settings tab, you will see a **trash** icon on the bottom of the page. Click the icon and enter the **Connector Name**.

<div align="center">
    <img src="images/delete2.png" width=75% height=75%>
</div>

3. Finally, under **Cluster Settings**, select the **Delete Cluster** button at the bottom. Enter the **Cluster Name** and select **Confirm**. 

<div align="center">
    <img src="images/delete3.png" width=50% height=50%>
</div>

*** 

## <a name="step-9"></a>Confluent Resources and Further Testing

Here are some links to check out if you are interested in further testing:
- [Confluent Cloud Documentation](https://docs.confluent.io/cloud/current/overview.html)
- [Apache Flink 101](https://developer.confluent.io/courses/apache-flink/intro/)
- [Stream Processing with Confluent Cloud for Apache Flink](https://docs.confluent.io/cloud/current/flink/index.html)
- [Flink SQL Reference](https://docs.confluent.io/cloud/current/flink/reference/overview.html)
- [Flink SQL Functions](https://docs.confluent.io/cloud/current/flink/reference/functions/overview.html)

***
