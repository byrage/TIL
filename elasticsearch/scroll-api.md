- Scroll API? : https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-scroll.html
- Scroll API in REST Client : https://www.elastic.co/guide/en/elasticsearch/client/java-rest/6.6/java-rest-high-search-scroll.html


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