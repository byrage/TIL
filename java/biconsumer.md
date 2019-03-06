#### Description
- `BiConsumer` Inteface는 Vava8에 `java.util.function`패키지에 추가됨
- 리턴값이 없으므로 각 객체의 메소드를 실행시킬 때 유용
- `BiConsumer.accept()` : ` 2개의 파라미터`(T, U)`를 받아서 ` function을 실행
- `BiConsumer.andthen()` : 2개의 `BiConsumer`를 순차적으로 실행시킴. andThen().accept() 순서로 호출해야 한다.
- reactor `doOnSuccessOrError()` 메소드에서는 onSuccess시 클래스와 throwable을 파라미터로 받아서 로직 처리 할 수 있다.
~~~java
// signature
public final Mono<T> doOnSuccessOrError(BiConsumer<? super T, Throwable> onSuccessOrError) {}

// example
Mono.just(String.valueOf(1)).doOnSuccessOrError((string, throwable) -> {
               if (throwable != null) {
                   log.error("occurred exception.", e);
               }
                if("1".equals(string)) {
                   ...
               }
            });
~~~

#### Example
- `accept()`
~~~java
import java.util.ArrayList; 
import java.util.List; 
import java.util.function.BiConsumer; 
  
public class GFG { 
    public static void main(String args[]) 
    { 
  
        // Create the first list 
        List<Integer> lista = new ArrayList<Integer>(); 
        lista.add(2); 
        lista.add(1); 
        lista.add(3); 
  
        // Create the second list 
        List<Integer> listb = new ArrayList<Integer>(); 
        listb.add(2); 
        listb.add(1); 
        listb.add(2); 
  
        // BiConsumer to compare both lists 
        BiConsumer<List<Integer>, List<Integer> > 
            equals = (list1, list2) -> 
        { 
            if (list1.size() != list2.size()) { 
                System.out.println("False"); 
            } 
            else { 
                for (int i = 0; i < list1.size(); i++) 
                    if (list1.get(i) != list2.get(i)) { 
                        System.out.println("False"); 
                        return; 
                    } 
                System.out.println("True"); 
            } 
        }; 
        equals.accept(lista, listb); 
    } 
} 
~~~
~~~
False
~~~

- `andThen()`
~~~java
public class Main { 
    public static void main(String args[]) 
    { 
  
        // Create first list 
        List<Integer> lista = new ArrayList<Integer>(); 
        lista.add(2); 
        lista.add(1); 
        lista.add(3); 
  
        // Create second list 
        List<Integer> listb = new ArrayList<Integer>(); 
        listb.add(2); 
        listb.add(1); 
        listb.add(2); 
  
        // BiConsumer to compare 2 lists of integers 
        BiConsumer<List<Integer>, List<Integer> > equals = (list1, list2) -> 
        { 
            if (list1.size() != list2.size()) { 
                System.out.println("False"); 
            } 
            else { 
                for (int i = 0; i < list1.size(); i++) 
                    if (list1.get(i) != list2.get(i)) { 
                        System.out.println("False"); 
                        return; 
                    } 
                System.out.println("True"); 
            } 
        }; 
  
        // BiConsumer to print 2 lists 
        BiConsumer<List<Integer>, List<Integer> > disp = (list1, list2) -> 
        { 
            list1.stream().forEach(a -> System.out.print(a + " ")); 
            System.out.println(); 
            list2.stream().forEach(a -> System.out.print(a + " ")); 
            System.out.println(); 
        }; 
  
        // Using addThen() method 
        equals.andThen(disp).accept(lista, listb); 
    } 
} 
~~~
~~~
False
2 1 3 
2 1 2
~~~

- See : https://www.geeksforgeeks.org/java-8-biconsumer-interface-in-java-with-examples/