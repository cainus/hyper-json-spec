#hyper-json-spec

This is a specification for hyper+json, which is a json format with hyperlinks.

##A quick example:
This example shows almost all the (entirely optional) features at once.
```javascript
{ 
  "some" : "other property",
  "more" : [1,2,3,4],
  "_links" : { 
              "users" : {"href" : "/user"},
              "create" : { "href" : "/user", 
                           "method" : "POST", 
                           "schema" : {
                              "description" : "A user",
                              "type" : "object",
                              "properties" : {
                                "firstname" : { 
                                  "type" : "string",
                                  "title" : "First Name"
                                },
                                "lastname" : { 
                                  "type" : "string",
                                  "title" : "Last Name"
                                }
                              }
                           }
                          },
              "user-search" : {"href" : "/user?name={username}"}
}}
```

##Goals of the hyper+json mediatype:
* **To create a hypermedia format for the purpose of web-based APIs.**
  Hypermedia has a unique way of making an API self-describing which
makes it easier to learn and use, both for humans and for programs.
* **Support hypermedia in otherwise application/json documents.**  This is important because most real-world APIs use a json media-type, but few hypermedia media-types are based on json.  This has been a hinderance to the proliferation of hypermedia apis. 
* **support hypermedia controls that not only describe read capabilities
  of the server/api (like hyperlinks in html) but also describe capabilities that might change state (like forms in  html).**  The purpose of this is to attempt to fully describe the capabilities of a server/api in the actual documents that it responds with.  This probably separates hyper+json from other json-based hypermedia formats.
* **Minimize the introduction of any additional "types" that are beyond what is absolutely necessary to support hypermedia.**  This is important because a key feature of json is its simplicity.
* **Don't constrain the json form in any way whatsoever beyond the json spec.**  The features (links) that this spec adds to json are optional and therefore not necessary to be considered a hyper+json document.  Conversely, a hyper+json document is also "just json", because the links that are added are added as a json object and are still parseable by any correct json parser.

##Non-goals:
* **Dictating a format or schema for sub-types (like a "collection" representation), even
  though this appears to be common in other json hypermedia types.**  Of course other
  mediatypes could do this and base themselves off hyper+json if they
wanted to (or not).
* **Constraining the representation in any way whatsoever to aid in
  interactivity with a specific client framework.**  Of course other
  mediatypes could do this and base themselves off hyper+json if they
wanted to (or not).

<sub>
All documents related to the Hyper+JSON mediatype shall use the keywords “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” to describe requirements. These keywords are to be interpreted as described in [RFC2119].</sub>

##1 The _links object
 In a hyper+json document, an object or a property of an object with the name _links MUST be considered to be a _links object as defined in this specification.  

The _links object MAY be added as a property to any JSON object (a root object, or any number of sub-objects) to represent a list of available “links” as name-value pairs, where the name is the link relation name, and the value is an object describing the available interaction in detail.

###1.1 Link relations
The name of a link (in the list of name-value pairs of a _links object) is the relation name that denotes how the given link relates to the current document (as described in detail by http://tools.ietf.org/html/rfc5988). A Hyper+JSON link has a relation name that is similar in nature to the "rel" property of an HTML link but unlike HTML links, its “rel” property is the name for it in the _links object.

eg:
Relation in a html link:
```html
<a href="/user" rel="users">User</a>
```

Relation in a hyper+json link:
```javascript
{"_links" : { "users" : {"href" : "/user"} }}
```

The actual name used for the relation SHOULD use a standard relation
name (
http://www.iana.org/assignments/link-relations/link-relations.xhtml )
where possible.

###1.1 href property
A link object MUST contain an `href` property that denotes the uri for
the given link.  The value must either be a uri that conforms to the
uri specification ( https://tools.ietf.org/html/rfc6570 ), or a
uri-template that confirms to the uri-template specification ( https://tools.ietf.org/html/rfc6570 ).

For example:

```javascript
{
  "some" : "other property",
  "more" : [1,2,3,4],
  "_links" : { 
              "users" : {"href" : "/user"},
              "user-search" : {"href" : "/user?name={username}"}
}}
```

###1.2 method property


A link object MAY contain a `method` property that denotes the HTTP method ( http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html ) to be used for the given link.  If no `method` property is provided a client MUST assume that the method is GET.   The `method` property MUST contain a valid HTTP method. The method MUST be allowed for the resource at the URI specified in the 'href' property.

For example:

```javascript
{
  "some" : "other property",
  "more" : [1,2,3,4],
  "_links" : { 
              "remove" : {"href" : "/user/1234", "method" : "DELETE"},
              "user-search" : {"href" : "/user?name={username}"}
}}
```

###1.3 The schema property
For links where the HTTP method expects an accompanying HTTP body of type application/json (or a suitable sub-type of application/json), a `schema` SHOULD be provided and MUST be a valid JSON Schema document ( See http://json-schema.org/ for details and examples ). The schema MAY be used for validation purposes both on the client and the server, though any given request may be subject to additional validations not described in the schema.


```javascript
{ 
  "some" : "other property",
  "more" : [1,2,3,4],
  "_links" : { 
              "create" : { "href" : "/user", 
                           "method" : "POST", 
                           "schema" : {
                              "description" : "A user",
                              "type" : "object",
                              "properties" : {
                                "firstname" : { 
                                  "type" : "string",
                                  "title" : "First Name"
                                },
                                "lastname" : { 
                                  "type" : "string",
                                  "title" : "Last Name"
                                }
                              }
                           }
                          },
              "user-search" : {"href" : "/user?name={username}"}
}}
```





