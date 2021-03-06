# contactFolder: delta

> **Important**: APIs under the /beta version in Microsoft Graph are in preview and are subject to change. Use of these APIs in production applications is not supported.

Get a set of contact folders that have been added, deleted, or removed from the user's mailbox.

A **delta** function call for contact folders in a mailbox is similar to a GET request, except that by appropriately 
applying [state tokens](../../../concepts/delta_query_overview.md) in one or more of these calls, 
you can query for incremental changes in the contact folders. This allows you to maintain and synchronize 
a local store of a user's contact folders without having to fetch all the contact folders of that mailbox from the server every time.

### Prerequisites
One of the following **scopes** is required to execute this API: _Contacts.Read_; _Contacts.ReadWrite_

### HTTP request
<!-- { "blockType": "ignored" } -->
```http
GET /me/contactFolders/delta
GET /users/<id>/contactFolders/delta
```

### Query parameters

Tracking changes in contact folders incurs a round of one or more **delta** function calls. If you use any query parameter 
(other than `$deltatoken` and `$skiptoken`), you must specify 
it in the initial **delta** request. Microsoft Graph automatically encodes any specified parameters 
into the token portion (`skiptoken` or `$deltatoken`) of the `nextLink` or `deltaLink` URL provided in the response. 
You only need to specify any desired query parameters once upfront. 
In subsequent requests, simply copy and apply the `nextLink` or `deltaLink` URL from the previous response, as that URL already 
includes the encoded, desired parameters.

| Query parameter	   | Type	|Description|
|:---------------|:--------|:----------|
| $deltatoken | string | A [state token](../../../concepts/delta_query_overview.md) returned in the `deltaLink` URL of the previous **delta** function call for the same contact folder collection, indicating the completion of that round of change tracking. Save and apply the entire `deltaLink` URL including this token in the first request of the next round of change tracking for that collection.|
| $skiptoken | string | A [state token](../../../concepts/delta_query_overview.md) returned in the `nextLink` URL of the previous **delta** function call, indicating there are further changes to be tracked in the same contact folder collection. |


#### OData query parameters

You can use a `$select` query parameter as in any GET request to specify only the properties your need for best performance. The 
_id_ property is always returned. 

### Request headers
| Name       | Type | Description |
|:---------------|:----------|:----------|
| Authorization  | string  | Bearer {token}. Required. |
| Content-Type  | string  | application/json. Required. |
| Prefer | string  | odata.maxpagesize={x}. Optional. |


### Response
If successful, this method returns a `200, OK` response code and [contactFolder](../resources/contactfolder.md) collection object in the response body.

### Example
##### Request
The following example shows how to make a single **delta** function call, and limit the maximum number of contact folders 
in the response body to 2.

To track changes in the contact folders of a mailbox, you would make one or more **delta** function calls, with 
appropriate state tokens, to get the set of incremental changes since the last delta query. 

You can find a similar example that shows how to use the state tokens to track changes in the messages of a mail folder: 
[Get incremental changes to messages in a folder](../../../concepts/delta_query_messages.md). The main differences
between tracking contact folders and tracking messages in a folder are in the delta query request URLs, and the query responses 
returning **contactFolder** rather than **message** collections.

<!-- {
  "blockType": "request",
  "name": "contactfolder_delta"
}-->
```http
GET https://graph.microsoft.com/beta/me/contactFolders/delta

Prefer: odata.maxpagesize=2
```

##### Response

If the request is successful, the response would include a state token, which is either a _skipToken_  
(in an _@odata.nextLink_ response header) or a _deltaToken_ (in an _@odata.deltaLink_ response header). 
Respectively, they indicate whether you should continue with the round or you have completed 
getting all the changes for that round.

The response below shows a _skipToken_ in an _@odata.nextLink_ response header.

Note: The response object shown here may be truncated for brevity. All of the properties will be returned from an actual call.
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.contactFolder",
  "isCollection": true
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json
Content-length: 254

{
  "@odata.nextLink":"https://graph.microsoft.com/beta/me/contactfolders/delta?$skiptoken={_skipToken_}",
  "value": [
    {
     "parentFolderId": "parentFolderId-value",
      "displayName": "displayName-value",
      "wellKnownName": "wellKnownName-value",
      "id": "id-value"
    }
  ]
}
```

### See also

- [Microsoft Graph delta query](../../../concepts/delta_query_overview.md)
- [Get incremental changes to messages in a folder](../../../concepts/delta_query_messages.md)

<!-- uuid: 8fcb5dbc-d5aa-4681-8e31-b001d5168d79
2015-10-25 14:57:30 UTC -->
<!-- {
  "type": "#page.annotation",
  "description": "contactFolder: delta",
  "keywords": "",
  "section": "documentation",
  "tocPath": ""
}-->
