# A content cache to store crawled content in Elasticsearch

Alexandria provides a simple interface to store resources downloaded from the web. It uses Elasticsearch to index downloaded content.

## Usage

All methods in alexandria return an `EventChannel`:

    alexandria = require "alexandria"
    
    # Elasticsearch host, port and protocol details
    options =
      host: "127.0.0.1"
      port: 9200
      secure: false
      
    do events.serially (go) ->
      (go) -> alexandria.initialize(options)
      (go) -> alexandria.deleteStore(someDomain)
      (go) -> alexandria.createStore(someDomain)
      (go) -> alexandria.putResource(someDomain, someUrl, content_type, content)
      (go) -> alexandria.putResource(someDomain, someOtherUrl, content_type, content)
      (go) -> alexandria.getResource(domain, someUrl, true) # pass false as last argument if resource is not to be downloaded if not in cache
      (go) -> alexandria.getResource(domain, someOtherUrl, true)
      (go) -> alexandria.getAllResourceUrls(someDomain)


## Notes on Elasticsearch index and type mappings

* Index where resources are stored can be configured via `options` passed to `initialize`

* For every domain whose resources are stored, two type mappings are created
  * one type to store content type and the actual content in base64 encoding, id of the document will be md5 digest of the content and another type to store url and id of the document that contains the actual content
  * first type is named `<domain name with . replaced by _>` and second type is named `<domain name with . replaced by _>__content__`, example: a domain such as `example.com` would be  types would be named `example_com` and `example_com__content__`
