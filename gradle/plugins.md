
#### java
- 기본 디렉토리 구조를 설정해주는 플러그인
- https://docs.gradle.org/current/userguide/java_plugin.html
- http://kwonnam.pe.kr/wiki/gradle/java

#### idea
- 프로젝트를 IntelliJ에서 열 수 있는 기능을 지원하는 플러그인
~~~bash
./gradlew openIdea
./gradlew cleanIdea
~~~
- 최근에는 idea 플러그인이 없이도 IntelliJ 에서 프로젝트 Open 할 수 있음
- https://docs.gradle.org/current/userguide/idea_plugin.html
- http://kwonnam.pe.kr/wiki/gradle/idea

#### eclipse
- 프로젝트를 eclipse에서 열 수 있는 기능을 지원하는 플러그인
~~~bash
./gradlew eclipse
./gradlew cleanEclipse
~~~
- 멀티모듈 프로젝트의 경우 서브모듈을 eclipse에서 열 수 있도록 설정해준다고 함
- https://docs.gradle.org/current/userguide/eclipse_plugin.html
- http://kwonnam.pe.kr/wiki/gradle/eclipse
