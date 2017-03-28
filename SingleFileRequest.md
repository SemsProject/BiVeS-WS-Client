Single File Request 
====================

* get information about single files
* see also [/src/test/java/de/unirostock/sems/bives/webservice/client/ClientSingleFileExample.java](https://github.com/SemsProject/BiVeS-WS-Client/tree/master/src/test/java/de/unirostock/sems/bives/webservice/client/ClientSingleFileExample.java)

To initiate a single file request create an object of the type [Bives/SingleFileRequest](https://github.com/SemsProject/BiVeS-WS-Client/tree/master/src/main/java/de/unirostock/sems/bives/webservice/client/BivesSingleFileRequest.java) ([JDOC](http://jdoc.sems.uni-rostock.de/BiVeS-WS-Client/de/unirostock/sems/bives/webservice/client/BivesSingleFileCommands.html)), passing the link to the model or the XML representation of the model:

```java
BivesSingleFileRequest request = new BivesSingleFileRequest("http://.../path/to/model.xml");
// alternatively
request = new BivesSingleFileRequest("<sbml> .. </sbml>");
```

afterwards you need to define the operations to be executed on the web server by [adding commands](http://jdoc.sems.uni-rostock.de/BiVeS-WS-Client/de/unirostock/sems/bives/webservice/client/BivesRequest.html#addCommand%28java.lang.String%29) (commands are available as static fields in [Bives/SingleFileRequest](http://jdoc.sems.uni-rostock.de/BiVeS-WS-Client/de/unirostock/sems/bives/webservice/client/BivesSingleFileCommands.html#field_summary)). For example to get some *meta information* about a CellML document and *flatten* it at the same time you would do the following:

```java
request.addCommand(BivesSingleFileRequest.COMMAND_DOCUMENT_TYPE);
request.addCommand(BivesSingleFileRequest.COMMAND_META);
request.addCommand(BivesSingleFileRequest.COMMAND_SINFLE_FLATTEN);
```

(see also [all constant values](http://jdoc.sems.uni-rostock.de/BiVeS-WS-Client/constant-values.html))

To perform the request you need to instantiate a [BivesWs](http://jdoc.sems.uni-rostock.de/BiVeS-WS-Client/de/unirostock/sems/bives/webservice/client/BivesWs.html) object by passing the URL to the BiVeS web service you want to use (here we're using the web service of the [SEMS project](https://sems.uni-rostock.de/), available at <http://bives.sems.uni-rostock.de/>).
This object is able to communicate to the web service, just pass the request to the [performRequest](http://jdoc.sems.uni-rostock.de/BiVeS-WS-Client/de/unirostock/sems/bives/webservice/client/BivesWs.html#performRequest%28de.unirostock.sems.bives.webservice.client.BivesSingleFileRequest%29) method. The result will be a [Bives/SingleFileResponse](http://jdoc.sems.uni-rostock.de/BiVeS-WS-Client/de/unirostock/sems/bives/webservice/client/BivesSingleFileResponse.html):

```java
BivesWs bives = new HttpBivesClient("http://bives.sems.uni-rostock.de/");
BivesSingleFileResponse result = bives.performRequest(request);
```

The returned [Bives/SingleFileResponse](http://jdoc.sems.uni-rostock.de/BiVeS-WS-Client/de/unirostock/sems/bives/webservice/client/BivesSingleFileResponse.html) object provides methods to get the result of that response. Here for example you can get the requested XML code of the flattened model by passing the *flatten command* to the [getResult](http://jdoc.sems.uni-rostock.de/BiVeS-WS-Client/de/unirostock/sems/bives/webservice/client/BivesResponse.html#getResult(java.lang.String)) method:

```java
String flattenedModel = result.getResult (BivesSingleFileResponse.COMMAND_SINFLE_FLATTEN);
```

That is the usual work flow, but the client also has some more methods which preprocess certain fields of the response or give you access to some other information, see [Special Methods](#special-methods).

Special Methods 
----------------

Besides the [getResult](http://jdoc.sems.uni-rostock.de/BiVeS-WS-Client/de/unirostock/sems/bives/webservice/client/BivesResponse.html#getResult(java.lang.String)) method of the [Bives/SingleFileResponse](http://jdoc.sems.uni-rostock.de/BiVeS-WS-Client/de/unirostock/sems/bives/webservice/client/BivesSingleFileResponse.html) object there are some other methods:

### Errors 

To check if an error occurred during the computations on the server you can as the `response` whether it [hasError](http://jdoc.sems.uni-rostock.de/BiVeS-WS-Client/de/unirostock/sems/bives/webservice/client/BivesResponse.html#hasError())s. If that's the case, you'll get them by calling [getErrors](http://jdoc.sems.uni-rostock.de/BiVeS-WS-Client/de/unirostock/sems/bives/webservice/client/BivesResponse.html#getErrors()):

```java
if (result.hasError ())
{
	for (String err : result.getErrors ())
		System.err.println ("ERROR: " + err);
	System.exit (1);
}
```

### Meta Data 

The is an object meant for people who are doing some analysis. The String returned by `result.get (BivesSingleFileResponse.COMMAND_META)` is in general a semicolon (`;`) delimited list of colon (`:`) delimited key-value-pairs: `key1:value1;key2:value2;...`

As of version 1.3 of the web service this list contains some node statistics in the `nodestats` field and some other fields depending on the document type:
* for CellML documents it also returns
  * `containsImports`: a boolean flag that is true if the model contains imports
  * `modelName`: the name of the model
* for SBML documents it also returns
  * `sbmlLevel`: the SBML level
  * `sbmlVersion`: the SBML version
  * `modelId`: the id of the model
  * `modelName`: the name of the model

This list is more or less arbitrary, we may add other information if there is a need for anyone. Feel free to [contact one of us](https://sems.uni-rostock.de/people/).
The meta data information is parsed by the client, so you can easily get this information by calling:

```java
Map<String, String> meta = result.getMeta ();
System.out.println ("model name: " + meta.get ("modelName"));
```

#### Node Stats 

The special `nodestats` field contains a comma (`,`) delimited map of eq sign (`=`) delimited key value pairs. Here the keys are tag names of nodes and values are the number of their occurrences in the document. Thus, the response may contain something like that:

```
nodestats:{reaction=1, model=1, listOfReactions=1, listOfReactants=1, species=4, ...};
```

However, also the `nodestats` are already parsed if you're using our client library. Therefore, you can simply sun something like the following:

```java
Map<String, Integer> nodeStats = result.getNodeStats ();
System.out.println ("there are " + nodeStats.get ("component") + " component nodes in the XML tree");
```


### Document Type 

The BiVeS web service is also able to detect the type of the document of interest. Thus, if you send the [`COMMAND_DOCUMENT_TYPE`](http://jdoc.sems.uni-rostock.de/BiVeS-WS-Client/de/unirostock/sems/bives/webservice/client/BivesSingleFileCommands.html#COMMAND_DOCUMENT_TYPE) command you can receive the document type as a list from the response object by calling:

```java
System.out.println ("this is of type " + result.getDocumentTypes ());
```

In case of CellML models this list will contain the entries `XML` and `CellML`, because it is valid XML and a valid CellML document. An SBML document will probably return `XML` and `SBML`.

