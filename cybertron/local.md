Local Cybertron development.

The process is

- sbt `compile`
- sbt `docker:publishLocal`
- docker-compose down
- docker-compose up

## `compile`

```
sbt:cybertron> compile
[info] Generating scrooge thrift for  ...

[info] Compiling 1 Scala source to /Users/PGE13/work/id-cybertron/profilemanagement/gateway/target/scala-2.12/classes ...
[success] Total time: 75 s (01:15), completed 5 Jan 2022, 09:25:34
```

## `docker:publishLocal`

```
sbt:getprofilegateway> docker:publishLocal
[info] Wrote /Users/PGE13/work/id-cybertron/getprofile/gateway/target/scala-2.12/getprofilegateway_2.12-0.1.0.pom
[info] Generating scrooge thrift for  ...
[info] Wrote /Users/PGE13/work/id-cybertron/thrift/target/scala-2.12/thrift_2.12-0.1.0.pom
[info] Wrote /Users/PGE13/work/id-cybertron/core/target/scala-2.12/core_2.12-0.1.0.pom
[info] Wrote /Users/PGE13/work/id-cybertron/getprofile/util/target/scala-2.12/getprofile-util_2.12-0.1.0.pom
[error] #1 [internal] load build definition from Dockerfile
[error] #1 sha256:bfabc015d858892a56237a0ddf71ab43eee67b00ef474ead8ee5efd33ea555a4
[error] #1 transferring dockerfile: 255B done
[error] #1 DONE 0.0s
[error] #2 [internal] load .dockerignore
[error] #2 sha256:a4b091c0f7046c2ad8af21a566ee0095c823f4853d91eebaf24e404cc8ab8b05
[error] #2 transferring context: 2B done
[error] #2 DONE 0.0s
[error] #3 [internal] load metadata for sky-identity-docker-local.jfrog.io/cybertron-openjdk:131
[error] #3 sha256:6cd0cb5e5a9d7c9b3143d654d91f62be28bf2993ae5aee18eb984db297f3a59c
[error] #3 DONE 0.0s
[error] #4 [1/3] FROM sky-identity-docker-local.jfrog.io/cybertron-openjdk:131
[error] #4 sha256:ec8a7a22d121d2b1d6359333bc0e49121a1f43058f6d08245f73046ffb0ed64e
[error] #4 DONE 0.0s
[error] #6 [internal] load build context
[error] #6 sha256:f4e19bb3a89dd85a47da2202ea212e6b3eb59ccccf01487518966f08c0a4289f
[error] #6 transferring context: 282.71kB 0.0s done
[error] #6 DONE 0.0s
[error] #5 [2/3] WORKDIR /opt/docker
[error] #5 sha256:3a75d55c4579d107e69548866c2515de030bc281f60c95a71bbb4c369553cf89
[error] #5 CACHED
[error] #7 [3/3] ADD --chown=daemon:daemon opt /opt
[error] #7 sha256:33576bfc2ab60d5d6b9408c2117569dff1de3a1201b816453221132b40b4d765
[error] #7 DONE 1.4s
[error] #8 exporting to image
[error] #8 sha256:e8c613e07b0b7ff33893b694f7759a10d42e180f2b4dc349fb57dc6b71dcab00
[error] #8 exporting layers
[error] #8 exporting layers 0.3s done
[error] #8 writing image sha256:2a948c6d411b9e5a628a9bcfc15c99705d715b17705be58457b12e05d98d3b5a done
[error] #8 naming to sky-identity-docker-local.jfrog.io/cybertron/getprofilegateway:1641374648 done
[error] #8 naming to sky-identity-docker-local.jfrog.io/cybertron/getprofilegateway:latest done
[error] #8 DONE 0.3s
[error] Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
[info] Built image sky-identity-docker-local.jfrog.io/cybertron/getprofilegateway with tags [1641374648, latest]
[success] Total time: 13 s, completed 5 Jan 2022, 10:28:26
sbt:getprofilegateway> 
```

# Timeouts

Sometimes if you're debugging there will be timeouts, e.g.:

```
local git:(IPB-15148) ✗ curl --location --request GET 'http://localhost:9989/profile ' \
--header 'Accept: application/vnd.aggregator.v3+json' \
--header 'X-SkyOTT-Provider: SKYSHOWTIME' \
--header 'X-SkyOTT-Proposition: SKYSHOWTIME' \
--header 'X-SkyOTT-Territory: PL' \
--header 'X-Identifier: NsProfileId 40cb391a-c91d-4258-9883-b88f4dc533a6' \
--header 'Authorization: Basic Y2xpZW50OnBhc3N3b3Jk'
{"message":"Internal service timeout","service":"getprofilegatewaycontainer$"}
```

So increase the timeouts...

Set in `local/.env`:

`DOWNSTREAM_REQUEST_TIMEOUT_IN_MILLIS=60000`

And refer in `docker-compose.yml`

```yml
    getprofilegateway:
      image: sky-identity-docker-local.jfrog.io/cybertron/getprofilegateway:latest
      environment:
...
          DOWNSTREAM_REQUEST_TIMEOUT_IN_MILLIS: ${DOWNSTREAM_REQUEST_TIMEOUT_IN_MILLIS}
```
