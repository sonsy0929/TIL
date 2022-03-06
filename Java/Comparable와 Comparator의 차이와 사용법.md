# 객체의 정렬 기준을 명시하는 두 가지 방법
## 객체 정렬: Comparable과 Comparator 인터페이스
Comparable과 Comparator 인터페이스는 둘 다 정렬의 기준을 정하는 인터페이스이다. 보통 Comparable은 프로그램에서 계속 사용될 default 정렬을 정의하는데 쓰이고, Comparator는 그때마다 정렬의 기준이 달라질 때 사용하므로 주로 익명 클래스로 사용된다.

## Interface Comparable
정렬 수행시 디폴트로 적용되어 정렬 기준이 되는 메소드를 정의하는 인터페이스이다. Java에서 제공되는 정렬이 가능한 클래스들은 모두 Comparable 인터페이스를 구현하고 있으며, 정렬 시에 이에 맞게 정렬이 수행된다.
```java
// Integer Class : 오름차순 정렬
public final class Integer extend Number implements Comparable<Integer> { ... }
// String Class : 사전순 정렬
public final class String implements java.io.Serializable, Comparable<String>, CharSequence { ... }
```
그렇다면 어떻게 구현을 해야할까? 정렬할 객체에 Comparable interface를 inplements 후, `compareTo()` 메소드를 오버라이드하여 구현한다. 아래는 Comparable interface를 이용한 Point 객체를 정렬하는 예시이다. 

```java
// BOJ 11650: 좌표 정렬하기
import java.io.*;
import java.util.*;

class Point implements Comparable<Point> {
    int x, y;
    Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public int compareTo(Point o) {
        /*
         return 값이 양수인 경우, 두 값의 자리가 변경되면서 정렬된다.
         오름차순 : return this - other
         내림차순 : return other - this
        */
        if (x != o.x) return x - o.x;
        return y - o.y;
    }
}

public class Main {
    static int N;
    static ArrayList<Point> points = new ArrayList<>();
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
        N = Integer.parseInt(br.readLine());
        for (int i = 0, x, y; i < N; i++) {
            StringTokenizer st = new StringTokenizer(br.readLine());
            x = Integer.parseInt(st.nextToken());
            y = Integer.parseInt(st.nextToken());
            points.add(new Point(x, y));
        }
        Collections.sort(points);
        for (Point point : points) {
            bw.write(point.x + " " + point.y + "\n");
        }
        bw.flush();
        bw.close();
    }
}
```

## Interface Comparator
정렬 가능한 클래스(Comparable 인터페이스를 구현한 클래스)들의 default 정렬 기준과 다르게 정렬하고 싶을 때 사용하는 인터페이스이다. 주로 익명 클래스를 이용하여 사용되며, 기본적인 정렬 방법인 오름차순 정렬을 내림차순으로 정렬할 때 많이 사용된다. 그렇다면 어떻게 구현해야할까? Comparator interface를 implements 후 `compare()` 메서드를 오버라이드한 새로운 클래스를 작성한다. 이후 `Arrays.sort()`, `Collections.sort()` 메소드의 두 번째 인자로 Comparator interface를 전달해준다. 우선순위 큐도 생성자의 두 번째 인자로 Comparator interface를 전달할 수 있다. 

> `PriorityQueue(int initialCapacity, Comparator<? super E> comparator)`

아래는 Comparator interface를 이용한 Point 객체를 정렬한 예시이다.
```java
// BOJ 11650: 좌표 정렬하기
import java.io.*;
import java.util.*;

class Point {
    int x, y;
    Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}

public class Main {
    static int N;
    static ArrayList<Point> points = new ArrayList<>();
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
        N = Integer.parseInt(br.readLine());
        for (int i = 0, x, y; i < N; i++) {
            StringTokenizer st = new StringTokenizer(br.readLine());
            x = Integer.parseInt(st.nextToken());
            y = Integer.parseInt(st.nextToken());
            points.add(new Point(x, y));
        }
        Comparator<Point> pointComparator = (o1, o2) -> {
            if (o1.x != o2.x) return o1.x - o2.x;
            return o1.y - o2.y;
        };
        Collections.sort(points, pointComparator);
        for (Point point : points) {
            bw.write(point.x + " " + point.y + "\n");
        }
        bw.flush();
        bw.close();
    }
}
```