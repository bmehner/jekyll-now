---
layout: post
title: Testing Rest Clients With Wiremock
---
Nowadays with frameworks like JAX-RS it is simple to develop clients for REST APIs. The framework takes care of everything for you: Binding from object to JSON, validating and even doing the actual HTTP request. This is very comfortable to implement. Unfortunately it is difficult to test. Does the JSON really meet my expectations? You can not test this kind of code with a traditional unit test. You need a server for this! What if the server is developed by another team and is not available yet? All you got is the spec? And what if it is a third party service and we do not have an instance for testing? Continuous testing against a production environment? Not a good idea. [WireMock](http://wiremock.org/) is here to help you with this kind of scenarios.

<blockquote cite="http://wiremock.org">
    WireMock is a simulator for HTTP-based APIs. Some might consider it a service virtualization tool or a mock server.
</blockquote>

How does it work? Basically it fires up a Jetty server and provides you with a local REST service endpoint that you can teach to answer any requests you like on the fly. This way you can mock the real REST service and keep your calls local. Also it provides methods to verify your requests.

    @Rule
    public WireMockRule wireMockRule = new WireMockRule(
            WireMockConfiguration
                .wireMockConfig()
                .dynamicPort()
                .dynamicHttpsPort()
        );

Text

    stubFor(post(urlPathEqualTo("/your-local-url"))
                .withHeader("Content-Type", containing("application/json"))
                .withRequestBody(matchingJsonPath("$.customer[?(@.customerId == '4711')]"))
                .willReturn(okJson("{\"some\": \"value\"}")
                )
        );
        
        SomeClient someClient = new SomeClient("https://localhost:" + wireMockRule.port() + "/yourapp/your-local-url");
        someClient.doRestCall();

        verify(postRequestedFor(urlPathEqualTo("/your-local-url")));
        
