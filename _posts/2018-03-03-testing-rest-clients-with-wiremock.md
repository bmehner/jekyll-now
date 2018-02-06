---
layout: post
title: Testing Rest Clients With Wiremock
---

Text

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
        
        SomeClient someClient = new SomeClient();
        someClient.doRestCall();

        verify(postRequestedFor(urlPathEqualTo("/your-local-url")));
