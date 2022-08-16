# **Stream API in Java**
## **Để học được bài học này các bạn cần có những kiến thức về functional interface, lambda expression and method reference.**
## **I. Functional interface & Lambda expresion.**
## 1.Functional interface
### Khái niệm:
- Là một interface trong mà chỉ có duy nhất một hàm abstract
- Ta có thể sử dụng anotation @FunctionalInterface để đánh dấu nó.
```
@FunctionalInterface
public interface EnglishClub{
    void learnEnglish();
}
```
## 2.Anonymous Class
### Khi ta new ra một object từ iterface về bản chất nó sẽ tạo ra một class mới và implement lại method abstract của interface đó và nó không có tên hay còn được gọi là Anonymous Class.
```
EnglishClub club = new EnglishClub() {
            @Override
            public void learnEnglish() {

            }
        };
```
### Ta có Student implement lại EnglishClub, trong class Student có thêm method playBiLac
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
                
            }
            public void sing(){

            }
        };
}
```
## 3.Lambda expresion
### Khái niệm: Khi interface chỉ có duy nhất một hàm abstract(hay gọi là functional interface) -> Tức là nó chỉ cần một thằng implement hàm abstract đó => Lúc này Java cho phép biến đổi nó thành một biểu thức lambda(Từ java 8 trở đi) hay gọi là anonymous function
### Cú pháp: ([Danh sách tham số]->{/**thân của hàm**/})
### Chú ý: Nếu thân của hàm chỉ có duy nhất một lệnh ta có thể bỏ {} hoặc chỉ có một tham số truyền vào hàm ta cũng có thể bỏ dấu ()
### Ví dụ: Bên trên ta đã biết cách khai báo một anonymous class
```
EnglishClub club = new EnglishClub() {
            @Override
            public void learnEnglish() {

            }
};
```
### Vì đây là một functional interface => Lúc này ta có thể biến đổi nó thành một biểu thức lambda.
```
    EnglishClub club = ()-> {};
```
### Giải thích: Vì hàm learnEnglish không có tham số => theo cú pháp lambda trên ta dc *()->{}*. Nhìn rất ngắn gọn phải không. Những người mới tiếp cận thì sẽ thấy nó hơi khó hiểu. Mọi người chỉ nhớ cho mình là lambda một functional interface()
### *Chú ý: Chỉ Functional Interface ta mới có thể rút gọn nó thành biểu thức lambda.*
### *Mọi người có thể tham khảo một số Functional Interface Java cung cấp sẵn*
- Predicate<T>
- Consumer<T>
- Supplier<T>
- Function<T, R>
- ...
## **II. Stream API.**
>## *Khái niệm:* Là một API cung cấp các hàm giúp mình sử lý và thao tác với tập hợp theo cách viết functional programming(What).
## 1. Cách tạo một stream.
## 1.1 Phương thức of
### Ví dụ ta muốn tạo một stream gồm 5 phần tử
```
    Stream.of(3, 1, 5, 4, 2)
    .forEach(s -> System.out.println(s));
```
## 2. Sử dụng iterate method.
>### Phương thức này sẽ sinh ra các giá trị đế khi không sinh được nữa và nó nhận 3 tham số:
> - ### *seed: là giá trị bắt đầu của stream*
> - ### *Predicate(Functional Interface): là điều kiện được sinh ra*
> - ### *UnaryOperator(Functional Interface): là cách thức sinh ra giá trị tiếp theo*
> ### *Chú ý: Nếu không cần điều kiện sinh thì ta có thể loại bỏ tham số thứ 2.*
> ### Mọi người để ý có thể thấy phương thưc này giống hệt vòng lặp. Ví dụ ta cần vòng lặp 10 làn chả hạn.
```
    for(int i = 0; i < 10; ++i){
        System.out.println(i);
    }
```
```
    Stream.iterate(0, i -> i < 10, i -> i + 1)
    .forEach(System.out::println);
```
### Để giới hạn số lượng giá trị sinh ra tử sử dụng phương thức limit. Phương thức này nhận một tham số là số lượng phần tử giới hạn. Ví dụ ta cần sinh ra tối đa một 100 phần tử:
```
    Stream.iterate(1, i -> i + 1)
          .limit(100)
          .forEach(System.out::println);
```


