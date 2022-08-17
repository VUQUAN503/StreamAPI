<!-- <style>
r { color: Red }
o { color: Orange }
g { color: Green }
y { color: Yellow}
</style> -->

# **Stream API in Java**
## **Để học được bài học này các bạn cần có những kiến thức về functional interface, lambda expression and method reference.**
## **I. Functional interface & Lambda expresion.**
## 1.Functional interface
#### Khái niệm:
- Là một interface trong mà chỉ có duy nhất một hàm abstract
- Ta có thể sử dụng anotation @FunctionalInterface để đánh dấu nó.
```
@FunctionalInterface
public interface EnglishClub{
    void learnEnglish();
}
```
## 2.Anonymous Class
#### Khi ta new ra một object từ iterface về bản chất nó sẽ tạo ra một class mới và implement lại method abstract của interface đó và nó không có tên hay còn được gọi là Anonymous Class.
```
EnglishClub club = new EnglishClub() {
            @Override
            public void learnEnglish() {

            }
        };
```
#### Ta có Student implement lại EnglishClub, trong class Student có thêm method playBiLac
```
public class Student implements EnglishClub {
    @Override
    public void learnEnglish() {
        System.out.println("I am learning E");
    }

    public void playBiLac() {
        System.out.println("I am playing Bi Lắc");
    }
}
```
```
public static void main(String[] args) {
        EnglishClub student = new Student();
        student.learnEnglish();
        // Lúc này ta muốn gọi method playBilac của Student
        // => Ta cast thành class Student và gọi method
        ((Student)student).playBiLac();
        EnglishClub club = new EnglishClub() {
            @Override
            public void learnEnglish() {
                // Bạn có thể gọi phương thức sing() ở đây.
            }
            public void sing(){
                // bạn không thể gọi phương thức bày bên ngoài class vì nó không biết tên class nó là gì để cast.
            }
        };
}
```
#### *<r>Chú ý: </r>Trong anonymous class nếu ta có định nghĩa thêm các method trong nó thì bên ngoài sẽ không thể gọi được vì nó không biết nó là class nào để cast. Mình chỉ có thể gọi các phương thức đó bên trong hàm implement.*
## 3.Lambda expresion
#### Khái niệm: Khi interface chỉ có duy nhất một hàm abstract(hay gọi là functional interface) -> Tức là nó chỉ cần một thằng implement hàm abstract đó => Lúc này Java cho phép biến đổi nó thành một biểu thức lambda(Từ java 8 trở đi) hay gọi là anonymous function
#### Cú pháp: ([Danh sách tham số]->{/**thân của hàm**/})
#### <r>Chú ý</r>: Nếu thân của hàm chỉ có duy nhất một lệnh ta có thể bỏ {} hoặc chỉ có một tham số truyền vào hàm ta cũng có thể bỏ dấu ().
#### Ví dụ: Bên trên ta đã biết cách khai báo một anonymous class
```
EnglishClub club = new EnglishClub() {
            @Override
            public void learnEnglish() {
                
            }
};
```
#### Vì đây là một functional interface => Lúc này ta có thể biến đổi nó thành một biểu thức lambda.
```
    EnglishClub club = ()-> {};
```
#### Giải thích: Vì hàm learnEnglish không có tham số => theo cú pháp lambda trên ta dc *()->{}*. Nhìn rất ngắn gọn phải không. Những người mới tiếp cận thì sẽ thấy nó hơi khó hiểu. Mọi người chỉ nhớ cho mình là lambda expression là một functional interface()
#### *Chú ý: Chỉ Functional Interface ta mới có thể rút gọn nó thành biểu thức lambda.*
#### *Mọi người có thể tham khảo một số Functional Interface Java cung cấp sẵn*
- Predicate<T> :Nhận vào một tham số và trả về boolean.
```
@FunctionalInterface
public interface Predicate<T> {

    /**
     * Evaluates this predicate on the given argument.
     *
     * @param t the input argument
     * @return {@code true} if the input argument matches the predicate,
     * otherwise {@code false}
     */
    boolean test(T t);
    ...
}
```
<g>Ví dụ: Kiểm tra một số lẻ</g>
```
    Predicate<Integer> isOddNumber = i -> { return i % 2 != 0; };
```
- Consumer<T> : Nhận vào một tham số kiểu T và ko có giá trị trả về
```
@FunctionalInterface
public interface Consumer<T> {
    /**
     * Performs this operation on the given argument.
     *
     * @param t the input argument
     */
    void accept(T t);
}
```
<g>Ví dụ: in ra số lẻ</g>
```
    Consumer<Integer> printOddNumber = i -> { 
        if(i % 2 != 0){
            System.out.println("Odd number: " + i);
        }
    };
```
- Supplier<T> : Không nhận vào tham số và trả về giá trị kiểu T
```
    Supplier<Integer> getOddNumber = () -> { 
        return 1;
    };
```
- Function<T, R> : Nhận vào tham số kiểu T và trả về giá trị kiểu R
```
    // Convert integer to String
    Function<Integer, String> getOddNumber = i -> { 
        return "String - " + i;
    };
```
- ...
## **II. Stream API.**
### *Khái niệm:* Là một API cung cấp các hàm giúp mình xử lý và thao tác với tập hợp theo cách viết functional programming(What). Stream thiết kế theo partten builder nên ta có thể nên các phương thức của nó sẽ có kiểu giá trị trả về là chính nó.
#### <g>Một ví dụ cơ bản.</g>
```
    //Lấy các tên có độ dài lớn hơn 10 và viết hoa tất cả ký tự lên.
    names.stream()
    .filter(s -> s.length() > 10)
    .map(String::toUpperCase)
    .foreach(System.out::println);
```
### <r>*Lưu ý quan trọng: Stream chỉ hoạt động khi nó kết thúc bằng một phương thức terminate. Phương thức terminate là phương thức mà kiểu trả về của nó không phải Stream<> như forEach(), count(), collect(), ....*</r>
###1. Các cách tạo một stream cơ bản.
###1.1 Phương thức of
#### Ví dụ ta muốn tạo một stream gồm 5 phần tử
```
    Stream.of(3, 1, 5, 4, 2)
    .forEach(s -> System.out.println(s));
```
### 1.2. Sử dụng iterate method.
#### Phương thức này sẽ sinh ra các giá trị đế khi không sinh được nữa và nó nhận 3 tham số:
- #### *seed: là giá trị bắt đầu của stream*
- #### *Predicate(Functional Interface): là điều kiện được sinh ra*
- #### *UnaryOperator(Functional Interface): là cách thức sinh ra giá trị tiếp theo*
#### *<r>Chú ý:</r> Nếu không cần điều kiện sinh thì ta có thể loại bỏ tham số thứ 2.*
#### Mọi người để ý có thể thấy phương thưc này giống hệt vòng lặp. Ví dụ ta cần vòng lặp 10 làn chả hạn.
```
    for(int i = 0; i < 10; ++i){
        System.out.println(i);
    }
```
```
    Stream.iterate(0, i -> i < 10, i -> i + 1)
    .forEach(System.out::println);
```
#### Để giới hạn số lượng giá trị sinh ra tử sử dụng phương thức limit. Phương thức này nhận một tham số là số lượng phần tử giới hạn.
#### <g>Ví dụ: Sinh ra một stream có tối đa một 100 phần tử:</g>
```
    Stream.iterate(1, i -> i + 1)
          .limit(100)
          .forEach(System.out::println);
```
### 1.3. Tạo stream từ một collection
#### Trong List, Set, ... ta muốn biến đổi nó thành stream ta chỉ cần gọi phương thức stream()
#### <g>Ví dụ: Biến đổi List<> to Stream<></g>
```
List<Integer> numbers = List.of(1, 2, 3, 4, 5);
numbers.stream();// convert list -> stream
```
### 2. Các phương thức cơ bản
|Tên | Cú pháp |Mô tả |
--- | --- | --- |
|filter | public abstract Stream<T> filter(java.util.function.Predicate<? super T> predicate) | Returns a stream consisting of the elements of this stream that match the given predicate. |
|map | public abstract <R> Stream<R> map(java.util.function.Function<? super T, ? extends R> mapper) | Returns a stream consisting of the results of applying the given function to the elements of this stream. |
|flatMap | public abstract <R> Stream<R> flatMap(java.util.function.Function<? super T, ? extends Stream<? extends R>> mapper) | Returns a stream consisting of the results of replacing each element of this stream with the contents of a mapped stream produced by applying the provided mapping function to each element. Each mapped stream is closed after its contents have been placed into this stream. (If a mapped stream is null an empty stream is used, instead.) |
|findFirst | public abstract java.util.Optional<T> findFirst() | Returns an Optional describing the first element of this stream, or an empty Optional if the stream is empty. If the stream has no encounter order, then any element may be returned. |
|findAny | public abstract java.util.Optional<T> findAny() | Returns an Optional describing some element of the stream, or an empty Optional if the stream is empty. |
### 2.1 Filter method
#### Phương thức này giúp mình Lọc các phần tử theo điều kiện được truyền vào
#### Ví dụ: Lọc phần tử chẵn trong stream integer
#### Nếu mọi người chưa quen với biểu thức lambda thì mình new trực tiếp đối tượng Predicate sau khi viết logic trong hàm implement xong mình có thể biến đổi nó thành biểu thức lambda theo [công thức trên](#3lambda-expresion).
```
    // tạo stream integer gồm 5 phần tử
    Stream.of(1, 2, 3, 4, 5)
          .filter(new Predicate(){
                @Override
                public void test(Integer i){
                    return i % 2 == 0;
                }
          }) // lọc ra các phần tử chẵn
          .forEach(System.out::println); // 2 4
```
#### Viết bằng biểu thức lambda.
```
    // tạo stream integer gồm 5 phần tử
    Stream.of(1, 2, 3, 4, 5)
          .filter(i -> i % 2 == 0) // lọc ra các phần tử chẵn
          .forEach(System.out::println); // 2 4
```