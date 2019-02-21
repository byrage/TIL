- objectmapper로 객체 -> json 변환 시 primitive boolean 타입 필드가 있다면 getter 이름을 기준으로 json이 생성됨
```
private boolean isUse;
-> 
{
  "use" : true
}
```

- 필드 위에 @JsonProperty를 붙이면 필드명, getter명 2개의 json이 생성됨
```
@JsonProperty("isUse")
private boolean isUse;
->
{
    "use" : true,
    "isUse" : true
}
```

- getter 위에 @JsonProperty를 붙이면 필드명으로만 json이 생성됨
```
@JsonProperty("isUse")
public boolean isUse() {
    return isUse;
}
->
{
    "isUse" : true
}
```