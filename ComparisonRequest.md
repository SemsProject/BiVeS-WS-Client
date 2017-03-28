Comparison Request 
===================

* compare versions of computational models
* see also [/src/test/java/de/unirostock/sems/bives/webservice/client/ClientComparisonExample.java](https://github.com/SemsProject/BiVeS-WS-Client/tree/master/src/test/java/de/unirostock/sems/bives/webservice/client/ClientComparisonExample.java)

To initiate a comparison request create an object of the type [Bives/ComparisonRequest](https://github.com/SemsProject/BiVeS-WS-Client/tree/master/src/main/java/de/unirostock/sems/bives/webservice/client/BivesComparisonRequest.java) ([JDOC](http://jdoc.sems.uni-rostock.de/BiVeS-W/S-Client/de/unirostock/sems/bives/webservice/client/BivesComparisonRequest.html)), passing the link to the two versions of a model or the XML representations of these models:

```java
BivesComparisonRequest request = new BivesComparisonRequest ("http://.../path/to/model2.xml", "http://.../path/to/model2.xml");
// alternatively
request = new BivesComparisonRequest ("<sbml> .. </sbml>", "<sbml> .. </sbml>");
```

afterwards you need to define the operations to be executed on the web server by [adding commands](http://jdoc.sems.uni-rostock.de/BiVeS-W/S-Client/de/unirostock/sems/bives/webservice/client/BivesRequest.html/#addCommand%28java.lang.String%29) (commands are available as static fields in [Bives/ComparisonRequest](http://jdoc.sems.uni-rostock.de/BiVeS-W/S-Client/de/unirostock/sems/bives/webservice/client/BivesComparisonCommands.html#field_summary)). For example, to *force SBML* comparison (skip detection of document type) and compute the *highlighted reaction network encoded in GraphML* (see [Reaction Network](http://sems.uni-rostock.de/trac/bives-core/wiki/ReactionNetwork) and [GraphML](http://sems.uni-rostock.de/trac/bives-core/wiki/GraphmlFormatDescription)) and the report of the* differences encoded in HTML* (see also [report](http://sems.uni-rostock.de/trac/bives-core/wiki/TypeSetting#HTML) and [HTML](http://sems.uni-rostock.de/trac/bives-core/wiki/ReportHtml)) you would do the following:

```java
request.addCommand(BivesComparisonRequest.COMMAND_FORCE_SBML);
request.addCommand(BivesComparisonRequest.COMMAND_REACTIONS_GRAPHML);
request.addCommand(BivesComparisonRequest.COMMAND_REPORT_HTML);
```

(see also [all constant values](http://jdoc.sems.uni-rostock.de/BiVeS-W/S-Client/constant-values.html))

To perform the request you need to instantiate a [BivesWs](http://jdoc.sems.uni-rostock.de/BiVeS-W/S-Client/de/unirostock/sems/bives/webservice/client/BivesWs.html) object by passing the URL to the /BiVeS web service you want to use (here we're using the web service of the [SEMS project](https://sems.uni-rostock.de/), available at <http://bives.sems.uni-rostock.de/>).
This object is able to communicate to the web service, just pass the request to the [performRequest](http://jdoc.sems.uni-rostock.de/BiVeS-W/S-Client/de/unirostock/sems/bives/webservice/client/BivesWs.html#performRequest%28de.unirostock.sems.bives.webservice.client.//BivesComparisonRequest%29) method. The result will be a [Bives/ComparisonResponse](http://jdoc.sems.uni-rostock.de/BiVeS-W/S-Client/de/unirostock/sems/bives/webservice/client/BivesComparisonResponse.html):

```java
BivesWs bives = new HttpBivesClient("http://bives.sems.uni-rostock.de/");
BivesComparisonResponse result = bives.performRequest(request);
```

The returned [Bives/ComparisonResponse](http://jdoc.sems.uni-rostock.de/BiVeS-W/S-Client/de/unirostock/sems/bives/webservice/client/BivesComparisonResponse.html) object provides methods to get the result of that response. Here for example we get the requested HTML report by passing the **HTML report command** to the [getResult](http://jdoc.sems.uni-rostock.de/BiVeS-W/S-Client/de/unirostock/sems/bives/webservice/client/BivesResponse.html/#getResult(java.lang.String)) method:

```java
String htmlReport = result.getResult (BivesComparisonResponse.COMMAND_REPORT_HTML);
```

That's the usual work flow, but the client also has some more methods which give you access to some other information.

Special Methods 
----------------
Besides the [getResult](http://jdoc.sems.uni-rostock.de/BiVeS-W/S-Client/de/unirostock/sems/bives/webservice/client/BivesResponse.html/#getResult(java.lang.String)) method of the [Bives/ComparisonResponse](http://jdoc.sems.uni-rostock.de/BiVeS-W/S-Client/de/unirostock/sems/bives/webservice/client/BivesComparisonResponse.html) object there are some other methods, see [Special methods](#special-methods).

### Errors 

To check if an error occurred during the computations on the server you can as the `response` whether it [hasError](http://jdoc.sems.uni-rostock.de/BiVeS-W/S-Client/de/unirostock/sems/bives/webservice/client/BivesResponse.html/#hasError())s. If that's the case, you'll get them by calling [getErrors](http://jdoc.sems.uni-rostock.de/BiVeS-W/S-Client/de/unirostock/sems/bives/webservice/client/BivesResponse.html/#getErrors()):

```java
if (result.hasError ())
{
	for (String err : result.getErrors ())
		System.err.println ("ERROR: " + err);
	System.exit (1);
}
```
