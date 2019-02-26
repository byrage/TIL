- ES에서 많은양의 문서를 검색할 때 쓰는 방법으로 RDB의 커서와 유사한 방식으로 동작한다.
> 다량의 문서를 검색할때 사용하는 다른 방법으로는 [window size 세팅](es-window-size.md)이 있음 (추천하지 않음)
- scroll 옵션을 설정하고 search 요청을 보내면 매 요청마다 scroll_id를 받고, 이후에 scroll_id를 통해 요청을 보냄
> 계속적인 scroll API 요청상황에서 scroll_id는 왠만하면 변경되지 않지만, 변경될 가능성도 있으니 가장 최신에 받은 scroll_id를 가지고 요청을 보내라고 함
- [Slice scroll API](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-scroll.html#sliced-scroll)를 이용해서 요청 개수를 나누고, 독립적으로 호출할 수 있다. (병렬처리 시 유용할듯)
- scroll id는 요청시 지정한 keepAliveTime이 지나면 닫히지만, [Clear Scroll API](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-scroll.html#_clear_scroll_api)를 호출해서 직접 삭제할 수도 있음
- Scroll API? : https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-scroll.html
- Scroll API in REST Client : https://www.elastic.co/guide/en/elasticsearch/client/java-rest/6.6/java-rest-high-search-scroll.html

#### Example
~~~json
POST /twitter/_search?scroll=1m
{
    "size": 100,
    "query": {
        "match" : {
            "title" : "elasticsearch"
        }
    }
}

Response
{
  "_scroll_id": "DXF1ZXJ5QW5kRmV0Y2gBAAAAAAAABVcWYzZka0ZUY3lSeUdYZjRsbGtxUHJYdw==",
  "took": 14,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 0,
    "max_score": null,
    "hits": [
        ...
    ]
  }
}
----------------------------------------------------------------------------------------
POST /_search/scroll 
{
    "scroll" : "1m", 
    "scroll_id" : "DXF1ZXJ5QW5kRmV0Y2gBAAAAAAAABVcWYzZka0ZUY3lSeUdYZjRsbGtxUHJYdw==" 
}
~~~

~~~java
public void searchScroll(SearchRequest searchRequest, TimeValue scrollKeepAlive) throws IOException {

        searchRequest.scroll(scrollKeepAlive);

        SearchResponse searchResponse = searchRestHighLevelClient.search(searchRequest, REQUEST_OPTIONS);
        String scrollId = searchResponse.getScrollId();
        SearchHit[] searchHits = searchResponse.getHits().getHits();

        while (searchHits != null && searchHits.length > 0) {

            log.info("searchScroll. searchHits size={}, responseTime={}", searchHits.length, searchResponse.getTook());

            SearchScrollRequest scrollRequest = new SearchScrollRequest(scrollId).scroll(scrollKeepAlive);
            searchResponse = searchRestHighLevelClient.scroll(scrollRequest, RequestOptions.DEFAULT);
            searchHits = searchResponse.getHits().getHits();
        }

        clearScroll(scrollId);
    }

    private void clearScroll(String scrollId) throws IOException {

        ClearScrollRequest clearScrollRequest = new ClearScrollRequest();
        clearScrollRequest.addScrollId(scrollId);
        ClearScrollResponse clearScrollResponse = searchRestHighLevelClient.clearScroll(clearScrollRequest, RequestOptions.DEFAULT);
        log.info("clearScrollResponse. isSucceeded={}, scrollId={}", clearScrollResponse.isSucceeded(), scrollId);
    }
~~~