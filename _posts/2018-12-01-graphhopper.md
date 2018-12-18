---
layout: post
title: Using GraphHopper for Routing with OpenStreetMap-Data 
---

![GraphHopper-Logo](/images/graphhopper-logo-small-300x51.png)

If you want to do routing in your application (the kind of routing that involves streets, crossing, ...) you can use for example [Googles Direction API](https://developers.google.com/maps/documentation/directions/intro). It's quality is unmatched and it is usually a very good solution for a lot of requirements. But there are some downsides. Google allows only a limited amount of requests. It is a WebService, so there is some overhead for all the network stuff going on. And last but not least if you have to send personal information like the addresses of your customers you might be violating the General Data Protection Regulation of the EU. So it might be good to have an alternative. One of those alternatives is GraphHopper. This software and its usage is the subject of this post.

In one of my recent projects we needed to calculate the distance between an address of a customer and a list car repair shops. But the distance should not be calculated as the crow flies but instead the real distance to actually drive there by car. The exact requirements were:

* Easy to use from a Java application
* Fast calculation: The list of car repair shops could be as long as 60 different ones. The distance to all of those should be calculated in less than 5 seconds.
* No personal information should be sent to a third party because we need to stick to the General Data Protection Regulation of the EU.
* Exact calculation of the distance as the car drives.

GraphHopper fullfills all this requirements. It's a routing engine implemented completely in Java. You can run it embbedded in your Java application. So there is no need to send any information over the network. It's also extremly fast.

## Maven

If you are using Maven you can import GraphHopper like this:

````XML
     <dependency>
          <groupId>com.graphhopper</groupId>
          <artifactId>graphhopper-core</artifactId>
          <version>0.11.0</version>
     </dependency>
````
## Usage

````Java
        GraphHopper graphHopper = new GraphHopper().forServer();
        graphHopper.setMemoryMapped();
        graphHopper.setDataReaderFile(OSM_FILE.getAbsolutePath());
        graphHopper.setGraphHopperLocation(GRAPHHOPPER_WORKINGDIR.getAbsolutePath());
        graphHopper.setEncodingManager(new EncodingManager(FlagEncoderFactory.CAR));
        graphHopper.getCHFactoryDecorator().setWeightingsAsStrings("shortest", "fastest");
        graphHopper.clean();
        graphHopper.importOrLoad();
````


````Java
              GHRequest request = 
                new GHRequest(startingPointLatitude, startingPointLongitude, destinationLatitude, destinationLongitude)
                        .setWeighting("fastest")
                        .setVehicle("car");

                HintsMap hints = request.getHints();
                hints.put("instructions", false);
                hints.put("calcPoints", false);

                GHResponse response = graphHopper.route(request);

                if (!response.hasErrors()) {
                    double distance = response.getBest().getDistance();
                } else {
                    ...
                }
````            








## References

 * GraphHopper Main Site: https://www.graphhopper.com/
 * GraphHopper Routing Engine: https://github.com/graphhopper/graphhopper
