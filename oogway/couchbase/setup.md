# Setup

### Create the cluster and buckets

https://issues.couchbase.com/browse/MB-20512

Changes from the Docker file:

- rename cluster-init-username to cluster-username and cluster-init-password to cluster-password
- remove all bucket-password arguments (Because couchbase 6 cli doesn't want them)

```
couchbase-cli cluster-init -c localhost:8091 --cluster-username=admin --cluster-password=oogway --cluster-ramsize=1024 --services=data,index,query --cluster-index-ramsize=256 --index-storage-setting=default
couchbase-cli bucket-create -c localhost:8091 --bucket=ephemeral --bucket-type=couchbase --bucket-replica=0 --bucket-ramsize=100 -u admin -p oogway
couchbase-cli bucket-create -c localhost:8091 --bucket=test-ephemeral --bucket-type=couchbase --bucket-replica=0 --bucket-ramsize=100 -u admin -p oogway
couchbase-cli bucket-create -c localhost:8091 --bucket=idmusers --bucket-type=couchbase --bucket-replica=0 --bucket-ramsize=100 -u admin -p oogway
couchbase-cli bucket-create -c localhost:8091 --bucket=oogway --bucket-type=couchbase --bucket-replica=0 --bucket-ramsize=150 -u admin -p oogway
couchbase-cli bucket-create -c localhost:8091 --bucket=test-oogway --bucket-type=couchbase --bucket-replica=0 --bucket-ramsize=150 -u admin -p oogway
couchbase-cli bucket-create -c localhost:8091 --bucket=session --bucket-type=couchbase --bucket-replica=0 --bucket-ramsize=100 -u admin -p oogway
couchbase-cli bucket-create -c localhost:8091 --bucket=test-session --bucket-type=couchbase --bucket-replica=0 --bucket-ramsize=100 -u admin -p oogway
couchbase-cli bucket-create -c localhost:8091 --bucket=audit --bucket-type=couchbase --bucket-replica=0 --bucket-ramsize=100 -u admin -p oogway
```

### Create indexes

Use back ticks for index names with hyphens

```
cbq -engine=http://admin:oogway@localhost:8093  << EOF
CREATE PRIMARY INDEX ON `ephemeral` USING GSI;
CREATE PRIMARY INDEX ON `test-ephemeral` USING GSI;
CREATE PRIMARY INDEX ON `oogway` USING GSI;
CREATE PRIMARY INDEX ON `test-oogway` USING GSI;
CREATE PRIMARY INDEX ON `session` USING GSI;
CREATE PRIMARY INDEX ON `test-session` USING GSI;
CREATE PRIMARY INDEX ON `audit` USING GSI;
EOF
```

### Add views?

```
curl -X PUT -H 'Content-Type: application/json' http://admin:oogway@localhost:8092/ephemeral/_design/CouchEphemeral -d @ephemeral_CouchEphemeral.ddoc
curl -X PUT -H 'Content-Type: application/json' http://admin:oogway@localhost:8092/test-ephemeral/_design/CouchEphemeral -d @ephemeral_CouchEphemeral.ddoc
curl -X PUT -H 'Content-Type: application/json' http://admin:oogway@localhost:8092/ephemeral/_design/CouchAllDocuments -d @ephemeral_CouchAllDocuments.ddoc
curl -X PUT -H 'Content-Type: application/json' http://admin:oogway@localhost:8092/test-ephemeral/_design/CouchAllDocuments -d @ephemeral_CouchAllDocuments.ddoc
curl -X PUT -H 'Content-Type: application/json' http://admin:oogway@localhost:8092/oogway/_design/CouchProfile -d @oogway_CouchProfile.ddoc
curl -X PUT -H 'Content-Type: application/json' http://admin:oogway@localhost:8092/test-oogway/_design/CouchProfile -d @oogway_CouchProfile.ddoc
curl -X PUT -H 'Content-Type: application/json' http://admin:oogway@localhost:8092/oogway/_design/CouchAllDocuments -d @oogway_CouchAllDocuments.ddoc
curl -X PUT -H 'Content-Type: application/json' http://admin:oogway@localhost:8092/test-oogway/_design/CouchAllDocuments -d @oogway_CouchAllDocuments.ddoc
```

If it works, you should be able to see views per bucket. E.g. the following for **Production** views for **oogway** bucket.

![Oogway views in couchbase web UI](oogway-views.jpg)




### Users

```
couchbase-cli user-manage -c 127.0.0.1:8091 -u admin -p oogway --set --rbac-username oogway --rbac-password oogway --rbac-name "Oogway Bucket" --roles "bucket_admin[oogway],bucket_full_access[oogway],views_admin[oogway],views_reader[oogway]" --auth-domain local
couchbase-cli user-manage -c 127.0.0.1:8091 -u admin -p oogway --set --rbac-username session --rbac-password session --rbac-name "Session Bucket" --roles "bucket_admin[session],bucket_full_access[session],views_admin[session],views_reader[session]" --auth-domain local
couchbase-cli user-manage -c 127.0.0.1:8091 -u admin -p oogway --set --rbac-username idmusers --rbac-password idmusers --rbac-name "Idmusers Bucket" --roles "bucket_admin[idmusers],bucket_full_access[idmusers],views_admin[idmusers],views_reader[idmusers]" --auth-domain local
couchbase-cli user-manage -c 127.0.0.1:8091 -u admin -p oogway --set --rbac-username ephemeral --rbac-password ephemeral --rbac-name "ephemeral Bucket" --roles "bucket_admin[ephemeral],bucket_full_access[ephemeral],views_admin[ephemeral],views_reader[ephemeral]" --auth-domain local
```










# IPB-15149 - Oogway: Add Home territory to Create Profile

https://agile.at.sky/browse/IPB-15149

## Trying to send a `home` block

(or trying to send *any* block that is not expected)

Oogway throws a validation error.

![Postman Oogway invalid home parameter](postman-oogway-invalid-home-parameter.jpg)

```
2021-12-08 11:05:52,286  [client=oogway, provider=SKY, proposition=SKY, territory=GB] [http-bio-8080-exec-6] ERROR s.o.e.m.InvalidPropertyExceptionMapper - InvalidPropertyException
sky.oogway.exception.InvalidPropertyException: Invalid parameter [home]
	at sky.oogway.view.OogwayMessageBodyReader.readFrom(OogwayMessageBodyReader.java:93)
	at sky.oogway.view.OogwayMessageBodyReader.readFrom(OogwayMessageBodyReader.java:30)
	at org.glassfish.jersey.message.internal.ReaderInterceptorExecutor$TerminalReaderInterceptor.invokeReadFrom(ReaderInterceptorExecutor.java:233)
	at org.glassfish.jersey.message.internal.ReaderInterceptorExecutor$TerminalReaderInterceptor.aroundReadFrom(ReaderInterceptorExecutor.java:212)
	at org.glassfish.jersey.message.internal.ReaderInterceptorExecutor.proceed(ReaderInterceptorExecutor.java:132)

ts=2021-12-08T11:05:52.320Z,exception="InvalidPropertyException",service.id="SRVCI0011861",destination.port="8080",sky.provider_territory="GB",ua="PostmanRuntime/7.28.4",url.scheme="http",elapsed=212,sky.top_tenant="identity",sky.request_id="8d659ea0-9ec5-4391-8ad0-aa7af17d2c4a",http.request.method="PUT",sky.provider="SKY",provider="SKY",requestId=cee2dc34-bde9-4eae-88d5-f42c61938428,sky.proposition="SKY",proposition="SKY",client="oogway",source.ip=0:0:0:0:0:0:0:1,http.response.status_code="400",errorcode=902,error.code="902",user_agent.original="PostmanRuntime/7.28.4",method=PUT,destination.ip="0:0:0:0:0:0:0:1",accept=v4,contenttype=v4,event.duration="212",@timestamp="2021-12-08T11:05:52.111Z",source.port="61005",url.domain="localhost",territory="GB",status=400
```


## Deleting all docs from a bucket

During dev, you might want to quickly delete all docs from a bucket.

```
curl -X POST -u USER:PASSWORD localhost:8091/pools/default/buckets/BUCKET_NAME/controller/doFlush
```

For example:

```
curl -X POST -u admin:oogway localhost:8091/pools/default/buckets/oogway/controller/doFlush
curl -X POST -u admin:oogway localhost:8091/pools/default/buckets/session/controller/doFlush
curl -X POST -u admin:oogway localhost:8091/pools/default/buckets/ephemeral/controller/doFlush
```

If flushing is not enabled for the bucket, you might see...

```
{"_":"Flush is disabled for the bucket"}%
```

You need to enable *flush* for the bucket to delete all docs from a bucket...

![Bucket enable flush](bucket-enable-flush.jpg)
