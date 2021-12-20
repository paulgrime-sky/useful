# IPB-15149 tests

This page records the errors we had with the IPB-15149 PR.

The summary is that there seems to be some concurrency issues with some of the tests, and that adding the `@singlenode` tag to the feature(s) can fix.

Asking Nick Gomm about the tests...

> ... not seen that test behave in a flaky manner before.  Time is a key factor in that test so if there is a clock discrepancy on the different wap nodes that could account for the flakiness.  Although I can’t see a big difference.  Might be worth annotating the test as @singlenode which forces it to send all requests to the same node.  The reason the time could cause flakiness is that when we revoke all tokens it doesn’t have a way of finding all the session documents in the database to delete,  so it creates a Revoke document with a timestamp.  When you then try to use a session token it checks the time the session was issued against the revoke time.  Since these are all running in quick succession a few millis out could make all the difference.

and

> I know we have some tests around messages that can be flaky sometimes. (Often down to people doing things directly on the fun environment which creates additional messages it isn’t expecting).

## Build 14 - Original error

This is the error we saw most of the time after making the changes.
There is no obvious reason why the changes made for `IPB-15149` should cause these errors.
These errors were repeated on several attempted re-builds, so they weren't random, in the sense that they were sort of consistently happening with each re-build.

```bash
Failed scenarios:
Management/Tokens/revoketokens.feature:7 # Scenario: CRM team should be able to revoke all tokens for a profile

1625 Scenarios (1 failed, 1624 passed)
8066 Steps (1 failed, 4 skipped, 8061 passed)
12m8.776s

java.lang.AssertionError: Expected Response code [200] but got [404] exception: OogwayException: Not Found
Expected: is <200>
     but: was <404>
	at org.hamcrest.MatcherAssert.assertThat(MatcherAssert.java:20)
	at org.junit.Assert.assertThat(Assert.java:964)
	at utils.Asserts.assertResponseCode(Asserts.java:23)
	at stepdefs.support.ReusableSteps.the_response_code_is(ReusableSteps.java:37)
	at ✽.Then the response code should be 200 OK(Management/Tokens/revoketokens.feature:17)

[ERROR] Tests run: 9691, Failures: 2, Errors: 0, Skipped: 4, Time elapsed: 745.196 s <<< FAILURE! - in RunFunctionalTest
[ERROR] Then the response code should be 200 OK(Scenario: CRM team should be able to revoke all tokens for a profile)  Time elapsed: 0.011 s  <<< FAILURE!
java.lang.AssertionError: 
Expected Response code [200] but got [404] exception: OogwayException: Not Found
Expected: is <200>
     but: was <404>

[ERROR] Scenario: CRM team should be able to revoke all tokens for a profile  Time elapsed: 0.011 s  <<< FAILURE!
java.lang.AssertionError: 
Expected Response code [200] but got [404] exception: OogwayException: Not Found
Expected: is <200>
     but: was <404>

[INFO] 
[INFO] Results:
[INFO] 
[ERROR] Failures: 
[ERROR]   Expected Response code [200] but got [404] exception: OogwayException: Not Found
Expected: is <200>
     but: was <404>
[ERROR] Scenario: CRM team should be able to revoke all tokens for a profile.Then the response code should be 200 OK(Scenario: CRM team should be able to revoke all tokens for a profile)
[ERROR]   Run 1: Expected Response code [200] but got [404] exception: OogwayException: Not Found
Expected: is <200>
     but: was <404>
[INFO]   Run 2: PASS
[INFO] 
[INFO] 
[ERROR] Tests run: 9690, Failures: 2, Errors: 0, Skipped: 3
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 12:43 min
[INFO] Finished at: 2021-12-14T09:45:06Z
[INFO] Final Memory: 55M/963M
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-surefire-plugin:3.0.0-M3:test (default-test) on project component-test: There are test failures.
[ERROR] 
[ERROR] Please refer to /var/sky/build/jobs/oogway/branches/IPB-15149/workspace/component-test/target/surefire-reports for the individual test results.
[ERROR] Please refer to dump files (if any exist) [date].dump, [date]-jvmRun[N].dump and [date].dumpstream.
[ERROR] -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoFailureException
```

[Full log here](./14.log).

## Build 15 - Unknown change (no change?)

Not sure what changes were made for the errors in build 15, but here they are:

