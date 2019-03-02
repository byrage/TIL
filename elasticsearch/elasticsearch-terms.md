## [Split Brain](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-node.html#split-brain)
- ES에는 discovery.zen.minimum_master_nodes 옵션을 통해 마스터노드 개수를 설정할 수 있는 옵션이 있다. (파일 또는 _cluster/settings API 설정)
- master_eligible_nodes 중에서 마스터 노드를 선출한다.
- 선출된 마스터 노드가 통신이 불가능할 때 (네트워크 이슈) 마스터 노드 후보 중에 하나를 마스터노드로 대체한다.
- 통신이 불가능하므로 기존의 마스터노드와 새로 선출된 마스터노드는 서로 자기만이 유일한 마스터노드라고 판단하게 됨
- 이후에 기존의 마스터노드가 복구 되었을 때, 새로 선출된 마스터노드와 다른 정보를 가지고 있으면 어떤 데이터가 올바른 데이터인지 알 수 없기 때문에 데이터가 유실됨
- ES에서는 과반수의 마스터 노드에 동일한 데이터를 가지고 있을 경우에 올바른 데이터라고 판단함
- minimum_master_nodes = (master_eligible_nodes / 2 ) + 1
- 또한, rolling update를 위해서는 minimu_master_nodes를 3 이상으로 설정해야 한다.

## [Hot-Warm Architecture](https://www.elastic.co/blog/hot-warm-architecture-in-elasticsearch-5-x)
- 로그, 메트릭 같은 일 단위(time-based) 데이터 관리 시 사용할수 있는 아키텍처
- 최근 인덱스를 Hot Node에 배치 시키고, 이전 인덱스는 Warm Node에 배치시키는 방식
- Hot Node : 검색, 색인과 같은 CPU,IO 작업이 많이 일어나므로 CPU,Disk 성능이 좋아야 함
- Warm Node : 높은 성능이 필요 없지만 많은 용량이 필요하므로 HDD를 사용
- ES에서 지원하는 [Curator](https://www.elastic.co/guide/en/elasticsearch/client/curator/current/about.html)를 통해서 배치 잡을 설정할 수도 있다. 
- 일 단위 데이터 관리 시 alias를 설정해서 당일 데이터에 접근하는 개념과 비슷하지만 자원이 한정적인 상황에서는 효과적인 아키텍처로 생각됨