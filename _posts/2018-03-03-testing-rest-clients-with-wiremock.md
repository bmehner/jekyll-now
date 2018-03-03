---
layout: post
title: Testing Rest Clients With Wiremock
---
<img src="http://wiremock.org/images/wiremock-concept-icon-01.png" style="float:right;"/> Nowadays with frameworks like JAX-RS it is simple to develop clients for REST APIs. The framework takes care of everything for you: Binding from object to JSON, validating and even doing the actual HTTP request. This is very comfortable to implement. Unfortunately it is difficult to test. Does the JSON really meet my expectations? You can not test this kind of code with a traditional unit test. You need a server for this! What if the server is developed by another team and is not available yet? All you got is the spec? And what if it is a third party service and we do not have an instance for testing? Continuous testing against a production environment? Not a good idea. [WireMock](http://wiremock.org/) is here to help you with this kind of scenarios.

<blockquote cite="http://wiremock.org">
    WireMock is a simulator for HTTP-based APIs. Some might consider it a service virtualization tool or a mock server.
</blockquote>

How does it work? Basically it fires up a Jetty server and provides you with a local REST service endpoint that you can teach to answer any requests you like on the fly. This way you can mock the real REST service and keep your calls local. Also it provides methods to verify your requests.

To use WireMock in your unit tests simply add the following jUnit-Rule to your test:

    @Rule
    public WireMockRule wireMockRule = new WireMockRule(
            WireMockConfiguration
                .wireMockConfig()
                .dynamicHttpsPort()
        );

This Rule takes care of setting the server up for you. It supports a bunch of options. You can specify fixed ports for HTTP and HTTPs for example. Alternatively you can use a dynamic port like in the example above. This way WireMock determines a free port dynamically on your machine and binds the Jetty server to it. I recommend using dynamic ports if you run your tests in a CI environment. If you use fixed ports you can run into trouble from time to time if builds are running in parallel on your CI server.


Your actual test now looks something like this:

    stubFor(post(urlPathEqualTo("/your-local-url"))
                .withHeader("Content-Type", containing("application/json"))
                .withRequestBody(matchingJsonPath("$.customer[?(@.customerId == '4711')]"))
                .willReturn(okJson("{\"some\": \"value\"}")
                )
    );

    SomeClient someClient = new SomeClient("https://localhost:" + wireMockRule.httpsPort() + "/yourapp/your-local-url");
    someClient.doRestCall();

    verify(postRequestedFor(urlPathEqualTo("/your-local-url")));
        
In this example the mock waits for post requests to the URL `/your-local-url`. It answers with a HTTP 200 ok and the JSON content `{"some": "value"}` if the request has a content type header of `application/json` and the body contains a customer with a customerId of 4711. Note the use of `wireMockRule.httpsPort()` when using dynamic ports.

WireMock has a ton of funtions to verify your requests. It needs a little bit getting used to the way since all the verification is either done up front via the `stubFor()` or at the end of the test with the `postRequestedFor()`.

For more information about this useful testing tool see the [Documentation of WireMock](http://wiremock.org/docs/).