```bash
Failed scenarios:
Sessions/SignIn/SignInWithContext.feature:28 # Scenario Outline: Successful V4 SignIn with empty partner branding Id writes events without context
Sessions/SignIn/SignInWithContext.feature:29 # Scenario Outline: Successful V4 SignIn with empty partner branding Id writes events without context
Sessions/SignIn/SignInWithContext.feature:40 # Scenario Outline: Successful V2 V3 SignIn with partner branding Id writes events without context
Sessions/SignIn/SignInWithContext.feature:41 # Scenario Outline: Successful V2 V3 SignIn with partner branding Id writes events without context

1625 Scenarios (4 failed, 1621 passed)
8066 Steps (8066 passed)
12m2.810s

java.lang.AssertionError: Oh dear, message still on the queue: {"id":"c31d6445-1183-48db-87d5-cd09367192cf","timestamp":1639498225077,"provider":"SKY","territory":"GB","profileid":"99536370","event":"PROFILE_CREATED","data":{"profile":{"username":"GAUGE823105.1639498224245","title":"Miss","firstname":"Shavonda","lastname":"Adams","displayname":null,"contactemail":"gauge823105.1639498224245ce@oogway.oog","skyoptin":true,"globalbookmarkingoptout":null,"personalisedrecsoptout":null,"targetedoptin":true,"targetedoptindate":1639498225062,"targetedoptoutdate":null,"hhusertype":"guest","email":"gauge823105.1639498224245@oogway.oog","termsandconditionsaccepted":true,"registrationdate":1639498225062,"mobilenumber":null,"trackingid":"eO3ijJy9jcCHvxuEVHZ79MKwJMrNdFgYPz14qRiI5mA=","nsprofileid":"3ca5d68d-80f4-45f7-b96d-81f95ec5e08b","profileid":"99536370","emailverified":false,"mobilenumberverified":null,"hhid":null,"hhuserauthorised":null,"dateofbirth":null,"mobilenumberchanged":null,"emailchanged":1639498225062,"passwordrequireschange":false,"services":{},"securityquestionid":null,"journey":null,"inviternsprofileid":null,"euportability":true},"servicename":"atlantic","obfuscatedids":{"qubit":{"profileid":"53e4fd16c2571eef8b6750d416ff8669a9ae84c0","partyid":"53e4fd16c2571eef8b6750d416ff8669a9ae84c0"},"internal_userid":{"profileid":"c89341a61d5bd0dfc83708568954372dad8d67b2","partyid":"c89341a61d5bd0dfc83708568954372dad8d67b2"},"ad_smart":{"profileid":"e0326e36d92a00355c4d3cbb5b07ccb8bb9c8f0a","partyid":"e0326e36d92a00355c4d3cbb5b07ccb8bb9c8f0a"},"mparticle":{"profileid":"b5246455c1c1363c376833119105bc7ebd620566","partyid":"b5246455c1c1363c376833119105bc7ebd620566"},"salesforce":{"profileid":"54ef6b7b4c100921b71748d128824802d1c82138","partyid":"54ef6b7b4c100921b71748d128824802d1c82138"},"omniture":{"profileid":"7ef644362db8a27e703bb43174d55cdc9f3ff60b","partyid":"7ef644362db8a27e703bb43174d55cdc9f3ff60b"},"digitaladoptiontool":{"profileid":"5b1e251ede31cb8cd3b79ee3164098f806393c4c","partyid":"5b1e251ede31cb8cd3b79ee3164098f806393c4c"},"conviva":{"profileid":"37a0cdb8129753443dbb09642b9c91c5363f1e17","partyid":"37a0cdb8129753443dbb09642b9c91c5363f1e17"},"freewheel":{"profileid":"69ab0ca8c862666995e435ecc9817f5318512f70","partyid":"69ab0ca8c862666995e435ecc9817f5318512f70"},"adobe_dmp":{"profileid":"a04b9e631c2dcc97655cd72cf0b70d453813cf7d","partyid":"a04b9e631c2dcc97655cd72cf0b70d453813cf7d"},"oculus_vr":{"profileid":"8f1d20e5c95f900923d6439d233253d6f2a93a8c","partyid":"8f1d20e5c95f900923d6439d233253d6f2a93a8c"},"comscore":{"profileid":"44381d3aab48ccabfcbf6e5b512b3f46adcfb4de","partyid":"44381d3aab48ccabfcbf6e5b512b3f46adcfb4de"},"adobe_analytics":{"profileid":"ba5767f768745049707160dbc67bc83bb91046cc","partyid":"ba5767f768745049707160dbc67bc83bb91046cc"},"urban_airship":{"profileid":"5927693cd1f3f423856bd0665f5c1807ecf35b23","partyid":"5927693cd1f3f423856bd0665f5c1807ecf35b23"},"tax_jar":{"profileid":"7caca64dae1388bde1a02cab4163d03ba7bd1adb","partyid":"7caca64dae1388bde1a02cab4163d03ba7bd1adb"},"barclaycard":{"profileid":"8ecdb1ee86e5c99ad1500d23ed713c591e3ba406","partyid":"8ecdb1ee86e5c99ad1500d23ed713c591e3ba406"},"advertisingid":{"profileid":"69ab0ca8c862666995e435ecc9817f5318512f70","partyid":"69ab0ca8c862666995e435ecc9817f5318512f70"},"experian":{"profileid":"d90e177839a653f5a1c356960133f399ef3e4951","partyid":"d90e177839a653f5a1c356960133f399ef3e4951"},"firebase":{"profileid":"180d212762a5ca08867e3e00697370e6d40481ae","partyid":"180d212762a5ca08867e3e00697370e6d40481ae"},"adobe_campaign_manager":{"profileid":"ad3686ced2e8bad50f6a592e310b0a795d5d6ed9","partyid":"ad3686ced2e8bad50f6a592e310b0a795d5d6ed9"},"skoonie_data_hub":{"profileid":"20642fda8397b5ff74d56ac6bfad6a1ac732a3a3","partyid":"20642fda8397b5ff74d56ac6bfad6a1ac732a3a3"},"yospace":{"profileid":"69ab0ca8c862666995e435ecc9817f5318512f70","partyid":"69ab0ca8c862666995e435ecc9817f5318512f70"},"appdynamics":{"profileid":"e20add1ff94b2fd4cae1d0a7daa24a604b651f3f","partyid":"e20add1ff94b2fd4cae1d0a7daa24a604b651f3f"},"nfl":{"profileid":"08e1c9af72439fa4a37f8297026d4c2ede65371d","partyid":"08e1c9af72439fa4a37f8297026d4c2ede65371d"},"ad_profiler":{"profileid":"093fab7f509e98898f0ca6356bc360b23ce079d6","partyid":"093fab7f509e98898f0ca6356bc360b23ce079d6"},"fluxcapacitor":{"profileid":"b36a2c479a2c30901e261b57c4f15d3926d00591","partyid":"b36a2c479a2c30901e261b57c4f15d3926d00591"},"trackingid":{"profileid":"eO3ijJy9jcCHvxuEVHZ79MKwJMrNdFgYPz14qRiI5mA=","partyid":"eO3ijJy9jcCHvxuEVHZ79MKwJMrNdFgYPz14qRiI5mA="}},"operatorreference":null,"singleusetoken":"gauge823105.1639498224245@oogway.oogAAAAAAAAAAAAAAAAAAAAAAAAAAAA","contexturl":null,"context":null},"sendEmail":true}
	at org.junit.Assert.fail(Assert.java:89)
	at org.junit.Assert.assertTrue(Assert.java:42)
	at stepdefs.events.EventSteps.cleanup(EventSteps.java:31)
	at sun.reflect.GeneratedMethodAccessor193.invoke(Unknown Source)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at cucumber.runtime.Utils$1.call(Utils.java:40)
	at cucumber.runtime.Timeout.timeout(Timeout.java:16)
	at cucumber.runtime.Utils.invoke(Utils.java:34)
	at cucumber.runtime.java.JavaHookDefinition.execute(JavaHookDefinition.java:60)
	at cucumber.runtime.Runtime.runHookIfTagsMatch(Runtime.java:224)
	at cucumber.runtime.Runtime.runHooks(Runtime.java:212)
	at cucumber.runtime.Runtime.runAfterHooks(Runtime.java:206)
	at cucumber.runtime.model.CucumberScenario.run(CucumberScenario.java:46)
	at cucumber.runtime.junit.ExecutionUnitRunner.run(ExecutionUnitRunner.java:102)
	at org.junit.runners.Suite.runChild(Suite.java:128)
	at org.junit.runners.Suite.runChild(Suite.java:27)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.runtime.junit.ExamplesRunner.run(ExamplesRunner.java:59)
	at org.junit.runners.Suite.runChild(Suite.java:128)
	at org.junit.runners.Suite.runChild(Suite.java:27)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.runtime.junit.ScenarioOutlineRunner.run(ScenarioOutlineRunner.java:53)
	at cucumber.runtime.junit.FeatureRunner.runChild(FeatureRunner.java:63)
	at cucumber.runtime.junit.FeatureRunner.runChild(FeatureRunner.java:18)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.runtime.junit.FeatureRunner.run(FeatureRunner.java:70)
	at cucumber.api.junit.Cucumber.runChild(Cucumber.java:95)
	at cucumber.api.junit.Cucumber.runChild(Cucumber.java:38)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.api.junit.Cucumber.run(Cucumber.java:100)
	at org.apache.maven.surefire.junit4.JUnit4Provider.execute(JUnit4Provider.java:365)
	at org.apache.maven.surefire.junit4.JUnit4Provider.executeWithRerun(JUnit4Provider.java:273)
	at org.apache.maven.surefire.junit4.JUnit4Provider.executeTestSet(JUnit4Provider.java:238)
	at org.apache.maven.surefire.junit4.JUnit4Provider.invoke(JUnit4Provider.java:159)
	at org.apache.maven.surefire.booter.ForkedBooter.invokeProviderInSameClassLoader(ForkedBooter.java:384)
	at org.apache.maven.surefire.booter.ForkedBooter.runSuitesInProcess(ForkedBooter.java:345)
	at org.apache.maven.surefire.booter.ForkedBooter.execute(ForkedBooter.java:126)
	at org.apache.maven.surefire.booter.ForkedBooter.main(ForkedBooter.java:418)

java.lang.AssertionError: Oh dear, message still on the queue: {"id":"75bcae19-d1c8-465b-813a-2db3daf73a19","timestamp":1639498226562,"provider":"SKY","territory":"GB","profileid":"99536372","event":"PROFILE_CREATED","data":{"profile":{"username":"GAUGE785294.1639498226309","title":"Ms","firstname":"Nanci","lastname":"Erdman","displayname":null,"contactemail":"gauge785294.1639498226309ce@oogway.oog","skyoptin":true,"globalbookmarkingoptout":null,"personalisedrecsoptout":null,"targetedoptin":true,"targetedoptindate":1639498226545,"targetedoptoutdate":null,"hhusertype":"guest","email":"gauge785294.1639498226309@oogway.oog","termsandconditionsaccepted":true,"registrationdate":1639498226545,"mobilenumber":null,"trackingid":"UTyjgD4Qtr0v2xR4J98QKogKoNUARTf9uIPrKL+4QV4=","nsprofileid":"5f5ed309-d02f-4582-ab4c-7f10d14619f3","profileid":"99536372","emailverified":false,"mobilenumberverified":null,"hhid":null,"hhuserauthorised":null,"dateofbirth":null,"mobilenumberchanged":null,"emailchanged":1639498226545,"passwordrequireschange":false,"services":{},"securityquestionid":null,"journey":null,"inviternsprofileid":null,"euportability":true},"servicename":"atlantic","obfuscatedids":{"qubit":{"profileid":"01978957390f26d30270d7498238bed930176d9f","partyid":"01978957390f26d30270d7498238bed930176d9f"},"internal_userid":{"profileid":"5fd906678a92a589bc1d91147b2bdce16aa5374f","partyid":"5fd906678a92a589bc1d91147b2bdce16aa5374f"},"ad_smart":{"profileid":"f895e8d569b952e62a3aca49026808be795ba80b","partyid":"f895e8d569b952e62a3aca49026808be795ba80b"},"mparticle":{"profileid":"cfba480d8e6f4eaf03019c9da3ad3cdc26dddd98","partyid":"cfba480d8e6f4eaf03019c9da3ad3cdc26dddd98"},"salesforce":{"profileid":"52a79639248f678ad3cb39eff41bdcc7e43cbda7","partyid":"52a79639248f678ad3cb39eff41bdcc7e43cbda7"},"omniture":{"profileid":"987510035926dd99441bb688b96673f60fdcd833","partyid":"987510035926dd99441bb688b96673f60fdcd833"},"digitaladoptiontool":{"profileid":"6bf736c0433a676fa0c3e876888d108924523c6c","partyid":"6bf736c0433a676fa0c3e876888d108924523c6c"},"conviva":{"profileid":"ea9f1ea2f20c66ab6e7ebae37d84679e2cc6863f","partyid":"ea9f1ea2f20c66ab6e7ebae37d84679e2cc6863f"},"freewheel":{"profileid":"c13b201e912f32788c8fefd10bf0280d41636660","partyid":"c13b201e912f32788c8fefd10bf0280d41636660"},"adobe_dmp":{"profileid":"509dd4ea6fd83193b8383f494a9fbb98f65927f4","partyid":"509dd4ea6fd83193b8383f494a9fbb98f65927f4"},"oculus_vr":{"profileid":"8240b68a80a0ff232f790b31272912e3704e4843","partyid":"8240b68a80a0ff232f790b31272912e3704e4843"},"comscore":{"profileid":"0a4997b4d9ff370d12beec69afe76a657b283576","partyid":"0a4997b4d9ff370d12beec69afe76a657b283576"},"adobe_analytics":{"profileid":"76e602046a1ac30f6a3779eba2b35fcde5eb52ee","partyid":"76e602046a1ac30f6a3779eba2b35fcde5eb52ee"},"urban_airship":{"profileid":"9c840e8edda6484196c56aa343d17c57e07c3712","partyid":"9c840e8edda6484196c56aa343d17c57e07c3712"},"tax_jar":{"profileid":"e070f5db31dbaa90bc992ddbfb481cdb8ff8dcf7","partyid":"e070f5db31dbaa90bc992ddbfb481cdb8ff8dcf7"},"barclaycard":{"profileid":"440cab76ed5b4ce3709878c2ed130d6e412b46ea","partyid":"440cab76ed5b4ce3709878c2ed130d6e412b46ea"},"advertisingid":{"profileid":"c13b201e912f32788c8fefd10bf0280d41636660","partyid":"c13b201e912f32788c8fefd10bf0280d41636660"},"experian":{"profileid":"3ae47aebd28ad24d1a66950bfb079a097b714fe2","partyid":"3ae47aebd28ad24d1a66950bfb079a097b714fe2"},"firebase":{"profileid":"d19db34b39b6a688d81822a73e1dd362d2d6e6e2","partyid":"d19db34b39b6a688d81822a73e1dd362d2d6e6e2"},"adobe_campaign_manager":{"profileid":"ec72f6c88dd94ef06d2c0d84714fcbbbd595290a","partyid":"ec72f6c88dd94ef06d2c0d84714fcbbbd595290a"},"skoonie_data_hub":{"profileid":"e0f3bc5a0a61c3ed8ac9919f26720a7b85f814f0","partyid":"e0f3bc5a0a61c3ed8ac9919f26720a7b85f814f0"},"yospace":{"profileid":"c13b201e912f32788c8fefd10bf0280d41636660","partyid":"c13b201e912f32788c8fefd10bf0280d41636660"},"appdynamics":{"profileid":"6d2c1b864000f09da44a97b1abf2b6aa6ea939a6","partyid":"6d2c1b864000f09da44a97b1abf2b6aa6ea939a6"},"nfl":{"profileid":"138195efc28373d87bac3679eb2ddcd77c967a31","partyid":"138195efc28373d87bac3679eb2ddcd77c967a31"},"ad_profiler":{"profileid":"b6b93021e2692b8e4ff23edbf64c5c6289e3984d","partyid":"b6b93021e2692b8e4ff23edbf64c5c6289e3984d"},"fluxcapacitor":{"profileid":"fe582f6ee2fc6b0208b1fad34d844d8e2147d681","partyid":"fe582f6ee2fc6b0208b1fad34d844d8e2147d681"},"trackingid":{"profileid":"UTyjgD4Qtr0v2xR4J98QKogKoNUARTf9uIPrKL+4QV4=","partyid":"UTyjgD4Qtr0v2xR4J98QKogKoNUARTf9uIPrKL+4QV4="}},"operatorreference":null,"singleusetoken":"gauge785294.1639498226309@oogway.oogAAAAAAAAAAAAAAAAAAAAAAAAAAAA","contexturl":null,"context":null},"sendEmail":true}
	at org.junit.Assert.fail(Assert.java:89)
	at org.junit.Assert.assertTrue(Assert.java:42)
	at stepdefs.events.EventSteps.cleanup(EventSteps.java:31)
	at sun.reflect.GeneratedMethodAccessor193.invoke(Unknown Source)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at cucumber.runtime.Utils$1.call(Utils.java:40)
	at cucumber.runtime.Timeout.timeout(Timeout.java:16)
	at cucumber.runtime.Utils.invoke(Utils.java:34)
	at cucumber.runtime.java.JavaHookDefinition.execute(JavaHookDefinition.java:60)
	at cucumber.runtime.Runtime.runHookIfTagsMatch(Runtime.java:224)
	at cucumber.runtime.Runtime.runHooks(Runtime.java:212)
	at cucumber.runtime.Runtime.runAfterHooks(Runtime.java:206)
	at cucumber.runtime.model.CucumberScenario.run(CucumberScenario.java:46)
	at cucumber.runtime.junit.ExecutionUnitRunner.run(ExecutionUnitRunner.java:102)
	at org.junit.runners.Suite.runChild(Suite.java:128)
	at org.junit.runners.Suite.runChild(Suite.java:27)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.runtime.junit.ExamplesRunner.run(ExamplesRunner.java:59)
	at org.junit.runners.Suite.runChild(Suite.java:128)
	at org.junit.runners.Suite.runChild(Suite.java:27)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.runtime.junit.ScenarioOutlineRunner.run(ScenarioOutlineRunner.java:53)
	at cucumber.runtime.junit.FeatureRunner.runChild(FeatureRunner.java:63)
	at cucumber.runtime.junit.FeatureRunner.runChild(FeatureRunner.java:18)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.runtime.junit.FeatureRunner.run(FeatureRunner.java:70)
	at cucumber.api.junit.Cucumber.runChild(Cucumber.java:95)
	at cucumber.api.junit.Cucumber.runChild(Cucumber.java:38)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.api.junit.Cucumber.run(Cucumber.java:100)
	at org.apache.maven.surefire.junit4.JUnit4Provider.execute(JUnit4Provider.java:365)
	at org.apache.maven.surefire.junit4.JUnit4Provider.executeWithRerun(JUnit4Provider.java:273)
	at org.apache.maven.surefire.junit4.JUnit4Provider.executeTestSet(JUnit4Provider.java:238)
	at org.apache.maven.surefire.junit4.JUnit4Provider.invoke(JUnit4Provider.java:159)
	at org.apache.maven.surefire.booter.ForkedBooter.invokeProviderInSameClassLoader(ForkedBooter.java:384)
	at org.apache.maven.surefire.booter.ForkedBooter.runSuitesInProcess(ForkedBooter.java:345)
	at org.apache.maven.surefire.booter.ForkedBooter.execute(ForkedBooter.java:126)
	at org.apache.maven.surefire.booter.ForkedBooter.main(ForkedBooter.java:418)

java.lang.AssertionError: Oh dear, message still on the queue: {"id":"436639da-b337-402e-a024-d3440cde3e84","timestamp":1639498228926,"provider":"SKY","territory":"GB","profileid":"9524099380067","event":"SIGNIN","data":{"username":"1639498228870IDZPTRQUVJYYRHGO","service":"atlantic","context":null},"sendEmail":true}
	at org.junit.Assert.fail(Assert.java:89)
	at org.junit.Assert.assertTrue(Assert.java:42)
	at stepdefs.events.EventSteps.cleanup(EventSteps.java:31)
	at sun.reflect.GeneratedMethodAccessor193.invoke(Unknown Source)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at cucumber.runtime.Utils$1.call(Utils.java:40)
	at cucumber.runtime.Timeout.timeout(Timeout.java:16)
	at cucumber.runtime.Utils.invoke(Utils.java:34)
	at cucumber.runtime.java.JavaHookDefinition.execute(JavaHookDefinition.java:60)
	at cucumber.runtime.Runtime.runHookIfTagsMatch(Runtime.java:224)
	at cucumber.runtime.Runtime.runHooks(Runtime.java:212)
	at cucumber.runtime.Runtime.runAfterHooks(Runtime.java:206)
	at cucumber.runtime.model.CucumberScenario.run(CucumberScenario.java:46)
	at cucumber.runtime.junit.ExecutionUnitRunner.run(ExecutionUnitRunner.java:102)
	at org.junit.runners.Suite.runChild(Suite.java:128)
	at org.junit.runners.Suite.runChild(Suite.java:27)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.runtime.junit.ExamplesRunner.run(ExamplesRunner.java:59)
	at org.junit.runners.Suite.runChild(Suite.java:128)
	at org.junit.runners.Suite.runChild(Suite.java:27)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.runtime.junit.ScenarioOutlineRunner.run(ScenarioOutlineRunner.java:53)
	at cucumber.runtime.junit.FeatureRunner.runChild(FeatureRunner.java:63)
	at cucumber.runtime.junit.FeatureRunner.runChild(FeatureRunner.java:18)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.runtime.junit.FeatureRunner.run(FeatureRunner.java:70)
	at cucumber.api.junit.Cucumber.runChild(Cucumber.java:95)
	at cucumber.api.junit.Cucumber.runChild(Cucumber.java:38)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.api.junit.Cucumber.run(Cucumber.java:100)
	at org.apache.maven.surefire.junit4.JUnit4Provider.execute(JUnit4Provider.java:365)
	at org.apache.maven.surefire.junit4.JUnit4Provider.executeWithRerun(JUnit4Provider.java:273)
	at org.apache.maven.surefire.junit4.JUnit4Provider.executeTestSet(JUnit4Provider.java:238)
	at org.apache.maven.surefire.junit4.JUnit4Provider.invoke(JUnit4Provider.java:159)
	at org.apache.maven.surefire.booter.ForkedBooter.invokeProviderInSameClassLoader(ForkedBooter.java:384)
	at org.apache.maven.surefire.booter.ForkedBooter.runSuitesInProcess(ForkedBooter.java:345)
	at org.apache.maven.surefire.booter.ForkedBooter.execute(ForkedBooter.java:126)
	at org.apache.maven.surefire.booter.ForkedBooter.main(ForkedBooter.java:418)

java.lang.AssertionError: Oh dear, message still on the queue: {"id":"9c4aa969-7831-4eb8-bedb-01a9ba99c7e3","timestamp":1639498231386,"provider":"SKY","territory":"GB","profileid":"99536377","event":"PROFILE_MODIFIED_PARTY_ID_AND_USER_TYPE","data":{"profile":{"username":"GAUGE099450.1639498231080","title":"Mr","firstname":"Cami","lastname":"Buckridge","displayname":null,"contactemail":"gauge099450.1639498231080ce@oogway.oog","skyoptin":true,"globalbookmarkingoptout":null,"personalisedrecsoptout":null,"targetedoptin":true,"targetedoptindate":1639498231293,"targetedoptoutdate":null,"hhusertype":"primary","email":"gauge099450.1639498231080@oogway.oog","termsandconditionsaccepted":true,"registrationdate":1639498231293,"mobilenumber":null,"trackingid":"swlnkBA+J7/U88BFs9rq80qjLA8i4ARQfRHDQo5i+gU=","nsprofileid":"39818d77-e029-4034-95f5-1ea385e5ab7d","profileid":"99536377","emailverified":false,"mobilenumberverified":null,"hhid":"2113462067","hhuserauthorised":null,"dateofbirth":null,"mobilenumberchanged":null,"emailchanged":1639498231293,"passwordrequireschange":false,"services":{},"securityquestionid":null,"journey":null,"inviternsprofileid":null,"euportability":true},"changes":["hhid","hhusertype","obfuscatedids"],"primaryprofile":null,"obfuscatedids":{"qubit":{"profileid":"a7ab02c534ae00914132e638fd32a9c49a81e546","partyid":"0ab8a509dd8bdc3144f6fd35a6c08860982c947c"},"internal_userid":{"profileid":"0454fd0713c28caed124843cc8d60e99509107a4","partyid":"62d546fcb7c822cc7ac406b647ff6112f9e9eafd"},"ad_smart":{"profileid":"a127a00c2865e0f14d09888268bd0dacf222e5cc","partyid":"5e552185d91db1c82230f3111519f5413121ab02"},"mparticle":{"profileid":"73225fe2686813e3db3eb59c31981fafb0be19f6","partyid":"d822d49f6201dc26634e8d5b75eaac724786c3f3"},"salesforce":{"profileid":"966bd0126c4342d9146f8421a33f54dee69cb271","partyid":"c1f356b83ca5b50efa9ba39ba015ed7c18e4143f"},"omniture":{"profileid":"87b5e559e75433472da4b75dc8a4bfd863edcf76","partyid":"b64c47c2c5814f5d1faa3d173f70eebed56cce84"},"digitaladoptiontool":{"profileid":"dc79d1f004c0d2e8de10c8044ca5f3c7f03cf4d3","partyid":"9e232106c02c34698baef344d57b2a2c30dfc69e"},"conviva":{"profileid":"7217735125479e4274e504b6476339448efae6b8","partyid":"b9204cc89b0e18aa5fc11d66b1d217f2235a8af8"},"freewheel":{"profileid":"f81ca846faa39248b16e61a2b5e0d0abb7a9fcb9","partyid":"8f06ec3caa0f8a20603ed8d432217f3612035c20"},"adobe_dmp":{"profileid":"5d46848d04bbe037dc9a8120fdf58b58f2a35772","partyid":"387e2953d1734a03991dde6f8651c5c3201ca3ce"},"oculus_vr":{"profileid":"bf931d0a6192d9c02e1daa3fafb5fea7439384a8","partyid":"535f5b06cfa96ad87c03c7ff483f98c24c5e5b4a"},"comscore":{"profileid":"5fd2a2f22723954af2e8bb3d188ba0ceb1678827","partyid":"c0011998a7989727c53eac1f0c03268f22db6379"},"adobe_analytics":{"profileid":"db9f20572e0fda355064976a8a2d13fa8cfea1ef","partyid":"7b2057d8653c4fa8b94b6daf446f809bcd55a97b"},"urban_airship":{"profileid":"eacaebd7c9680d144de8daeb84ed63b074228890","partyid":"3ff2cd0b2ac5b2674d8c65414b551da8748a5121"},"tax_jar":{"profileid":"51ac25418f81c29546bf84b98330b7ee0c1238b0","partyid":"2fa12479680a561f722d5df15cdd438a07a73f4b"},"barclaycard":{"profileid":"00b8c4392e0b82f43a22668ac0231b289fbeaf6a","partyid":"0e5502cfa72c3bddefdcbac81085c92da7259f33"},"advertisingid":{"profileid":"f81ca846faa39248b16e61a2b5e0d0abb7a9fcb9","partyid":"8f06ec3caa0f8a20603ed8d432217f3612035c20"},"experian":{"profileid":"9cb22bb838fe3ce4393036db703e78ae4ee3322b","partyid":"ff64c69f6859ae99c1d152dc47c5149711a6c0ff"},"firebase":{"profileid":"54fab1ae86d98f34ffc714fe0863fc8e3b539242","partyid":"43f1c6a7230012a467419efd6d85040ccb66cec8"},"adobe_campaign_manager":{"profileid":"f4d410eb6bf60331e39ef00e6c145b6f1d937dc1","partyid":"d5bdbed267ade383cdf2199bfe5033f518619ffa"},"skoonie_data_hub":{"profileid":"2d12e999db6e02019fec07c49a4afaf57e7ecba9","partyid":"41d84bc926a1da16be3783784b6779a02f5dea03"},"yospace":{"profileid":"f81ca846faa39248b16e61a2b5e0d0abb7a9fcb9","partyid":"8f06ec3caa0f8a20603ed8d432217f3612035c20"},"appdynamics":{"profileid":"602d3186b4ebe21a63b6d92e7a1e31469cfa3d6e","partyid":"4790c4761d51d9645b0251b7757b1330238438b3"},"nfl":{"profileid":"176163a4b464b60849bebbe3a0eea4583a3700e8","partyid":"191822ee7261ff247952c9a8018c78903e2a6a2b"},"ad_profiler":{"profileid":"6b765706be835bacbd6dd51471d7f124d2eeea94","partyid":"994115493f4ad5b8f285fb1826be568eedd5e032"},"fluxcapacitor":{"profileid":"6c2773fc781556f08a3abd592ba6a8693dbdfbff","partyid":"63d2b9ad5364ddfba673cb83c70762e5e275ab45"},"trackingid":{"profileid":"swlnkBA+J7/U88BFs9rq80qjLA8i4ARQfRHDQo5i+gU=","partyid":"swlnkBA+J7/U88BFs9rq80qjLA8i4ARQfRHDQo5i+gU="}}},"sendEmail":true}
	at org.junit.Assert.fail(Assert.java:89)
	at org.junit.Assert.assertTrue(Assert.java:42)
	at stepdefs.events.EventSteps.cleanup(EventSteps.java:31)
	at sun.reflect.GeneratedMethodAccessor193.invoke(Unknown Source)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at cucumber.runtime.Utils$1.call(Utils.java:40)
	at cucumber.runtime.Timeout.timeout(Timeout.java:16)
	at cucumber.runtime.Utils.invoke(Utils.java:34)
	at cucumber.runtime.java.JavaHookDefinition.execute(JavaHookDefinition.java:60)
	at cucumber.runtime.Runtime.runHookIfTagsMatch(Runtime.java:224)
	at cucumber.runtime.Runtime.runHooks(Runtime.java:212)
	at cucumber.runtime.Runtime.runAfterHooks(Runtime.java:206)
	at cucumber.runtime.model.CucumberScenario.run(CucumberScenario.java:46)
	at cucumber.runtime.junit.ExecutionUnitRunner.run(ExecutionUnitRunner.java:102)
	at org.junit.runners.Suite.runChild(Suite.java:128)
	at org.junit.runners.Suite.runChild(Suite.java:27)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.runtime.junit.ExamplesRunner.run(ExamplesRunner.java:59)
	at org.junit.runners.Suite.runChild(Suite.java:128)
	at org.junit.runners.Suite.runChild(Suite.java:27)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.runtime.junit.ScenarioOutlineRunner.run(ScenarioOutlineRunner.java:53)
	at cucumber.runtime.junit.FeatureRunner.runChild(FeatureRunner.java:63)
	at cucumber.runtime.junit.FeatureRunner.runChild(FeatureRunner.java:18)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.runtime.junit.FeatureRunner.run(FeatureRunner.java:70)
	at cucumber.api.junit.Cucumber.runChild(Cucumber.java:95)
	at cucumber.api.junit.Cucumber.runChild(Cucumber.java:38)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.api.junit.Cucumber.run(Cucumber.java:100)
	at org.apache.maven.surefire.junit4.JUnit4Provider.execute(JUnit4Provider.java:365)
	at org.apache.maven.surefire.junit4.JUnit4Provider.executeWithRerun(JUnit4Provider.java:273)
	at org.apache.maven.surefire.junit4.JUnit4Provider.executeTestSet(JUnit4Provider.java:238)
	at org.apache.maven.surefire.junit4.JUnit4Provider.invoke(JUnit4Provider.java:159)
	at org.apache.maven.surefire.booter.ForkedBooter.invokeProviderInSameClassLoader(ForkedBooter.java:384)
	at org.apache.maven.surefire.booter.ForkedBooter.runSuitesInProcess(ForkedBooter.java:345)
	at org.apache.maven.surefire.booter.ForkedBooter.execute(ForkedBooter.java:126)
	at org.apache.maven.surefire.booter.ForkedBooter.main(ForkedBooter.java:418)

[ERROR] Tests run: 9691, Failures: 4, Errors: 0, Skipped: 0, Time elapsed: 738.43 s <<< FAILURE! - in RunFunctionalTest
[ERROR] | false | VERSION_4 |  Time elapsed: 1.085 s  <<< FAILURE!
java.lang.AssertionError: Oh dear, message still on the queue: {"id":"c31d6445-1183-48db-87d5-cd09367192cf","timestamp":1639498225077,"provider":"SKY","territory":"GB","profileid":"99536370","event":"PROFILE_CREATED","data":{"profile":{"username":"GAUGE823105.1639498224245","title":"Miss","firstname":"Shavonda","lastname":"Adams","displayname":null,"contactemail":"gauge823105.1639498224245ce@oogway.oog","skyoptin":true,"globalbookmarkingoptout":null,"personalisedrecsoptout":null,"targetedoptin":true,"targetedoptindate":1639498225062,"targetedoptoutdate":null,"hhusertype":"guest","email":"gauge823105.1639498224245@oogway.oog","termsandconditionsaccepted":true,"registrationdate":1639498225062,"mobilenumber":null,"trackingid":"eO3ijJy9jcCHvxuEVHZ79MKwJMrNdFgYPz14qRiI5mA=","nsprofileid":"3ca5d68d-80f4-45f7-b96d-81f95ec5e08b","profileid":"99536370","emailverified":false,"mobilenumberverified":null,"hhid":null,"hhuserauthorised":null,"dateofbirth":null,"mobilenumberchanged":null,"emailchanged":1639498225062,"passwordrequireschange":false,"services":{},"securityquestionid":null,"journey":null,"inviternsprofileid":null,"euportability":true},"servicename":"atlantic","obfuscatedids":{"qubit":{"profileid":"53e4fd16c2571eef8b6750d416ff8669a9ae84c0","partyid":"53e4fd16c2571eef8b6750d416ff8669a9ae84c0"},"internal_userid":{"profileid":"c89341a61d5bd0dfc83708568954372dad8d67b2","partyid":"c89341a61d5bd0dfc83708568954372dad8d67b2"},"ad_smart":{"profileid":"e0326e36d92a00355c4d3cbb5b07ccb8bb9c8f0a","partyid":"e0326e36d92a00355c4d3cbb5b07ccb8bb9c8f0a"},"mparticle":{"profileid":"b5246455c1c1363c376833119105bc7ebd620566","partyid":"b5246455c1c1363c376833119105bc7ebd620566"},"salesforce":{"profileid":"54ef6b7b4c100921b71748d128824802d1c82138","partyid":"54ef6b7b4c100921b71748d128824802d1c82138"},"omniture":{"profileid":"7ef644362db8a27e703bb43174d55cdc9f3ff60b","partyid":"7ef644362db8a27e703bb43174d55cdc9f3ff60b"},"digitaladoptiontool":{"profileid":"5b1e251ede31cb8cd3b79ee3164098f806393c4c","partyid":"5b1e251ede31cb8cd3b79ee3164098f806393c4c"},"conviva":{"profileid":"37a0cdb8129753443dbb09642b9c91c5363f1e17","partyid":"37a0cdb8129753443dbb09642b9c91c5363f1e17"},"freewheel":{"profileid":"69ab0ca8c862666995e435ecc9817f5318512f70","partyid":"69ab0ca8c862666995e435ecc9817f5318512f70"},"adobe_dmp":{"profileid":"a04b9e631c2dcc97655cd72cf0b70d453813cf7d","partyid":"a04b9e631c2dcc97655cd72cf0b70d453813cf7d"},"oculus_vr":{"profileid":"8f1d20e5c95f900923d6439d233253d6f2a93a8c","partyid":"8f1d20e5c95f900923d6439d233253d6f2a93a8c"},"comscore":{"profileid":"44381d3aab48ccabfcbf6e5b512b3f46adcfb4de","partyid":"44381d3aab48ccabfcbf6e5b512b3f46adcfb4de"},"adobe_analytics":{"profileid":"ba5767f768745049707160dbc67bc83bb91046cc","partyid":"ba5767f768745049707160dbc67bc83bb91046cc"},"urban_airship":{"profileid":"5927693cd1f3f423856bd0665f5c1807ecf35b23","partyid":"5927693cd1f3f423856bd0665f5c1807ecf35b23"},"tax_jar":{"profileid":"7caca64dae1388bde1a02cab4163d03ba7bd1adb","partyid":"7caca64dae1388bde1a02cab4163d03ba7bd1adb"},"barclaycard":{"profileid":"8ecdb1ee86e5c99ad1500d23ed713c591e3ba406","partyid":"8ecdb1ee86e5c99ad1500d23ed713c591e3ba406"},"advertisingid":{"profileid":"69ab0ca8c862666995e435ecc9817f5318512f70","partyid":"69ab0ca8c862666995e435ecc9817f5318512f70"},"experian":{"profileid":"d90e177839a653f5a1c356960133f399ef3e4951","partyid":"d90e177839a653f5a1c356960133f399ef3e4951"},"firebase":{"profileid":"180d212762a5ca08867e3e00697370e6d40481ae","partyid":"180d212762a5ca08867e3e00697370e6d40481ae"},"adobe_campaign_manager":{"profileid":"ad3686ced2e8bad50f6a592e310b0a795d5d6ed9","partyid":"ad3686ced2e8bad50f6a592e310b0a795d5d6ed9"},"skoonie_data_hub":{"profileid":"20642fda8397b5ff74d56ac6bfad6a1ac732a3a3","partyid":"20642fda8397b5ff74d56ac6bfad6a1ac732a3a3"},"yospace":{"profileid":"69ab0ca8c862666995e435ecc9817f5318512f70","partyid":"69ab0ca8c862666995e435ecc9817f5318512f70"},"appdynamics":{"profileid":"e20add1ff94b2fd4cae1d0a7daa24a604b651f3f","partyid":"e20add1ff94b2fd4cae1d0a7daa24a604b651f3f"},"nfl":{"profileid":"08e1c9af72439fa4a37f8297026d4c2ede65371d","partyid":"08e1c9af72439fa4a37f8297026d4c2ede65371d"},"ad_profiler":{"profileid":"093fab7f509e98898f0ca6356bc360b23ce079d6","partyid":"093fab7f509e98898f0ca6356bc360b23ce079d6"},"fluxcapacitor":{"profileid":"b36a2c479a2c30901e261b57c4f15d3926d00591","partyid":"b36a2c479a2c30901e261b57c4f15d3926d00591"},"trackingid":{"profileid":"eO3ijJy9jcCHvxuEVHZ79MKwJMrNdFgYPz14qRiI5mA=","partyid":"eO3ijJy9jcCHvxuEVHZ79MKwJMrNdFgYPz14qRiI5mA="}},"operatorreference":null,"singleusetoken":"gauge823105.1639498224245@oogway.oogAAAAAAAAAAAAAAAAAAAAAAAAAAAA","contexturl":null,"context":null},"sendEmail":true}

[ERROR] | true | VERSION_4 |  Time elapsed: 1.11 s  <<< FAILURE!
java.lang.AssertionError: Oh dear, message still on the queue: {"id":"75bcae19-d1c8-465b-813a-2db3daf73a19","timestamp":1639498226562,"provider":"SKY","territory":"GB","profileid":"99536372","event":"PROFILE_CREATED","data":{"profile":{"username":"GAUGE785294.1639498226309","title":"Ms","firstname":"Nanci","lastname":"Erdman","displayname":null,"contactemail":"gauge785294.1639498226309ce@oogway.oog","skyoptin":true,"globalbookmarkingoptout":null,"personalisedrecsoptout":null,"targetedoptin":true,"targetedoptindate":1639498226545,"targetedoptoutdate":null,"hhusertype":"guest","email":"gauge785294.1639498226309@oogway.oog","termsandconditionsaccepted":true,"registrationdate":1639498226545,"mobilenumber":null,"trackingid":"UTyjgD4Qtr0v2xR4J98QKogKoNUARTf9uIPrKL+4QV4=","nsprofileid":"5f5ed309-d02f-4582-ab4c-7f10d14619f3","profileid":"99536372","emailverified":false,"mobilenumberverified":null,"hhid":null,"hhuserauthorised":null,"dateofbirth":null,"mobilenumberchanged":null,"emailchanged":1639498226545,"passwordrequireschange":false,"services":{},"securityquestionid":null,"journey":null,"inviternsprofileid":null,"euportability":true},"servicename":"atlantic","obfuscatedids":{"qubit":{"profileid":"01978957390f26d30270d7498238bed930176d9f","partyid":"01978957390f26d30270d7498238bed930176d9f"},"internal_userid":{"profileid":"5fd906678a92a589bc1d91147b2bdce16aa5374f","partyid":"5fd906678a92a589bc1d91147b2bdce16aa5374f"},"ad_smart":{"profileid":"f895e8d569b952e62a3aca49026808be795ba80b","partyid":"f895e8d569b952e62a3aca49026808be795ba80b"},"mparticle":{"profileid":"cfba480d8e6f4eaf03019c9da3ad3cdc26dddd98","partyid":"cfba480d8e6f4eaf03019c9da3ad3cdc26dddd98"},"salesforce":{"profileid":"52a79639248f678ad3cb39eff41bdcc7e43cbda7","partyid":"52a79639248f678ad3cb39eff41bdcc7e43cbda7"},"omniture":{"profileid":"987510035926dd99441bb688b96673f60fdcd833","partyid":"987510035926dd99441bb688b96673f60fdcd833"},"digitaladoptiontool":{"profileid":"6bf736c0433a676fa0c3e876888d108924523c6c","partyid":"6bf736c0433a676fa0c3e876888d108924523c6c"},"conviva":{"profileid":"ea9f1ea2f20c66ab6e7ebae37d84679e2cc6863f","partyid":"ea9f1ea2f20c66ab6e7ebae37d84679e2cc6863f"},"freewheel":{"profileid":"c13b201e912f32788c8fefd10bf0280d41636660","partyid":"c13b201e912f32788c8fefd10bf0280d41636660"},"adobe_dmp":{"profileid":"509dd4ea6fd83193b8383f494a9fbb98f65927f4","partyid":"509dd4ea6fd83193b8383f494a9fbb98f65927f4"},"oculus_vr":{"profileid":"8240b68a80a0ff232f790b31272912e3704e4843","partyid":"8240b68a80a0ff232f790b31272912e3704e4843"},"comscore":{"profileid":"0a4997b4d9ff370d12beec69afe76a657b283576","partyid":"0a4997b4d9ff370d12beec69afe76a657b283576"},"adobe_analytics":{"profileid":"76e602046a1ac30f6a3779eba2b35fcde5eb52ee","partyid":"76e602046a1ac30f6a3779eba2b35fcde5eb52ee"},"urban_airship":{"profileid":"9c840e8edda6484196c56aa343d17c57e07c3712","partyid":"9c840e8edda6484196c56aa343d17c57e07c3712"},"tax_jar":{"profileid":"e070f5db31dbaa90bc992ddbfb481cdb8ff8dcf7","partyid":"e070f5db31dbaa90bc992ddbfb481cdb8ff8dcf7"},"barclaycard":{"profileid":"440cab76ed5b4ce3709878c2ed130d6e412b46ea","partyid":"440cab76ed5b4ce3709878c2ed130d6e412b46ea"},"advertisingid":{"profileid":"c13b201e912f32788c8fefd10bf0280d41636660","partyid":"c13b201e912f32788c8fefd10bf0280d41636660"},"experian":{"profileid":"3ae47aebd28ad24d1a66950bfb079a097b714fe2","partyid":"3ae47aebd28ad24d1a66950bfb079a097b714fe2"},"firebase":{"profileid":"d19db34b39b6a688d81822a73e1dd362d2d6e6e2","partyid":"d19db34b39b6a688d81822a73e1dd362d2d6e6e2"},"adobe_campaign_manager":{"profileid":"ec72f6c88dd94ef06d2c0d84714fcbbbd595290a","partyid":"ec72f6c88dd94ef06d2c0d84714fcbbbd595290a"},"skoonie_data_hub":{"profileid":"e0f3bc5a0a61c3ed8ac9919f26720a7b85f814f0","partyid":"e0f3bc5a0a61c3ed8ac9919f26720a7b85f814f0"},"yospace":{"profileid":"c13b201e912f32788c8fefd10bf0280d41636660","partyid":"c13b201e912f32788c8fefd10bf0280d41636660"},"appdynamics":{"profileid":"6d2c1b864000f09da44a97b1abf2b6aa6ea939a6","partyid":"6d2c1b864000f09da44a97b1abf2b6aa6ea939a6"},"nfl":{"profileid":"138195efc28373d87bac3679eb2ddcd77c967a31","partyid":"138195efc28373d87bac3679eb2ddcd77c967a31"},"ad_profiler":{"profileid":"b6b93021e2692b8e4ff23edbf64c5c6289e3984d","partyid":"b6b93021e2692b8e4ff23edbf64c5c6289e3984d"},"fluxcapacitor":{"profileid":"fe582f6ee2fc6b0208b1fad34d844d8e2147d681","partyid":"fe582f6ee2fc6b0208b1fad34d844d8e2147d681"},"trackingid":{"profileid":"UTyjgD4Qtr0v2xR4J98QKogKoNUARTf9uIPrKL+4QV4=","partyid":"UTyjgD4Qtr0v2xR4J98QKogKoNUARTf9uIPrKL+4QV4="}},"operatorreference":null,"singleusetoken":"gauge785294.1639498226309@oogway.oogAAAAAAAAAAAAAAAAAAAAAAAAAAAA","contexturl":null,"context":null},"sendEmail":true}

[ERROR] | VERSION_2 |  Time elapsed: 1.061 s  <<< FAILURE!
java.lang.AssertionError: Oh dear, message still on the queue: {"id":"436639da-b337-402e-a024-d3440cde3e84","timestamp":1639498228926,"provider":"SKY","territory":"GB","profileid":"9524099380067","event":"SIGNIN","data":{"username":"1639498228870IDZPTRQUVJYYRHGO","service":"atlantic","context":null},"sendEmail":true}

[ERROR] | VERSION_3 |  Time elapsed: 1.059 s  <<< FAILURE!
java.lang.AssertionError: Oh dear, message still on the queue: {"id":"9c4aa969-7831-4eb8-bedb-01a9ba99c7e3","timestamp":1639498231386,"provider":"SKY","territory":"GB","profileid":"99536377","event":"PROFILE_MODIFIED_PARTY_ID_AND_USER_TYPE","data":{"profile":{"username":"GAUGE099450.1639498231080","title":"Mr","firstname":"Cami","lastname":"Buckridge","displayname":null,"contactemail":"gauge099450.1639498231080ce@oogway.oog","skyoptin":true,"globalbookmarkingoptout":null,"personalisedrecsoptout":null,"targetedoptin":true,"targetedoptindate":1639498231293,"targetedoptoutdate":null,"hhusertype":"primary","email":"gauge099450.1639498231080@oogway.oog","termsandconditionsaccepted":true,"registrationdate":1639498231293,"mobilenumber":null,"trackingid":"swlnkBA+J7/U88BFs9rq80qjLA8i4ARQfRHDQo5i+gU=","nsprofileid":"39818d77-e029-4034-95f5-1ea385e5ab7d","profileid":"99536377","emailverified":false,"mobilenumberverified":null,"hhid":"2113462067","hhuserauthorised":null,"dateofbirth":null,"mobilenumberchanged":null,"emailchanged":1639498231293,"passwordrequireschange":false,"services":{},"securityquestionid":null,"journey":null,"inviternsprofileid":null,"euportability":true},"changes":["hhid","hhusertype","obfuscatedids"],"primaryprofile":null,"obfuscatedids":{"qubit":{"profileid":"a7ab02c534ae00914132e638fd32a9c49a81e546","partyid":"0ab8a509dd8bdc3144f6fd35a6c08860982c947c"},"internal_userid":{"profileid":"0454fd0713c28caed124843cc8d60e99509107a4","partyid":"62d546fcb7c822cc7ac406b647ff6112f9e9eafd"},"ad_smart":{"profileid":"a127a00c2865e0f14d09888268bd0dacf222e5cc","partyid":"5e552185d91db1c82230f3111519f5413121ab02"},"mparticle":{"profileid":"73225fe2686813e3db3eb59c31981fafb0be19f6","partyid":"d822d49f6201dc26634e8d5b75eaac724786c3f3"},"salesforce":{"profileid":"966bd0126c4342d9146f8421a33f54dee69cb271","partyid":"c1f356b83ca5b50efa9ba39ba015ed7c18e4143f"},"omniture":{"profileid":"87b5e559e75433472da4b75dc8a4bfd863edcf76","partyid":"b64c47c2c5814f5d1faa3d173f70eebed56cce84"},"digitaladoptiontool":{"profileid":"dc79d1f004c0d2e8de10c8044ca5f3c7f03cf4d3","partyid":"9e232106c02c34698baef344d57b2a2c30dfc69e"},"conviva":{"profileid":"7217735125479e4274e504b6476339448efae6b8","partyid":"b9204cc89b0e18aa5fc11d66b1d217f2235a8af8"},"freewheel":{"profileid":"f81ca846faa39248b16e61a2b5e0d0abb7a9fcb9","partyid":"8f06ec3caa0f8a20603ed8d432217f3612035c20"},"adobe_dmp":{"profileid":"5d46848d04bbe037dc9a8120fdf58b58f2a35772","partyid":"387e2953d1734a03991dde6f8651c5c3201ca3ce"},"oculus_vr":{"profileid":"bf931d0a6192d9c02e1daa3fafb5fea7439384a8","partyid":"535f5b06cfa96ad87c03c7ff483f98c24c5e5b4a"},"comscore":{"profileid":"5fd2a2f22723954af2e8bb3d188ba0ceb1678827","partyid":"c0011998a7989727c53eac1f0c03268f22db6379"},"adobe_analytics":{"profileid":"db9f20572e0fda355064976a8a2d13fa8cfea1ef","partyid":"7b2057d8653c4fa8b94b6daf446f809bcd55a97b"},"urban_airship":{"profileid":"eacaebd7c9680d144de8daeb84ed63b074228890","partyid":"3ff2cd0b2ac5b2674d8c65414b551da8748a5121"},"tax_jar":{"profileid":"51ac25418f81c29546bf84b98330b7ee0c1238b0","partyid":"2fa12479680a561f722d5df15cdd438a07a73f4b"},"barclaycard":{"profileid":"00b8c4392e0b82f43a22668ac0231b289fbeaf6a","partyid":"0e5502cfa72c3bddefdcbac81085c92da7259f33"},"advertisingid":{"profileid":"f81ca846faa39248b16e61a2b5e0d0abb7a9fcb9","partyid":"8f06ec3caa0f8a20603ed8d432217f3612035c20"},"experian":{"profileid":"9cb22bb838fe3ce4393036db703e78ae4ee3322b","partyid":"ff64c69f6859ae99c1d152dc47c5149711a6c0ff"},"firebase":{"profileid":"54fab1ae86d98f34ffc714fe0863fc8e3b539242","partyid":"43f1c6a7230012a467419efd6d85040ccb66cec8"},"adobe_campaign_manager":{"profileid":"f4d410eb6bf60331e39ef00e6c145b6f1d937dc1","partyid":"d5bdbed267ade383cdf2199bfe5033f518619ffa"},"skoonie_data_hub":{"profileid":"2d12e999db6e02019fec07c49a4afaf57e7ecba9","partyid":"41d84bc926a1da16be3783784b6779a02f5dea03"},"yospace":{"profileid":"f81ca846faa39248b16e61a2b5e0d0abb7a9fcb9","partyid":"8f06ec3caa0f8a20603ed8d432217f3612035c20"},"appdynamics":{"profileid":"602d3186b4ebe21a63b6d92e7a1e31469cfa3d6e","partyid":"4790c4761d51d9645b0251b7757b1330238438b3"},"nfl":{"profileid":"176163a4b464b60849bebbe3a0eea4583a3700e8","partyid":"191822ee7261ff247952c9a8018c78903e2a6a2b"},"ad_profiler":{"profileid":"6b765706be835bacbd6dd51471d7f124d2eeea94","partyid":"994115493f4ad5b8f285fb1826be568eedd5e032"},"fluxcapacitor":{"profileid":"6c2773fc781556f08a3abd592ba6a8693dbdfbff","partyid":"63d2b9ad5364ddfba673cb83c70762e5e275ab45"},"trackingid":{"profileid":"swlnkBA+J7/U88BFs9rq80qjLA8i4ARQfRHDQo5i+gU=","partyid":"swlnkBA+J7/U88BFs9rq80qjLA8i4ARQfRHDQo5i+gU="}}},"sendEmail":true}

[INFO] 
[INFO] Results:
[INFO] 
[ERROR] Failures: 
[ERROR] | VERSION_2 |.| VERSION_2 |
[INFO]   Run 1: PASS
[INFO]   Run 2: PASS
[INFO]   Run 3: PASS
[ERROR]   Run 4: Oh dear, message still on the queue: {"id":"436639da-b337-402e-a024-d3440cde3e84","timestamp":1639498228926,"provider":"SKY","territory":"GB","profileid":"9524099380067","event":"SIGNIN","data":{"username":"1639498228870IDZPTRQUVJYYRHGO","service":"atlantic","context":null},"sendEmail":true}
[INFO]   Run 5: PASS
[INFO] 
[ERROR] | VERSION_3 |.| VERSION_3 |
[INFO]   Run 1: PASS
[INFO]   Run 2: PASS
[INFO]   Run 3: PASS
[ERROR]   Run 4: Oh dear, message still on the queue: {"id":"9c4aa969-7831-4eb8-bedb-01a9ba99c7e3","timestamp":1639498231386,"provider":"SKY","territory":"GB","profileid":"99536377","event":"PROFILE_MODIFIED_PARTY_ID_AND_USER_TYPE","data":{"profile":{"username":"GAUGE099450.1639498231080","title":"Mr","firstname":"Cami","lastname":"Buckridge","displayname":null,"contactemail":"gauge099450.1639498231080ce@oogway.oog","skyoptin":true,"globalbookmarkingoptout":null,"personalisedrecsoptout":null,"targetedoptin":true,"targetedoptindate":1639498231293,"targetedoptoutdate":null,"hhusertype":"primary","email":"gauge099450.1639498231080@oogway.oog","termsandconditionsaccepted":true,"registrationdate":1639498231293,"mobilenumber":null,"trackingid":"swlnkBA+J7/U88BFs9rq80qjLA8i4ARQfRHDQo5i+gU=","nsprofileid":"39818d77-e029-4034-95f5-1ea385e5ab7d","profileid":"99536377","emailverified":false,"mobilenumberverified":null,"hhid":"2113462067","hhuserauthorised":null,"dateofbirth":null,"mobilenumberchanged":null,"emailchanged":1639498231293,"passwordrequireschange":false,"services":{},"securityquestionid":null,"journey":null,"inviternsprofileid":null,"euportability":true},"changes":["hhid","hhusertype","obfuscatedids"],"primaryprofile":null,"obfuscatedids":{"qubit":{"profileid":"a7ab02c534ae00914132e638fd32a9c49a81e546","partyid":"0ab8a509dd8bdc3144f6fd35a6c08860982c947c"},"internal_userid":{"profileid":"0454fd0713c28caed124843cc8d60e99509107a4","partyid":"62d546fcb7c822cc7ac406b647ff6112f9e9eafd"},"ad_smart":{"profileid":"a127a00c2865e0f14d09888268bd0dacf222e5cc","partyid":"5e552185d91db1c82230f3111519f5413121ab02"},"mparticle":{"profileid":"73225fe2686813e3db3eb59c31981fafb0be19f6","partyid":"d822d49f6201dc26634e8d5b75eaac724786c3f3"},"salesforce":{"profileid":"966bd0126c4342d9146f8421a33f54dee69cb271","partyid":"c1f356b83ca5b50efa9ba39ba015ed7c18e4143f"},"omniture":{"profileid":"87b5e559e75433472da4b75dc8a4bfd863edcf76","partyid":"b64c47c2c5814f5d1faa3d173f70eebed56cce84"},"digitaladoptiontool":{"profileid":"dc79d1f004c0d2e8de10c8044ca5f3c7f03cf4d3","partyid":"9e232106c02c34698baef344d57b2a2c30dfc69e"},"conviva":{"profileid":"7217735125479e4274e504b6476339448efae6b8","partyid":"b9204cc89b0e18aa5fc11d66b1d217f2235a8af8"},"freewheel":{"profileid":"f81ca846faa39248b16e61a2b5e0d0abb7a9fcb9","partyid":"8f06ec3caa0f8a20603ed8d432217f3612035c20"},"adobe_dmp":{"profileid":"5d46848d04bbe037dc9a8120fdf58b58f2a35772","partyid":"387e2953d1734a03991dde6f8651c5c3201ca3ce"},"oculus_vr":{"profileid":"bf931d0a6192d9c02e1daa3fafb5fea7439384a8","partyid":"535f5b06cfa96ad87c03c7ff483f98c24c5e5b4a"},"comscore":{"profileid":"5fd2a2f22723954af2e8bb3d188ba0ceb1678827","partyid":"c0011998a7989727c53eac1f0c03268f22db6379"},"adobe_analytics":{"profileid":"db9f20572e0fda355064976a8a2d13fa8cfea1ef","partyid":"7b2057d8653c4fa8b94b6daf446f809bcd55a97b"},"urban_airship":{"profileid":"eacaebd7c9680d144de8daeb84ed63b074228890","partyid":"3ff2cd0b2ac5b2674d8c65414b551da8748a5121"},"tax_jar":{"profileid":"51ac25418f81c29546bf84b98330b7ee0c1238b0","partyid":"2fa12479680a561f722d5df15cdd438a07a73f4b"},"barclaycard":{"profileid":"00b8c4392e0b82f43a22668ac0231b289fbeaf6a","partyid":"0e5502cfa72c3bddefdcbac81085c92da7259f33"},"advertisingid":{"profileid":"f81ca846faa39248b16e61a2b5e0d0abb7a9fcb9","partyid":"8f06ec3caa0f8a20603ed8d432217f3612035c20"},"experian":{"profileid":"9cb22bb838fe3ce4393036db703e78ae4ee3322b","partyid":"ff64c69f6859ae99c1d152dc47c5149711a6c0ff"},"firebase":{"profileid":"54fab1ae86d98f34ffc714fe0863fc8e3b539242","partyid":"43f1c6a7230012a467419efd6d85040ccb66cec8"},"adobe_campaign_manager":{"profileid":"f4d410eb6bf60331e39ef00e6c145b6f1d937dc1","partyid":"d5bdbed267ade383cdf2199bfe5033f518619ffa"},"skoonie_data_hub":{"profileid":"2d12e999db6e02019fec07c49a4afaf57e7ecba9","partyid":"41d84bc926a1da16be3783784b6779a02f5dea03"},"yospace":{"profileid":"f81ca846faa39248b16e61a2b5e0d0abb7a9fcb9","partyid":"8f06ec3caa0f8a20603ed8d432217f3612035c20"},"appdynamics":{"profileid":"602d3186b4ebe21a63b6d92e7a1e31469cfa3d6e","partyid":"4790c4761d51d9645b0251b7757b1330238438b3"},"nfl":{"profileid":"176163a4b464b60849bebbe3a0eea4583a3700e8","partyid":"191822ee7261ff247952c9a8018c78903e2a6a2b"},"ad_profiler":{"profileid":"6b765706be835bacbd6dd51471d7f124d2eeea94","partyid":"994115493f4ad5b8f285fb1826be568eedd5e032"},"fluxcapacitor":{"profileid":"6c2773fc781556f08a3abd592ba6a8693dbdfbff","partyid":"63d2b9ad5364ddfba673cb83c70762e5e275ab45"},"trackingid":{"profileid":"swlnkBA+J7/U88BFs9rq80qjLA8i4ARQfRHDQo5i+gU=","partyid":"swlnkBA+J7/U88BFs9rq80qjLA8i4ARQfRHDQo5i+gU="}}},"sendEmail":true}
[INFO]   Run 5: PASS
[INFO] 
[ERROR] | false | VERSION_4 |.| false | VERSION_4 |
[INFO]   Run 1: PASS
[ERROR]   Run 2: Oh dear, message still on the queue: {"id":"c31d6445-1183-48db-87d5-cd09367192cf","timestamp":1639498225077,"provider":"SKY","territory":"GB","profileid":"99536370","event":"PROFILE_CREATED","data":{"profile":{"username":"GAUGE823105.1639498224245","title":"Miss","firstname":"Shavonda","lastname":"Adams","displayname":null,"contactemail":"gauge823105.1639498224245ce@oogway.oog","skyoptin":true,"globalbookmarkingoptout":null,"personalisedrecsoptout":null,"targetedoptin":true,"targetedoptindate":1639498225062,"targetedoptoutdate":null,"hhusertype":"guest","email":"gauge823105.1639498224245@oogway.oog","termsandconditionsaccepted":true,"registrationdate":1639498225062,"mobilenumber":null,"trackingid":"eO3ijJy9jcCHvxuEVHZ79MKwJMrNdFgYPz14qRiI5mA=","nsprofileid":"3ca5d68d-80f4-45f7-b96d-81f95ec5e08b","profileid":"99536370","emailverified":false,"mobilenumberverified":null,"hhid":null,"hhuserauthorised":null,"dateofbirth":null,"mobilenumberchanged":null,"emailchanged":1639498225062,"passwordrequireschange":false,"services":{},"securityquestionid":null,"journey":null,"inviternsprofileid":null,"euportability":true},"servicename":"atlantic","obfuscatedids":{"qubit":{"profileid":"53e4fd16c2571eef8b6750d416ff8669a9ae84c0","partyid":"53e4fd16c2571eef8b6750d416ff8669a9ae84c0"},"internal_userid":{"profileid":"c89341a61d5bd0dfc83708568954372dad8d67b2","partyid":"c89341a61d5bd0dfc83708568954372dad8d67b2"},"ad_smart":{"profileid":"e0326e36d92a00355c4d3cbb5b07ccb8bb9c8f0a","partyid":"e0326e36d92a00355c4d3cbb5b07ccb8bb9c8f0a"},"mparticle":{"profileid":"b5246455c1c1363c376833119105bc7ebd620566","partyid":"b5246455c1c1363c376833119105bc7ebd620566"},"salesforce":{"profileid":"54ef6b7b4c100921b71748d128824802d1c82138","partyid":"54ef6b7b4c100921b71748d128824802d1c82138"},"omniture":{"profileid":"7ef644362db8a27e703bb43174d55cdc9f3ff60b","partyid":"7ef644362db8a27e703bb43174d55cdc9f3ff60b"},"digitaladoptiontool":{"profileid":"5b1e251ede31cb8cd3b79ee3164098f806393c4c","partyid":"5b1e251ede31cb8cd3b79ee3164098f806393c4c"},"conviva":{"profileid":"37a0cdb8129753443dbb09642b9c91c5363f1e17","partyid":"37a0cdb8129753443dbb09642b9c91c5363f1e17"},"freewheel":{"profileid":"69ab0ca8c862666995e435ecc9817f5318512f70","partyid":"69ab0ca8c862666995e435ecc9817f5318512f70"},"adobe_dmp":{"profileid":"a04b9e631c2dcc97655cd72cf0b70d453813cf7d","partyid":"a04b9e631c2dcc97655cd72cf0b70d453813cf7d"},"oculus_vr":{"profileid":"8f1d20e5c95f900923d6439d233253d6f2a93a8c","partyid":"8f1d20e5c95f900923d6439d233253d6f2a93a8c"},"comscore":{"profileid":"44381d3aab48ccabfcbf6e5b512b3f46adcfb4de","partyid":"44381d3aab48ccabfcbf6e5b512b3f46adcfb4de"},"adobe_analytics":{"profileid":"ba5767f768745049707160dbc67bc83bb91046cc","partyid":"ba5767f768745049707160dbc67bc83bb91046cc"},"urban_airship":{"profileid":"5927693cd1f3f423856bd0665f5c1807ecf35b23","partyid":"5927693cd1f3f423856bd0665f5c1807ecf35b23"},"tax_jar":{"profileid":"7caca64dae1388bde1a02cab4163d03ba7bd1adb","partyid":"7caca64dae1388bde1a02cab4163d03ba7bd1adb"},"barclaycard":{"profileid":"8ecdb1ee86e5c99ad1500d23ed713c591e3ba406","partyid":"8ecdb1ee86e5c99ad1500d23ed713c591e3ba406"},"advertisingid":{"profileid":"69ab0ca8c862666995e435ecc9817f5318512f70","partyid":"69ab0ca8c862666995e435ecc9817f5318512f70"},"experian":{"profileid":"d90e177839a653f5a1c356960133f399ef3e4951","partyid":"d90e177839a653f5a1c356960133f399ef3e4951"},"firebase":{"profileid":"180d212762a5ca08867e3e00697370e6d40481ae","partyid":"180d212762a5ca08867e3e00697370e6d40481ae"},"adobe_campaign_manager":{"profileid":"ad3686ced2e8bad50f6a592e310b0a795d5d6ed9","partyid":"ad3686ced2e8bad50f6a592e310b0a795d5d6ed9"},"skoonie_data_hub":{"profileid":"20642fda8397b5ff74d56ac6bfad6a1ac732a3a3","partyid":"20642fda8397b5ff74d56ac6bfad6a1ac732a3a3"},"yospace":{"profileid":"69ab0ca8c862666995e435ecc9817f5318512f70","partyid":"69ab0ca8c862666995e435ecc9817f5318512f70"},"appdynamics":{"profileid":"e20add1ff94b2fd4cae1d0a7daa24a604b651f3f","partyid":"e20add1ff94b2fd4cae1d0a7daa24a604b651f3f"},"nfl":{"profileid":"08e1c9af72439fa4a37f8297026d4c2ede65371d","partyid":"08e1c9af72439fa4a37f8297026d4c2ede65371d"},"ad_profiler":{"profileid":"093fab7f509e98898f0ca6356bc360b23ce079d6","partyid":"093fab7f509e98898f0ca6356bc360b23ce079d6"},"fluxcapacitor":{"profileid":"b36a2c479a2c30901e261b57c4f15d3926d00591","partyid":"b36a2c479a2c30901e261b57c4f15d3926d00591"},"trackingid":{"profileid":"eO3ijJy9jcCHvxuEVHZ79MKwJMrNdFgYPz14qRiI5mA=","partyid":"eO3ijJy9jcCHvxuEVHZ79MKwJMrNdFgYPz14qRiI5mA="}},"operatorreference":null,"singleusetoken":"gauge823105.1639498224245@oogway.oogAAAAAAAAAAAAAAAAAAAAAAAAAAAA","contexturl":null,"context":null},"sendEmail":true}
[INFO] 
[ERROR] | true | VERSION_4 |.| true | VERSION_4 |
[INFO]   Run 1: PASS
[ERROR]   Run 2: Oh dear, message still on the queue: {"id":"75bcae19-d1c8-465b-813a-2db3daf73a19","timestamp":1639498226562,"provider":"SKY","territory":"GB","profileid":"99536372","event":"PROFILE_CREATED","data":{"profile":{"username":"GAUGE785294.1639498226309","title":"Ms","firstname":"Nanci","lastname":"Erdman","displayname":null,"contactemail":"gauge785294.1639498226309ce@oogway.oog","skyoptin":true,"globalbookmarkingoptout":null,"personalisedrecsoptout":null,"targetedoptin":true,"targetedoptindate":1639498226545,"targetedoptoutdate":null,"hhusertype":"guest","email":"gauge785294.1639498226309@oogway.oog","termsandconditionsaccepted":true,"registrationdate":1639498226545,"mobilenumber":null,"trackingid":"UTyjgD4Qtr0v2xR4J98QKogKoNUARTf9uIPrKL+4QV4=","nsprofileid":"5f5ed309-d02f-4582-ab4c-7f10d14619f3","profileid":"99536372","emailverified":false,"mobilenumberverified":null,"hhid":null,"hhuserauthorised":null,"dateofbirth":null,"mobilenumberchanged":null,"emailchanged":1639498226545,"passwordrequireschange":false,"services":{},"securityquestionid":null,"journey":null,"inviternsprofileid":null,"euportability":true},"servicename":"atlantic","obfuscatedids":{"qubit":{"profileid":"01978957390f26d30270d7498238bed930176d9f","partyid":"01978957390f26d30270d7498238bed930176d9f"},"internal_userid":{"profileid":"5fd906678a92a589bc1d91147b2bdce16aa5374f","partyid":"5fd906678a92a589bc1d91147b2bdce16aa5374f"},"ad_smart":{"profileid":"f895e8d569b952e62a3aca49026808be795ba80b","partyid":"f895e8d569b952e62a3aca49026808be795ba80b"},"mparticle":{"profileid":"cfba480d8e6f4eaf03019c9da3ad3cdc26dddd98","partyid":"cfba480d8e6f4eaf03019c9da3ad3cdc26dddd98"},"salesforce":{"profileid":"52a79639248f678ad3cb39eff41bdcc7e43cbda7","partyid":"52a79639248f678ad3cb39eff41bdcc7e43cbda7"},"omniture":{"profileid":"987510035926dd99441bb688b96673f60fdcd833","partyid":"987510035926dd99441bb688b96673f60fdcd833"},"digitaladoptiontool":{"profileid":"6bf736c0433a676fa0c3e876888d108924523c6c","partyid":"6bf736c0433a676fa0c3e876888d108924523c6c"},"conviva":{"profileid":"ea9f1ea2f20c66ab6e7ebae37d84679e2cc6863f","partyid":"ea9f1ea2f20c66ab6e7ebae37d84679e2cc6863f"},"freewheel":{"profileid":"c13b201e912f32788c8fefd10bf0280d41636660","partyid":"c13b201e912f32788c8fefd10bf0280d41636660"},"adobe_dmp":{"profileid":"509dd4ea6fd83193b8383f494a9fbb98f65927f4","partyid":"509dd4ea6fd83193b8383f494a9fbb98f65927f4"},"oculus_vr":{"profileid":"8240b68a80a0ff232f790b31272912e3704e4843","partyid":"8240b68a80a0ff232f790b31272912e3704e4843"},"comscore":{"profileid":"0a4997b4d9ff370d12beec69afe76a657b283576","partyid":"0a4997b4d9ff370d12beec69afe76a657b283576"},"adobe_analytics":{"profileid":"76e602046a1ac30f6a3779eba2b35fcde5eb52ee","partyid":"76e602046a1ac30f6a3779eba2b35fcde5eb52ee"},"urban_airship":{"profileid":"9c840e8edda6484196c56aa343d17c57e07c3712","partyid":"9c840e8edda6484196c56aa343d17c57e07c3712"},"tax_jar":{"profileid":"e070f5db31dbaa90bc992ddbfb481cdb8ff8dcf7","partyid":"e070f5db31dbaa90bc992ddbfb481cdb8ff8dcf7"},"barclaycard":{"profileid":"440cab76ed5b4ce3709878c2ed130d6e412b46ea","partyid":"440cab76ed5b4ce3709878c2ed130d6e412b46ea"},"advertisingid":{"profileid":"c13b201e912f32788c8fefd10bf0280d41636660","partyid":"c13b201e912f32788c8fefd10bf0280d41636660"},"experian":{"profileid":"3ae47aebd28ad24d1a66950bfb079a097b714fe2","partyid":"3ae47aebd28ad24d1a66950bfb079a097b714fe2"},"firebase":{"profileid":"d19db34b39b6a688d81822a73e1dd362d2d6e6e2","partyid":"d19db34b39b6a688d81822a73e1dd362d2d6e6e2"},"adobe_campaign_manager":{"profileid":"ec72f6c88dd94ef06d2c0d84714fcbbbd595290a","partyid":"ec72f6c88dd94ef06d2c0d84714fcbbbd595290a"},"skoonie_data_hub":{"profileid":"e0f3bc5a0a61c3ed8ac9919f26720a7b85f814f0","partyid":"e0f3bc5a0a61c3ed8ac9919f26720a7b85f814f0"},"yospace":{"profileid":"c13b201e912f32788c8fefd10bf0280d41636660","partyid":"c13b201e912f32788c8fefd10bf0280d41636660"},"appdynamics":{"profileid":"6d2c1b864000f09da44a97b1abf2b6aa6ea939a6","partyid":"6d2c1b864000f09da44a97b1abf2b6aa6ea939a6"},"nfl":{"profileid":"138195efc28373d87bac3679eb2ddcd77c967a31","partyid":"138195efc28373d87bac3679eb2ddcd77c967a31"},"ad_profiler":{"profileid":"b6b93021e2692b8e4ff23edbf64c5c6289e3984d","partyid":"b6b93021e2692b8e4ff23edbf64c5c6289e3984d"},"fluxcapacitor":{"profileid":"fe582f6ee2fc6b0208b1fad34d844d8e2147d681","partyid":"fe582f6ee2fc6b0208b1fad34d844d8e2147d681"},"trackingid":{"profileid":"UTyjgD4Qtr0v2xR4J98QKogKoNUARTf9uIPrKL+4QV4=","partyid":"UTyjgD4Qtr0v2xR4J98QKogKoNUARTf9uIPrKL+4QV4="}},"operatorreference":null,"singleusetoken":"gauge785294.1639498226309@oogway.oogAAAAAAAAAAAAAAAAAAAAAAAAAAAA","contexturl":null,"context":null},"sendEmail":true}
[INFO] 
[INFO] 
[ERROR] Tests run: 9681, Failures: 4, Errors: 0, Skipped: 0
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 12:36 min
[INFO] Finished at: 2021-12-14T16:11:05Z
[INFO] Final Memory: 55M/963M
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-surefire-plugin:3.0.0-M3:test (default-test) on project component-test: There are test failures.
[ERROR] 
[ERROR] Please refer to /var/sky/build/jobs/oogway/branches/IPB-15149/workspace/component-test/target/surefire-reports for the individual test results.
[ERROR] Please refer to dump files (if any exist) [date].dump, [date]-jvmRun[N].dump and [date].dumpstream.
[ERROR] -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoFailureException
```

