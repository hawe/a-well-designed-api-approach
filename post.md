The design of an API has different design phases and decision making that will determine how well your API is designed and the usability of your API. Without having a good strategic plan in place from the beginning can make it difficult to sustain the API over time. This post is to help you in this decision making process from the start to end of the design of the API, which leaves it up to you on how you implement your API and offer it for consumption by others that want to take advantage of the data that is offered from the API that you have created and designed.

#API Overview

An application programming interface (API) is a set of routines, protocols, and tools for building software applications. Operations, inputs, outputs, and types are the characteristics that make up an API. One must consider modularity to be the most important factor when starting to construct the architecture of the API so that the functionality is separated from the implementation. In the context of this post, which is based on web API's, an API is a specification of remote calls exposed to the API consumers.

Before we get into the design of an API, here are some example use cases for API's that are used on the web:

1. Photo sharing from social networking sites
2. Being able to share content
3. Content embedding
4. Sharing live comments
5. Video content embedding
6. Sharing content from one social community to 3rd party applications
7. Data about a person that can be shared across multiple applications

So that gives a good basis to dive into designing an API and what to take into consideration before, during, and after.

#Data Formats

---
One of the first decisions when designing your API is the data format that will represent the API. How the data is represented with be the decision maker on how your API is structured overall and how it will handle the requests and responses. These days the most popular data formats are JSON, but the use of XML is also an option. JSON has become so popular because of the ease of use with the data that is past back and forth to and from the API. What can drive this decision is one that you should consider in the eyes of the audience because it is the data format that will dictate how the information is understood and what is the easiest way for that data to be handled by those who are making use of the API. There certainly are different ways to handle the data depending on the structure of your application and the stack that is being used. I bring up JSON simply because I am using JavaScript/NodeJS/MongoDB as an example. 

During a review of this post, it was brought to my attention that JSON is not the most popular and easiest to use data format of all options that are available when designing an API. Other options, such as protocol buffer, SBE, thrift, and cap'n proto are more efficient in terms of how data is passed back and forth because the data is in binary format and is less stressful on the CPU, memory, and bandwidth footpint. That being said, I can agree that there are situations where this can be the case, but I also believe that JSON is the best way to handle data that is passed back in forth when dealing with an application that is built on top of JavaScript & NodeJS. I do not hold the knowledge level to talk about how other data formats would be better, so I will not try to. If there are others that can show these differences in performance gains if being used with the application stack that I am discussing, please share for the knowledge gain of those who are to read this. If you need a comparison of the different data serialization formats, please have a look at [Comparison of data serialization formats](http://wikipedia.com/en/Comparison_of_data_serialization_formats).

