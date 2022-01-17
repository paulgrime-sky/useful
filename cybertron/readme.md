

## `Invalid authorisation` error

We try to get a profile by identifier (header - `'X-Identifier: NsProfileId 40cb391a-c91d-4258-9883-b88f4dc533a6'`):

```
local git:(IPB-15148) ✗ curl --location --request GET 'http://localhost:9989/profile' \
--header 'Accept: application/vnd.aggregator.v3+json' \
--header 'X-SkyOTT-Provider: SKYSHOWTIME' \
--header 'X-SkyOTT-Proposition: SKYSHOWTIME' \
--header 'X-SkyOTT-Territory: PL' \
--header 'X-Identifier: NsProfileId 40cb391a-c91d-4258-9883-b88f4dc533a6' \
--header 'Authorization: Basic Y2xpZW50OnBhc3N3b3Jk'
```

and get an error:

```
{"message":"Invalid authorisation","service":"/authorisationservicecontainer$","errorcode":1305}
```

In `getprofile/gateway/src/main/scala/com/sky/getprofile/gateway/GetProfileFilters.scala`, see that the `authorizationFilter` will use the `authWithRoleRestrictionFilter` filter if there is an identifier header present.  This filter has a `roleRestriction` with the `"GET_PROFILE_BY_ID"` value, so we need to make sure that role is present in the authorisation service.

```java
  protected val authWithRoleRestrictionFilter = AuthFilter(serviceName, authenticate, authorise, roleRestriction = Some("GET_PROFILE_BY_ID"), errorCode = Option(1303))
  protected val authWithoutRoleRestrictionFilter = AuthFilter(serviceName, authenticate, authorise, errorCode = Option(1303))

  val authorizationFilter: SimpleFilter[Request, Response] = (request: Request, service: Service[Request, Response]) => {
    (SkyIdHeaders.extractTokenHeader(request), SkyIdHeaders.extractIdentifierHeader(request)) match {
      case (None, Some(_)) => authWithRoleRestrictionFilter(request, service)
      case (_, _) => authWithoutRoleRestrictionFilter(request, service)
    }
  }
```

The fix is to add `GET_PROFILE_BY_ID` to the `authorisation_client_roles` env var of the `authorisation-shared` container:

```
    authorisation-shared:
      image: sky-identity-docker-local.jfrog.io/cybertron/authorisation-shared:latest
      restart: always
      environment:
        authorisation_users: "client,rango_bridge,crmapi,activation_server_bridge,watson_aca_bridge,chat_gateway_bridge,dap_bridge,nowtv_njp_bridge,paymentsmanager_bridge,sbosports_bridge,umv_skygo_bridge,activationorchestrator_bridge,onetrust_bridge,destroyprofile_bridge,sas_bridge,client_profile_bridge,ulm_bridge,skyport_bridge,sas_roku_bridge,myskyapp_bridge,sas_google_adapter_bridge,gdm_bridge,boku_adapter_bridge,google_adaption_layer_bridge"
        authorisation_client_roles: "ACTIVATION, USERSTORE, USERSTORE_INTERNAL, PROFILES_EMAIL_SECURITY_TOKEN, PROFILE_DESTROY, RETRIEVE_PARTNER_LINK_ROKU, PROFILES_FIRSTPARTYDATA_BY_PROFILEID, GET_PROFILE_BY_ID"
        authorisation_onetrust_bridge_roles: "ACTIVATION, USERSTORE_INTERNAL, PROFILE_DESTROY"
        authorisation_gdm_bridge_roles: "PROFILE_DESTROY"
        authorisation_activation_server_bridge_roles: "ACTIVATION"
        authorisation_sas_bridge_roles: "USERSTORE"
        authorisation_roku_bridge_roles: "PROFILES_FIRSTPARTYDATA_BY_PROFILEID"
        authorisation_sas_roku_bridge_roles: "PROFILES_FIRSTPARTYDATA_BY_PROFILEID"
        authorisation_client_profile_bridge_roles: "USERSTORE"
        authorisation_ulm_bridge_roles: "GET_PROFILE_BY_ID"
        authorisation_skyport_bridge_roles: "GET_PROFILE_BY_ID, PROFILES_EMAIL_SECURITY_TOKEN"
        authorisation_myskyapp_bridge_roles: "PROFILES_EMAIL_SECURITY_TOKEN"
        authorisation_boku_adapter_bridge_roles: "GET_PROFILE_BY_ID"
        authorisation_sas_google_adapter_bridge_roles: ${AUTHORISATION_SAS_GOOGLE_ADAPTER_BRIDGE_ROLES}
        authorisation_google_adaption_layer_bridge_roles: "EXCHANGE_GOOGLE_JWT_FOR_OAUTH"
        CYBERTRON_HTTP_LOG_LEVEL: ${CYBERTRON_HTTP_LOG_LEVEL}
        CYBERTRON_BASE_LOG_LEVEL: ${CYBERTRON_BASE_LOG_LEVEL}
```
