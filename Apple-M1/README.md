# Apple M1

## cybertron-openjdk Docker

### Build locally

1. Run the image with a Bash shell (and notice it's x86_64):

```bash
➜  cybertron-openjdk git:(master) docker run -it sky-identity-docker-local.jfrog.io/cybertron-openjdk:131 /bin/bash
WARNING: The requested image's platform (linux/amd64) does not match the detected host platform (linux/arm64/v8) and no specific platform was requested
root@39449d07edab:/# uname -a
Linux 39449d07edab 5.10.47-linuxkit #1 SMP PREEMPT Sat Jul 3 21:50:16 UTC 2021 x86_64 GNU/Linux
```

2. In another shell on the host, copy the files out of the container to the host, into the `id-docker` repo's `/cybertron-openjdk/cert/` folder:

```bash
➜  cybertron-openjdk git:(master) docker ps
CONTAINER ID   IMAGE                                                      COMMAND       CREATED          STATUS          PORTS     NAMES
56c8f555670f   sky-identity-docker-local.jfrog.io/cybertron-openjdk:131   "/bin/bash"   11 seconds ago   Up 10 seconds             interesting_borg
➜  cybertron-openjdk git:(master) docker cp 56c8f555670f:/usr/local/share/ca-certificates/ ~/work/id-docker/cybertron-openjdk/cert/
```

3. Exit the container:

```bash
root@56c8f555670f:/# exit
exit
```

4. Remove the container:

```bash
➜  cybertron-openjdk git:(master) ✗ docker ps -a
CONTAINER ID   IMAGE                                                      COMMAND       CREATED         STATUS                     PORTS     NAMES
56c8f555670f   sky-identity-docker-local.jfrog.io/cybertron-openjdk:131   "/bin/bash"   2 minutes ago   Exited (0) 3 seconds ago             interesting_borg
➜  cybertron-openjdk git:(master) ✗ docker rm 56c8f555670f
56c8f555670f
```

5. Build the image

The script https://github.com/sky-uk/id-docker/blob/master/.circleci/build.sh has the following code to build the image:

```bash
docker build -t $ARTIFACTORY_REGISTRY/cybertron-openjdk:$1 cybertron-openjdk/
```

So replace the placeholders, and build from the `id-docker` repo root:

```bash
docker build -t sky-identity-docker-local.jfrog.io/cybertron-openjdk:131 cybertron-openjdk/
```

6. Tidy your local images if necessary (remove anything from before your build, e.g. the image from 8 months ago below):

```bash
➜  id-docker git:(master) ✗ docker images | grep jdk
sky-identity-docker-local.jfrog.io/cybertron-openjdk                                       131            aec8182917f8   8 minutes ago   243MB
sky-identity-docker-local.jfrog.io/cybertron-openjdk                                       <none>         3ddd9d78cb19   8 months ago    274MB
➜  id-docker git:(master) ✗ docker image rm 3ddd9d78cb19
Untagged: sky-identity-docker-local.jfrog.io/cybertron-openjdk@sha256:b81cb0c130278cf9c59ffab7e44bac505f9e7ab41a731d6720035c9b42eeef58
Deleted: sha256:3ddd9d78cb196da2a6da8d1caad8d0668f0b899a803b4742628f4722cf10306a
```

7. Verify ARM64 (aarch64)

```bash
➜  id-docker git:(master) ✗ docker run -it sky-identity-docker-local.jfrog.io/cybertron-openjdk:131 /bin/bash          
root@12b005d2cebc:/# uname -a
Linux 12b005d2cebc 5.10.47-linuxkit #1 SMP PREEMPT Sat Jul 3 21:50:16 UTC 2021 aarch64 GNU/Linux
```

### Results

When you use `docker-compose` to start the containers, you should see that the logging starts MUCH more quikly when using the new ARM image, rather than the x86 image.

### Try hitting cybertron

Try adding a profile.  Get an expected error!  🙂

```bash
➜  local git:(PAUL_ENV_SECRETS) ✗ curl --location --request POST 'http://localhost:9992/profiles' \
--header 'User-Agent: id-gauge/1.0.0' \
--header 'Authorization: Basic Y2xpZW50OnBhc3N3b3Jk' \
--header 'X-SkyOTT-Provider: SKYSHOWTIME' \
--header 'Accept: application/vnd.bridge.v1+json' \
--header 'X-SkyOTT-Territory: CZ' \
--header 'X-SkyOTT-Proposition: SKYSHOWTIME' \
--header 'Content-Type: application/vnd.bridge.v1+json' \
--header 'X-SkyInt-RequestId: bentest' \
--data-raw '{"profile":{"id":{"aliases":{"email":{"alias":"skyshowtime_testCZ@oogway.oog","verified":true}}},"authentication":{"password":"test1234"},"details":{"title":"Mr","firstname":"Rico","lastname":"Koepp"},"marketing":{"skyoptin":true,"targetedoptin":true},"registration":{"termsandconditionsaccepted":true},"securityoptions":{},"optoutpreferences":{},"home":{"territory":"PT"}},"clientinfo":{"servicename":"skyshowtime-pt","contexturl":"http://localhost"}}'
{"message":"Create User failed due to login alias already in use.","service":"/skyshowtime-ptcreateprofileservicecontainer$","errorcode":302,"errorlist":["profile.details.contact.email Email address must be unique","profile.id.aliases Alias of type [email] - Email address must be unique"]}
```

### Error?

Something pointing to local Couchbase?  Couldn't fix so just commented out the `checkhashedemail-internationaloogway` container from the `docker-compose.yml` file:

```bash
cybertron-checkhashedemail-internationaloogway-1           | Exception in thread "main" java.lang.ExceptionInInitializerError
cybertron-checkhashedemail-internationaloogway-1           |    at com.sky.CheckPartnerServicesContainer.main(CheckPartnerServices.scala)
cybertron-checkhashedemail-internationaloogway-1           | Caused by: com.couchbase.client.core.config.ConfigurationException: Could not open bucket.
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.core.config.DefaultConfigurationProvider$6.call(DefaultConfigurationProvider.java:297)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.core.config.DefaultConfigurationProvider$6.call(DefaultConfigurationProvider.java:294)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OperatorOnErrorResumeNextViaFunction$4.onError(OperatorOnErrorResumeNextViaFunction.java:140)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OnSubscribeDoOnEach$DoOnEachSubscriber.onError(OnSubscribeDoOnEach.java:87)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OnSubscribeDoOnEach$DoOnEachSubscriber.onError(OnSubscribeDoOnEach.java:87)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OnSubscribeMap$MapSubscriber.onError(OnSubscribeMap.java:88)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OnSubscribeDoOnEach$DoOnEachSubscriber.onError(OnSubscribeDoOnEach.java:87)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OperatorTake$1.onError(OperatorTake.java:66)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OperatorMerge$MergeSubscriber.reportError(OperatorMerge.java:266)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OperatorMerge$MergeSubscriber.emitLoop(OperatorMerge.java:658)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OperatorMerge$MergeSubscriber.emit(OperatorMerge.java:568)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OperatorMerge$InnerSubscriber.onError(OperatorMerge.java:855)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OperatorOnErrorResumeNextViaFunction$4$1.onError(OperatorOnErrorResumeNextViaFunction.java:122)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OnSubscribeMap$MapSubscriber.onError(OnSubscribeMap.java:88)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OperatorMerge$MergeSubscriber.reportError(OperatorMerge.java:266)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OperatorMerge$MergeSubscriber.checkTerminate(OperatorMerge.java:818)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OperatorMerge$MergeSubscriber.emitLoop(OperatorMerge.java:579)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OperatorMerge$MergeSubscriber.emit(OperatorMerge.java:568)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OperatorMerge$InnerSubscriber.onError(OperatorMerge.java:855)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OnSubscribeMap$MapSubscriber.onError(OnSubscribeMap.java:88)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OperatorMerge$MergeSubscriber.reportError(OperatorMerge.java:266)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OperatorMerge$MergeSubscriber.checkTerminate(OperatorMerge.java:818)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OperatorMerge$MergeSubscriber.emitLoop(OperatorMerge.java:579)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OperatorMerge$MergeSubscriber.emit(OperatorMerge.java:568)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OperatorMerge$MergeSubscriber.onError(OperatorMerge.java:276)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.OnSubscribeMap$MapSubscriber.onError(OnSubscribeMap.java:88)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.subjects.SubjectSubscriptionManager$SubjectObserver.onError(SubjectSubscriptionManager.java:227)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.subjects.AsyncSubject.onError(AsyncSubject.java:116)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.core.service.AbstractOnDemandService$1.onError(AbstractOnDemandService.java:83)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.observers.SafeSubscriber._onError(SafeSubscriber.java:153)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.observers.SafeSubscriber.onError(SafeSubscriber.java:115)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.subjects.SubjectSubscriptionManager$SubjectObserver.onError(SubjectSubscriptionManager.java:227)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.subjects.AsyncSubject.onError(AsyncSubject.java:116)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.core.endpoint.AbstractEndpoint$2.onSuccess(AbstractEndpoint.java:463)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.core.endpoint.AbstractEndpoint$2.onSuccess(AbstractEndpoint.java:414)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.SingleOperatorOnErrorResumeNext$2.onSuccess(SingleOperatorOnErrorResumeNext.java:63)
cybertron-checkhashedemail-internationaloogway-1           |    at rx.internal.operators.SingleTimeout$TimeoutSingleSubscriber.onSuccess(SingleTimeout.java:79)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.core.endpoint.AbstractEndpoint$4$1.operationComplete(AbstractEndpoint.java:391)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.core.endpoint.AbstractEndpoint$4$1.operationComplete(AbstractEndpoint.java:375)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.deps.io.netty.util.concurrent.DefaultPromise.notifyListener0(DefaultPromise.java:512)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.deps.io.netty.util.concurrent.DefaultPromise.notifyListeners0(DefaultPromise.java:505)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.deps.io.netty.util.concurrent.DefaultPromise.notifyListenersNow(DefaultPromise.java:484)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.deps.io.netty.util.concurrent.DefaultPromise.notifyListeners(DefaultPromise.java:425)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.deps.io.netty.util.concurrent.DefaultPromise.tryFailure(DefaultPromise.java:122)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.deps.io.netty.channel.nio.AbstractNioChannel$AbstractNioUnsafe.fulfillConnectPromise(AbstractNioChannel.java:278)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.deps.io.netty.channel.nio.AbstractNioChannel$AbstractNioUnsafe.finishConnect(AbstractNioChannel.java:294)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.deps.io.netty.channel.nio.NioEventLoop.processSelectedKey(NioEventLoop.java:634)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.deps.io.netty.channel.nio.NioEventLoop.processSelectedKeysPlain(NioEventLoop.java:546)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.deps.io.netty.channel.nio.NioEventLoop.processSelectedKeys(NioEventLoop.java:500)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.deps.io.netty.channel.nio.NioEventLoop.run(NioEventLoop.java:460)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.deps.io.netty.util.concurrent.SingleThreadEventExecutor$2.run(SingleThreadEventExecutor.java:131)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.deps.io.netty.util.concurrent.FastThreadLocalRunnable.run(FastThreadLocalRunnable.java:30)
cybertron-checkhashedemail-internationaloogway-1           |    at java.base/java.lang.Thread.run(Thread.java:834)
cybertron-checkhashedemail-internationaloogway-1           | Caused by: com.couchbase.client.deps.io.netty.channel.AbstractChannel$AnnotatedConnectException: Connection refused: /127.0.0.1:8091{"level":"WARN","level_value":30000,"@version":1,"message":"[127.0.0.1:8091][ConfigEndpoint]: Could not connect to remote socket: Connection refused: /127.0.0.1:8091","logger_name":"com.couchbase.client.core.endpoint.Endpoint","thread_name":"cb-io-1-4"}
cybertron-checkhashedemail-internationaloogway-1           | {"level":"WARN","level_value":30000,"@version":1,"message":"Error during reconnect: com.couchbase.client.deps.io.netty.channel.AbstractChannel$AnnotatedConnectException: Connection refused: /127.0.0.1:8091","logger_name":"com.couchbase.client.core.endpoint.Endpoint","thread_name":"cb-io-1-4"}
cybertron-checkhashedemail-internationaloogway-1           | 
cybertron-checkhashedemail-internationaloogway-1           |    at java.base/sun.nio.ch.SocketChannelImpl.checkConnect(Native Method)
cybertron-checkhashedemail-internationaloogway-1           |    at java.base/sun.nio.ch.SocketChannelImpl.finishConnect(SocketChannelImpl.java:779)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.deps.io.netty.channel.socket.nio.NioSocketChannel.doFinishConnect(NioSocketChannel.java:225)
cybertron-checkhashedemail-internationaloogway-1           |    at com.couchbase.client.deps.io.netty.channel.nio.AbstractNioChannel$AbstractNioUnsafe.finishConnect(AbstractNioChannel.java:291)
cybertron-checkhashedemail-internationaloogway-1           |    ... 7 more
cybertron-checkhashedemail-internationaloogway-1           | Caused by: java.net.ConnectException: Connection refused
cybertron-checkhashedemail-internationaloogway-1           |    ... 11 more
```