On a side note, technically JSON is not considered to be a RESTful API. The reasoning for this is that a key constraint for a RESTful API is that it must use hypermedia formats (the [HATEOAS](http://www.wikipedia.com/en/HATEOAS) constraint). JSON is not a hypermedia format. What this means is that there is no defined way that JSON knows how to deal with link discovery. The W3C has a recommendation for JSON-LD, which is a JSON-based serialization for linked data. More about [JSON-LD 1.0](http://www.w3.org/TR/json-ld/) is available in the W3C recommendation.

##Dealing with data formats
The process of dealing with data format is to make sure that the data has been put into a language that one computer can send to another that will understand that data. This is a reason why JSON has become so popular because the use of key value pairs makes it easy for both computers to understand that data stored in the key value pairs. Lets talk about the data that is represented by JSON.

The key value pairs that JSON holds are keys, and it is these keys are attributes that represent the object being described. The attributes tell what the object contains and what information can be taken from that object. When the attributes are needed from your API, it is the way tha the data is stored that makes it easy to consume because of the ability to look at the values in a sentence like format.

If you have worked with JSON already, you know that it has a good way of putting data in a hierarchy that can make sense. For example, in a dictionary you can put an object inside of another object. Then you have key values that you have available not only from the parent object, but any object(s) that are nested inside of that object.

##Headers

When passing data back and forth from computer to computer, there is important information that one computer needs to know about the data being sent from another computer. This information can be found in the headers of that envelope being sent so to speak. When the receiving computer receives the envelope, it will look at the headers for the information about the request or the response. One of the headers is the `Content-Type` that one server sends with a certain data type to the other computer. When that content type is sent, the receiving computer will determine if it can accept the data type that is found in the body of the message inside of the envelope. One of the upsides is that if there is an error inside of the message, there is a fallback using the header `Accept`.  In the HTTP/1.1 standard has a large list of headers that may be used in server-driven negotations - [see HTTP Content negotation - MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Content_negotiation). Along with `Accept`, you also have `Accept-Charset`, `Accept-Encoding`, `Accept-Language`, and `User-Agent`. The content negotiation - defined by the HTTP specification - makes it possible to serve different versions of a document so the user agents can specify the version that best suites their capabilities - see [RFC-2295 - Transparent Content Negotiation in HTTP](http://tools.ietf.org/html/rfc2295). More references are given at the end of this post.

During the time of the reading of this note taking, I found myself asking “Is there a preference when building an API as to whether you use JSON or XML?”. The answer is obvious because of the JavaScript, Mongo, Node stack that I work with. To me, XML is more along the lines of SOAP, which I have no preference for nor have I worked with it much.

#Architectual Design Decisions

---
When looking at the design of an API from this perspective, it is from a standpoint of how the API is used for hitting endpoints.

API’s have various decisions during the architectural design process, and these decisions determine the semantics of the API. Some of these decisions we will look at include: REST, requests and responses, resources, URL’s, endpoints, HTTP verb, **GET**, **POST**, **PUT**, **DELETE**.

##HTTP Verbs

Let me take a moment and give more details of these HTTP verbs. 

###GET

The first one listed above is the **GET** request. This is used to retrieve a representation of a resource. This can be a collection of a resource or a single item.. Such an example would be `/customers` for a collection, and `/customers/{id}` for a specific item. **GET** requests are not meant to change - read only - data. Some examples could include : `/patients/`, `patients/1001`, and `patients/1001/prescriptions`. 

###PUT

The **PUT** request is utilized for its updating capabilities. This request is for a new representation of a known resource. **PUT** is to be considered an unsafe operation as it modifies the state on the server. The reason for this to be unsafe is that if you update or create a resource and then try to update the resouce again, the resource is still there and will have the same state as was there from the first **PUT** call. Examples of **PUT** include: `/patients/1001`, `/patients/1001/prescriptions/5534`. More information on these methods and others can be found from the resources listed at the bottom.

###POST

The **POST** request is a new request that the web server accepts with the entity that is enclosed and identified by the URI. It is used to create subordinate resources, which in essence means it is a subordinate to some other resource. This method is not considered to be a *safe* method as making identical requests will likely result in two resources containing the same information. Examples of the **POST** include:

`/patients`
`/patients/1001/prescriptions`

###DELETE

The **DELETE** request is fairly straight and easy to understand. It is used to delete a resouce that is identified by a URI. The request will delete a resource, and repeatedly calling DELETE on that resource will always end up the same. Some examples of **DELETE** include:

`/patients/1001`
`/patients/1001/prescriptions`

###TRACE

The **TRACE** request will send back the received request allowing the client to see if there have been any changes or additions by intermediate servers.

###OPTIONS

The **OPTIONS** will return the methods of HTTP that the server supports for the specified URL. 

###CONNECT

The **CONNECT** request converts the connection to a transparent TCL/IP tunnel, which is usually used to facilitate SSL encryption. 

###PATCH

The **PATCH** request applies partial modifications to a resource.

##Safe Methods

Methods that are by convention considered to be *safe* are those that have no side effects and do not change the state of the server. These methods are considered to be: HEAD, GET, OPTIONS and TRACE). When being considered not to have side effects, what this means essentialy is that there is not much harm that can be done other than the likes of logging, caching, and other small harmless effects. 

Those that are not listed above that are considered to be *safe* are considered for the fact that they can cause state changes to the server and/or open the server to mailicious attacks if programming is careless.


#Thought Process

This first thought process has to be how easy can the API be so that other computers can work with the data delivers from the API. This brings up the decisions that were touched on in the previosu paragraph. One of the most used approaches is Representational State Transfer – REST – because it is an open approach for lots of conventions that are used for consumers of your API. How this transfer is made is determined by the resources made available by your API. 

##Example 

These are the nouns of the API. Examples could be such things as Patient, Doctor, etc. Take note to the work **things** that I used to describe the resources. The resources represent things, which are nouns, that make it easy to understand the main focus of what your API makes available. These things can be found everywhere around us in the real world. Knowing this can help to plan the processes of typical interactions that happen in the real world that will help you to determine the resources that are needed. The API data should act like a script from a movie where you have to determine the actors, plots, and story lines that go into the movie.

To continue with the real world example of a movie, we need to look at the script and what roles these actors play. It is the roles of these actors that determine how well the movie is accepted by those who are watching – the receiving computers. This is what we can semantics so to speak in the determination of how to represent the resources of the API. The URL’s have to be understanble from a semantic point of view just like it is clear what role the actor is playing in the moview.

Let us take a step back into the API lingo to get an understanding of how important it is to realize the structure of a REST API. There are patterns that have been accepted by API authors that have shown the semantically correct way to make your resources available. The URL is plural for the resource name, in our case we could have something like `/doctors`. Once we have that we then add on a unique identifier for the resource name. Lets say we have a doctor who has the ID of `4321` in the system and we want to make his information available. We would have a resource plus identifier so it is understood that this is the doctor we are wanting information for. So this will now look like so: `doctors/4321`. So what it is that we have here? We have an endpoint. This endpoint is important because we then start to perform actions on the endpoint. How do we set up the actions for this?

#Semantics

There are a couple well thought out semantics for handling actions to perform and those are determined by the endpoints. Those that are plural are for *listing and creating*. Those that end with a plural plus unique identifier are for *retrieving, updating, and cancelling*. One the semantics of your API are in place, you can then determine the data that goes with each of the endpoints. One thing to take note of when working with REST is that it has an open ended approach, which allows the deisgn of the API up to the person that is design it. At times, this can have a negative approach. There is a good amount of details that goes into the design of an API, and if it is thought out well, one will have good resources and endpoints that clearly define the semantics of the API.

Key Points:

1. Resources are for clarifying the nouns of the API
2. Endpoints are used to determine the HTTP verbs

#Maintenance

An API, or set of API's, are the center piece of your application and needs to have a set of principles surrounding the maintenance and versioning of the API.

##Versioning

Versioning is an important decision making process that must take into consideration what state the API will be in when changes occur. If one is to use the [semver](http://semver.org/) approach, the approach would be to follow a strict set of rules:

![Semantic Versioning](http://imageshack.com/a/img537/9536/YvODxA.jpg)

###Major
Major version changes are when you make incompatible API changes. When a major version change occurs, it must be made known to public consumers ahead of time about these changes so they can be aware of what will no longer be available. One rule of thumb that should be considered when there is going to be a major change is to deprecate so that consumers will know to start making changes based on the changes of the API. Knowing that the API will have changes that will supersede the previous version will give the consumers of your API time to adjust before the previous version is no longer available.

###Minor
A minor version is when you make changes that add functionality that is backwards compatible. This can be thought of with the above description of deprecation before the changes to the new version no longer support the changes to the previous version. The changes to the minor version will still allow for use of all parts of your API as is, only with new added functionality that will take the place of deprecated functionality that will be replaced with the new major version.

###Patch
Patches are bug fixes that improve the functionality of the API. 

In cases whre semver is used, the MAJOR, MINOR, PATCH follow the X.Y.X approach where a major version change will take your version from 1.0.0 to 2.0.0. If there is a minor change, your version will take the approach of adding the functionality changes to the current version. This would be something like an example of 3 functionality changes which would take your version from 1.0.0 to 1.3.0. Take note that when you make a change one level higher, the lesser levels will go back to 0. The patches take the form of increasing your version to something like 1.1.10 if there have been 10 patches to the current 1.1 version. Again, if your functionality changes, lets say to 1.2, your patch will go back to 0. 

Documentation of these changes is important to the public so that it is known what changes/bugs have been fixed in each version change. Take the time to read more about the concept behind [semver](http://semver.org/).

#How to Communicate in Real-Time

---
The design of the API has no real significance if there is no way to communicate from the user to the client and then to the server. The integration should be straightforward that will allow for sharing to link together the systems involved in the communcation process. What is needed for this communication to happen?

There is a continuous real time communication process that goes on when your API is being used. What happens for the most part is that when something happens on one computer, the other will automatically update. One example of this is when a person interacts with the client and wants to update the server. One thing I want to mention before moving on that I may have overlooked is how to look at the role of the API on the computer. Just like any computer that you interact with, you have programs that you use. The API is considered to be a program, and it sits on the computer, which we call the server, and it is a stand-alone program that waits for the server to talk to it and asks for information that was sent to it by the client. What we mentioned above before this, when we were talking about the person interacting with the client wanting information from the server, this is considered client-driven. There is also server-driven. Server-driven differs in that a user does something and needs the client to be aware of the change, which means that with the design of a good API, the client should be the only initiater of communication. When this design has been correctly implemented, the client will know exactly when data has changed, which allows it to call the API right away letting the server know about the data change.

At the time of this writing, we know that there is no such option for "true" real time communication in web applications. The likes of [WebSockets](https://developer.mozilla.org/en-US/docs/WebSockets), ServiceWorker](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorker_API), and [WebRTC](http://www.webrtc.org/), are bringing us closer to having this capability, but real time communication does have falacies that were made known to me from feedback during a review of this original post. If you would like to see an example of WebRTC in action, you can play around with the [Video Chat Application](https://apprtc.appspot.com/)

##Falacies

Falacies of distributed computing are assumptions that are invariably made by those new to distributed computing. These falacies are:

* The network is reliable
* Latency is zero
* Bandiwdth is infinite
* The network is secure
* Topology does not change
* There is one administrator
* Transport cost is zero
* The network is homongeneous

It is easy for me to say that I fall into the category of one who is new to distributed computing and have much to learn about. That being said, I still think that we - on the client-side - are getting closer and closer to being able to deliver real time communication. NodeJS has given us the capabilities to deal with these falacies more and more, which can be read about from a blog post that was done by Nodejitsu - [Fault tolerant applications in nodejs])(http://blog.nodejitsu.com/fault-tolerant-applications-in-nodejs/). There is another [blog post](http://www.lshift.net/blog/2012/04/02/the-fallacies-of-distributed-computing/) that talks about NodeJS not being able to isolate us from the effects of the infrastructure they utilize 

#Real Time Options

Of the two options, client-driven and server-driven, it is harder on the integration of server-driven because of the possiblity of numerous requests that could be coming in at the same time and the server needing to know how to handle all of these requests. How can this be handled? 

##Polling
To make this point clear, in the new era of single page applications, there is a need for a user to feel that everything is happening in real time. If the application has a large number of users at the same time making requests to the server, this will cause the server to become overloaded with requests. We don't necessarily want to have the server doing all of the heavy lifting for the application, so we can make use of client-side localStorage and sessionStorage. This way, once a package is sent back by the server, the data in the package can be stored on the client-side, not having to continue to send data back and forth for each and every request. Putting the load on the *client* allows for there to be less of a delay in the data being available to the user because it already has been stored on the client. The server will not need to handle the request again if the data has not changed and can handle other requests that are needing to be packaged and sent back with newer data.

The options of polling can *make* the real time communication process *look and feel* smoother to the user. There are two familiar types of polling: regular short interval polling and long polling. For example:

![Polling](https://www.safaribooksonline.com/library/view/restful-java-patterns/9781783287963/graphics/7963OS_06_01.jpg)

When it comes to regular polling, the client asks for requests from the server on the status of the request and always looking for the response, even if it has not changed. When it comes to polling, the more that is done by the client, the smoother the real time communication feels. Unfortunately, there are drawbacks to this, simply because there could be no updates from the request. This at times can make the client inneficient in the real time communicaiton process. So how can this be handled for a better real time communication process?

##Long polling 

Long polling comes in handy much more than short polling because the server does not send back a request until there is one. So the client can ask for the requests, but there will be no responses from the server until the response has changed. For example: 

![Long Polling](http://www.pubnub.com/blog/wp-content/uploads/2014/11/HTTP-Long-Polling-Diagram1.png)

##Webhooks

You still looking for other ways for that real time communication process?. Have you noticed a trend going on lately with web apps that have API’s available? They are offering webhooks. Examples include the likes of GitHub, Bitbucket, and more (see references below). Why is this? It is because it is a process where the client makes both the requests and at the same time listens for them, which allows the server to push updates. What makes this different? It is because the client becomes both what is it and also acts as the server. 

![Twilio Webhool](https://www.twilio.com/blog/wp-content/uploads/2012/03/network-diagram.png)

This makes it where the server is stateless and has less of a strain on the request and response procedure. You probably have seen this with the likes of Twitter and Instagram, where they use webhooks. This can be seen because you have a provided **callback URL** that can receive events, and the server for a place that the person can enter the callback URL.

##Subscription Webhooks

The polling, long polling, webhooks dynamics of the API is increasingly getting faster and faster for the real time communication process. One type of process that is making its ways into the limelight is Subscription Webhooks, which is a process where the client tells the server what events it is interested in and the callback URL that it should send the updates to. For more information, you can read about [REST Hooks](http://resthooks.org/docs) and [PubSubHubbub](http://code.google.com/p/pubsubhubbub). There also is the [W3C Push API](http://www.w3.org/TR/push-api/), which is described in the abstract of the working draft as:

> The Push API provides webapps with scripted access to server-sent messages, for simplicity referred to here as push messages, as delivered by push services. A push service allows a webapp server to send messages to a webapp, regardless of whether the webapp is currently active on the user agent. The push message will be delivered to a Service Worker, which could then store the message's data or display a notification to the user. This specification is designed to promote compatibility with any delivery method for push messages from push services to user agents.

Here is a diagram of the Push API from the W3C draft:

![Push API](http://www.w3.org/TR/push-api/sequence_diagram.png)

The take away from this section is how communication can be real time between the server and the user with the client acting as the middle man doing all of the requesting.

#Design

There are various decisions that go into the design of the API, and you can find some great examples online showing API documentation, such as Facebook and Twitter. In regards to the programming languages that are used for the way that the API is communicated with, this is all dependant on the knowledge that your team has and what is the best fit for the integration of your API. This is not as important as the design of the API itself. As long as your have a well structured, semantically correct design for your API, all of the rest is sugar on top of the superstar of your web application.

There is also the importance of authentication. This topic is important as to how access is granted for communication with your API. That will be in another post. Please take note that these are notes that are from a series that Zepier put on in regards to the design of an API, and you can go to their site to find out for yourself how they view a well thought out API design.

#Authentication

I originally left this out of this post because I felt that it was a whole different post in itself, but what would be a good API without authentication. This topic can be very lengthly to read and get up to speed about, but the details of how authentication is handled can make a huge difference on how you design your API. 

##OAuth 2.0
One of the most known and used authentication frameworks used these days is OAuth 2.0. This frameworks enables a third-party application to obtain limited access to an HTTP service. To complete this description of OAuth 2.0, here is the abstract from the Internet Engineering Task Force:

> The OAuth 2.0 authorization framework enables a third-party
   application to obtain limited access to an HTTP service, either on
   behalf of a resource owner by orchestrating an approval interaction
   between the resource owner and the HTTP service, or by allowing the
   third-party application to obtain access on its own behalf.

The client-server authentication traditional model is made up on the client requesting an access-restricted resource on the server by authenticating with the server using the resource owner's credentials. In order to provide third-party applications access to restricted resources, the resource owner shares its credentials with the third-party. In this model there are several problems and limitations that OAuth addresses. The issues are addressed by adding a layer of authoritzation and separating the role of the client from the resource owner. This layer of authentication is where the client obtains an access token that is a string symbolizing the specific scope, lifetime, and other access attributes. It is the authorization server that is responsible for issuing the access token with approval from the resource owner. 

###OAuth Roles

There are four roles that are defined by OAuth:

1. Resource owner
2. Resource server
3. Client
4. Authorization server

The resource owner is an entity that is capable of granting access to a protected resource. The protected resource is hosted on the resource server which is capable of accepting and responding to protected resource requests using access tokens. The client is an application that is making protected resource requests on behalf of the resource owner and with its authorization. After successfully authenticating, the authorization server issues an access token.

![OAuth Abstract Protocol](http://imageshack.com/a/img905/865/QhLUBq.png)

###Tokens

There are 2 types of tokens used in OAuth: Access token and Refresh token.

####Access Token

Access tokens are credentials used to access protected resources. Tokens are specific to scopes and durations of access, granted by the resource owner, and policed by the resource server and authorization server. 

The access token provides an abstraction layer, replacing different authorization methods - username/password for example - with a single token understood by the resource server. The abstraction allows for the issuing access tokens to be more restrictive than the authorization used to obrain them, and at the same time removing the resource server's need to understand the wide range of authentication methods. 

####Refresh Token

Refresh tokens are credentials used to obtain access tokens. When the current access token has become invalid or expires, the refresh token is issued to the client by the authorization server and used to obtain new a new access token. Issuing a refresh token is optional at the discretion of the authorization server. Unlike access tokens, refresh tokens are intended for use only with authorization servers and are never sent to resource servers. 

![Tokens](http://imageshack.com/a/img538/3070/ZwZ4RC.png)

####More Info

There is a slew of information that can be read about in regards to OAuth 2 from the Internet Engineering Task Force - [The OAuth 2.0 Authorization Framework](https://tools.ietf.org/html/rfc6749)

#Frameworks

This is a short list of frameworks that you may want to look into for your API. By no means are these the only frameworks, nor are they in any particular order. If you know of some that are not listed, please share.

1. [OAuth 2.0](http://oauth.net/2/)
2. [Swagger](http://swagger.io/)
2. [Django REST framework](http://www.django-rest-framework.org/)
3. [Restlet](http://restlet.com/)
4. [List of REST frameworks categorized by supported languages](https://code.google.com/p/implementing-rest/wiki/ByLanguage)


#Summary

Having a well thought out API is important in how you are able to give access to your consumers of the data that you make available. Not only so, but it also gives for creating a better application infrastructure. It will be the details that are put into the API design that can then give you the base knowledge of how to code your application around the API. 

Your consumers are the ones that will be most interested in your API, and needing to know how to use it will be something that will always be the starting point. Having good documentation for your set of API's will better guide those who are wanting to consume your data. Most, if not all, of the larger companies that have their own API's have a well structured wiki of some sorts that gives overiews, examples of endpoints, and more in regards to all of the endpoints that are available as well as how to reach those endpoints. There also are examples of how different programming languages can go about writing code against the API. With this said, be sure along the way to continue to document and give as much information that you can provide for those who need to know. 

#References

* [RFC-7231 - Hypertext Transfer Protocol: Semantics and Protocol](http://tools.ietf.org/html/rfc7231)
* [Header Field Definitions](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14)
* [Using HTTP methods for RESTful Services](http://www.restapitutorial.com/lessons/httpmethods.html)
* [WebSocket Protocal](http://tools.ietf.org/html/rfc6455)
* [Falaciies of distributed computing](http://en.wikipedia.org/wiki/Fallacies_of_distributed_computing)
* [What is HTTP Long Polling](http://www.pubnub.com/blog/http-long-polling/)
* [Webhooks - Read the docs](http://docs.readthedocs.org/en/latest/webhooks.html)
* [Hypertext Transfer Protocol](http://www.wikipedia.com/en/Hypertext_Transfer_Protocol)