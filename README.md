Sage SData Client Libraries for .NET
====================================

This repository contains the following

*	A .NET 4.5.1 library for consuming [SData](http://sdata.sage.com).
*	A set of unit tests
*	A WinForms application demonstrating various features of the API

For information about the client library and examples on how to use it, check out "Intro to SData CSharp Client Lib.doc" in the [docs](https://github.com/kodefuguru/SDataCSharpClientLib/tree/master/docs/) subfolder.

Also, keep an eye on the [Wiki](https://github.com/kodefuguru/SDataCSharpClientLib/wikis) for up-to-date information.

## Examples

#### Single Resources (Entry)
Single Resources are returned to the SData Client Libraries as an AtomEntry.  Simply specify the ResourceKind and Resource Selector and call the Read method of the SDataSingleResourceRequest.
	
	var service = new SDataService("http://localhost/sdata/slx/dynamic/-/", "admin", "");
	var request = new SDataSingleResourceRequest(service)
	{   
		ResourceKind = "contacts",
		ResourceSelector = "'CGHEA0000001'"
	};
	var entry = request.Read();
	var payload = entry.GetSDataPayload();

#### Resource Collections (Feed)
Resource collections are returned to the SData Client Libraries as an AtomFeed. Simply specify the desired ResourceKind and call the Read method on the SDataResourceCollectionRequest object.

	var service = new SDataService("http://localhost/sdata/slx/dynamic/-/", "admin", "");
	var request = new SDataResourceCollectionRequest(service) 
	{    
	    ResourceKind = "contacts" 
	}; 
	var feed = request.Read(); 
	foreach (var entry in feed.Entries) 
	{   
	    var payload = entry.GetSDataPayload(); 
	}

#### Batch Operations

	var contactIds = new[] {"CGHEA0002670", "CDEMOA00003A", "CDEMOA000037"}; 
	var service = new SDataService("http://localhost/sdata/slx/dynamic/-/", "admin", ""); 
	using (var batch = new SDataBatchRequest(service) {ResourceKind = "contacts"}) 
	{    
		var request = new SDataSingleResourceRequest(service) {ResourceKind = "contacts"};    
		foreach (var id in contactIds)    
		{        
			request.ResourceSelector = string.Format("'{0}'", id);        
			request.Read();    
		}    
		var feed = batch.Commit();    
		foreach (var entry in feed.Entries)    
		{        
			var values = entry.GetSDataPayload().Values;        
			values.Clear(); //no need to send back unchanged values        
			values["Type"] = "Decision Maker";        
			request.Entry = entry;        
			request.Update();    
		}    feed = batch.Commit();    
		foreach (var entry in feed.Entries)    
		{       
			Debug.Assert(entry.GetSDataHttpStatus() == HttpStatusCode.OK);    
		} 
	}
