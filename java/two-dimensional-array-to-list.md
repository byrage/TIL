~~~java
String[][] twoDimensionalArray = new String[0][0];
List<String> list = Collections.unmodifiableList(Arrays.stream(twoDimensionalArray)
                                                       .flatMap(Arrays::stream)
                                                       .collect(Collectors.toList()));
~~~