[Full log here](./15.log).

## Build 16 - @singlenode

After adding `@singlenode` to `revoketokens.feature`...

```
@managementtokens
@singlenode
Feature: revoke all tokens
...
```

Log snippet:

```bash
Failed scenarios:
OAuthManagement/RevokeOauthTokens.feature:105 # Scenario Outline: Invalid client Id for tenant results in 400 response

1625 Scenarios (1 failed, 1624 passed)
8066 Steps (1 failed, 5 skipped, 8060 passed)
12m9.444s

java.lang.RuntimeException: org.apache.http.NoHttpResponseException: funwap01.oogway.bskyb.com:80 failed to respond
	at sky.oogway.clients.oogway.OogwayWapClient.doPost(OogwayWapClient.java:80)
	at sky.oogway.clients.oogway.OogwayResource$1.invoke(OogwayResource.java:31)
	at sky.oogway.clients.oogway.OogwayResource.doCall(OogwayResource.java:69)
	at sky.oogway.clients.oogway.OogwayResource.doPost(OogwayResource.java:27)
	at sky.oogway.clients.oogway.resources.FeatureResource.lambda$updateFeature$0(FeatureResource.java:22)
	at java.util.ArrayList.forEach(ArrayList.java:1249)
	at sky.oogway.clients.oogway.resources.FeatureResource.updateFeature(FeatureResource.java:20)
	at stepdefs.metadata.FeatureSwitchSteps.update_feature(FeatureSwitchSteps.java:54)
	at ✽.And a client changes the multi-tenancy-revoke_token_by_messo feature value to true and with context ANY, ANY, ANY, ANY(OAuthManagement/RevokeOauthTokens.feature:97)
Caused by: org.apache.http.NoHttpResponseException: funwap01.oogway.bskyb.com:80 failed to respond
	at org.apache.http.impl.conn.DefaultHttpResponseParser.parseHead(DefaultHttpResponseParser.java:141)
	at org.apache.http.impl.conn.DefaultHttpResponseParser.parseHead(DefaultHttpResponseParser.java:56)
	at org.apache.http.impl.io.AbstractMessageParser.parse(AbstractMessageParser.java:259)
	at org.apache.http.impl.AbstractHttpClientConnection.receiveResponseHeader(AbstractHttpClientConnection.java:294)
	at org.apache.http.impl.conn.DefaultClientConnection.receiveResponseHeader(DefaultClientConnection.java:257)
	at org.apache.http.impl.conn.AbstractClientConnAdapter.receiveResponseHeader(AbstractClientConnAdapter.java:230)
	at org.apache.http.protocol.HttpRequestExecutor.doReceiveResponse(HttpRequestExecutor.java:273)
	at org.apache.http.protocol.HttpRequestExecutor.execute(HttpRequestExecutor.java:125)
	at org.apache.http.impl.client.DefaultRequestDirector.tryExecute(DefaultRequestDirector.java:684)
	at org.apache.http.impl.client.DefaultRequestDirector.execute(DefaultRequestDirector.java:486)
	at org.apache.http.impl.client.AbstractHttpClient.doExecute(AbstractHttpClient.java:835)
	at org.apache.http.impl.client.CloseableHttpClient.execute(CloseableHttpClient.java:83)
	at org.apache.http.impl.client.CloseableHttpClient.execute(CloseableHttpClient.java:108)
	at org.apache.http.impl.client.CloseableHttpClient.execute(CloseableHttpClient.java:56)
	at com.google.api.client.http.apache.ApacheHttpRequest.execute(ApacheHttpRequest.java:63)
	at com.google.api.client.http.HttpRequest.execute(HttpRequest.java:995)
	at sky.oogway.clients.oogway.OogwayWapClient.checkResponse(OogwayWapClient.java:115)
	at sky.oogway.clients.oogway.OogwayWapClient.doPost(OogwayWapClient.java:78)
	at sky.oogway.clients.oogway.OogwayResource$1.invoke(OogwayResource.java:31)
	at sky.oogway.clients.oogway.OogwayResource.doCall(OogwayResource.java:69)
	at sky.oogway.clients.oogway.OogwayResource.doPost(OogwayResource.java:27)
	at sky.oogway.clients.oogway.resources.FeatureResource.lambda$updateFeature$0(FeatureResource.java:22)
	at java.util.ArrayList.forEach(ArrayList.java:1249)
	at sky.oogway.clients.oogway.resources.FeatureResource.updateFeature(FeatureResource.java:20)
	at stepdefs.metadata.FeatureSwitchSteps.update_feature(FeatureSwitchSteps.java:54)
	at sun.reflect.GeneratedMethodAccessor165.invoke(Unknown Source)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at cucumber.runtime.Utils$1.call(Utils.java:40)
	at cucumber.runtime.Timeout.timeout(Timeout.java:16)
	at cucumber.runtime.Utils.invoke(Utils.java:34)
	at cucumber.runtime.java.JavaStepDefinition.execute(JavaStepDefinition.java:38)
	at cucumber.runtime.StepDefinitionMatch.runStep(StepDefinitionMatch.java:37)
	at cucumber.runtime.Runtime.runStep(Runtime.java:300)
	at cucumber.runtime.model.StepContainer.runStep(StepContainer.java:44)
	at cucumber.runtime.model.StepContainer.runSteps(StepContainer.java:39)
	at cucumber.runtime.model.CucumberScenario.run(CucumberScenario.java:44)
	at cucumber.runtime.junit.ExecutionUnitRunner.run(ExecutionUnitRunner.java:102)
	at org.junit.runners.Suite.runChild(Suite.java:128)
	at org.junit.runners.Suite.runChild(Suite.java:27)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.runtime.junit.ExamplesRunner.run(ExamplesRunner.java:59)
	at org.junit.runners.Suite.runChild(Suite.java:128)
	at org.junit.runners.Suite.runChild(Suite.java:27)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.runtime.junit.ScenarioOutlineRunner.run(ScenarioOutlineRunner.java:53)
	at cucumber.runtime.junit.FeatureRunner.runChild(FeatureRunner.java:63)
	at cucumber.runtime.junit.FeatureRunner.runChild(FeatureRunner.java:18)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.runtime.junit.FeatureRunner.run(FeatureRunner.java:70)
	at cucumber.api.junit.Cucumber.runChild(Cucumber.java:95)
	at cucumber.api.junit.Cucumber.runChild(Cucumber.java:38)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at cucumber.api.junit.Cucumber.run(Cucumber.java:100)
	at org.apache.maven.surefire.junit4.JUnit4Provider.execute(JUnit4Provider.java:365)
	at org.apache.maven.surefire.junit4.JUnit4Provider.executeWithRerun(JUnit4Provider.java:273)
	at org.apache.maven.surefire.junit4.JUnit4Provider.executeTestSet(JUnit4Provider.java:238)
	at org.apache.maven.surefire.junit4.JUnit4Provider.invoke(JUnit4Provider.java:159)
	at org.apache.maven.surefire.booter.ForkedBooter.invokeProviderInSameClassLoader(ForkedBooter.java:384)
	at org.apache.maven.surefire.booter.ForkedBooter.runSuitesInProcess(ForkedBooter.java:345)
	at org.apache.maven.surefire.booter.ForkedBooter.execute(ForkedBooter.java:126)
	at org.apache.maven.surefire.booter.ForkedBooter.main(ForkedBooter.java:418)

[ERROR] Tests run: 9691, Failures: 0, Errors: 2, Skipped: 5, Time elapsed: 745.444 s <<< FAILURE! - in RunFunctionalTest
[ERROR] And a client changes the multi-tenancy-revoke_token_by_messo feature value to true and with context ANY, ANY, ANY, ANY(| SKY | GB | ANY | 404 | User with profile id \d+ does not have a token for client INVALID_CLIENT | 910 |)  Time elapsed: 0.012 s  <<< ERROR!
java.lang.RuntimeException: org.apache.http.NoHttpResponseException: funwap01.oogway.bskyb.com:80 failed to respond
Caused by: org.apache.http.NoHttpResponseException: funwap01.oogway.bskyb.com:80 failed to respond

[ERROR] | SKY | GB | ANY | 404 | User with profile id \d+ does not have a token for client INVALID_CLIENT | 910 |  Time elapsed: 0.012 s  <<< ERROR!
java.lang.RuntimeException: org.apache.http.NoHttpResponseException: funwap01.oogway.bskyb.com:80 failed to respond
Caused by: org.apache.http.NoHttpResponseException: funwap01.oogway.bskyb.com:80 failed to respond

[INFO] 
[INFO] Results:
[INFO] 
[ERROR] Errors: 
[ERROR]   org.apache.http.NoHttpResponseException: funwap01.oogway.bskyb.com:80 failed to respond
[ERROR]   org.apache.http.NoHttpResponseException: funwap01.oogway.bskyb.com:80 failed to respond
[INFO] 
[ERROR] Tests run: 9691, Failures: 0, Errors: 2, Skipped: 5
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 12:43 min
[INFO] Finished at: 2021-12-15T09:32:38Z
[INFO] Final Memory: 56M/909M
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-surefire-plugin:3.0.0-M3:test (default-test) on project component-test: There are test failures.
[ERROR] 
[ERROR] Please refer to /var/sky/build/jobs/oogway/branches/IPB-15149/workspace/component-test/target/surefire-reports for the individual test results.
[ERROR] Please refer to dump files (if any exist) [date].dump, [date]-jvmRun[N].dump and [date].dumpstream.
[ERROR] -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoFailureException
```

[Full log here](./16.log).

## Build 17 - Success

Build 17 was simply a re-build after 16, and so there is no reason why it should have worked, but it did.

[Full log here](./17.log).
