## An introduction to modern software architecture pattern

Here is a summary of the book Software Architecture Pattern, contains my thoughts and viewpoints.

An architecture pattern describes the top-level mechanisms of a software product, its ultimate goal is to decouple each component in order to ease the process of development, deployment and the future maintenance. Application without an architecture pattern is generally tightly coupled and difficult to change. 

Choosing a suitable architecture pattern is a necessary fundamental structural work for the development that means costly to change once implemented. 

There are 5 architecture patterns described in this article as follows: 

### 0x00 Layered architecture pattern

The layered architecture pattern also called n-tier architecture pattern is the widest used architecture pattern in Java EE applications. Most Java Web applications especially using the Spring frameworks is based on layered architecture pattern. Components within layered architecture pattern are organized into horizontal layers, each layer performs a specific role in this application. 

Most Java Web applications is organized by the following layers from top to bottom as follows **presentation layer, business layer, persistence layer, and database layer**. The business layer also commonly split into **controller layer** and **service layer**.

Each layer plays its due role in applications and no matter to care how the other layers were implemented. The change in one layer does not affect other layers unless the interface between two layers was changed. For example, the presentation layer only needs to display the information on a screen, it doesn't need to care how the business layer works, and if the business layer changed, there is nothing else to do with the presentation layer. 

There are two modes of a layer within layered architecture pattern, that is **open layer** and **closed layer**.

The **closed layer** means a request or data stream must go through the layer right below it to get to the next layer below that one, in the other word, this layer cannot bypass.

In contrary, the **open layer** means the request or data stream can bypass this layer and go directly to the layer below it.  

So, which layer should be designed as an open layer or closed layer? This book proposed an easy approach that is 80-20 rule:

> It is typical to have around 20 percent of the requests as simple passthrough processing and 80 percent of the requests having some business logic associated with the request. However, if you find that this ratio is reversed and a majority of your requests are simple passthrough processing, you might want to consider making some of the architecture layers open, keeping in mind that it will be more difficult to control change due to the lack of layer isolation.

Now, let us consider a question. We are designing a Java Web server-side (or backend) program with layered architecture pattern. We usually split this application into those layers as follows Controller- >Service (optional)- >Persistence- >Database, an interface will be provided in current layer and be called by upper-layer. Both the Controller layer and the Service layer perform business logic. The Controller layer does works related to validating HTTP parameters, deciding which service to call with what parameters, and managing the HTTP session or request response. The Service layer performs reusable application logic and generally is transactional. Oh, it's right, in theory, isn't it? But, sometimes, I found that Service layer does nothing except simply pass through data to the next (or below) layer, especially for those information systems that only do CRUD operations. So, is the service layer necessary in those applications?

In above quotation referred to the 80-20 rule used to decide which layer should be an open layer in layered architecture pattern. When we design a single service class, firstly count how many methods in this class should be provided and then count how much method is just passthrough data to the next layer. If it has over 80 percent of methods in current class just passes through data and requests, apparently, this service class is redundant, just remove it and then implement business logic in the controller and let this controller can go directly to the layer below service layer (persistence layer in common). I don't think one single class in service layer can be designed as "open" service class. Then repeat it for all service class you want to design. Finally, if over 80 percent of service classes was removed, I think, the service layer can be totally removed in your application. Nevertheless, If the ratio reversed, there's 80 percent of service class do specific business logic, make the service layer open, and only remove those service classes contained over 80 percent of methods in it that processing no business logics.

**Remember: the open layer will let you more difficult to control change due to the lack of layer isolation.**

