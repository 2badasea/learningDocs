# 코딩테스트 문제 풀이 전략: 배열과 문자열

## < 1. 배열 (Array) >

### 1. 2차원 배열의 이해
2차원 배열은 x축(열)과 y축(행)으로 구성되어 있는 좌표로 생각하면 편리
**주의할 점:** 코드로 구현할 때는 우리가 생각하는 x, y 좌표와 달리, **행(y)에 먼저 접근한 후 열(x)에 접근**. 즉, `arr[행][열]` 순서로 접근

```java
public class ArrayExample {
    public static void main(String[] args) {
        // 3행(y) 3열(x) 크기의 2차원 배열 생성
        int[][] map = new int[3][3];
        
        map = {
                { 1, 2, 3},
                { 4, 5, 6},
                { 7, 8, 9}
            };

        // 올바른 접근 순서: map[y][x] 
        // ex) 6의 위치 구하기
        map[y][x] = ?;
    }
}
```

### 2. 배열의 경계 체크
배열에선 이동하려는 위치가 배열의 범위를 벗어나는지 반드시 확인 필요
또한 y축(행)을 먼저 체크하는 것이 안전한데, 예를 들어 `arr[y]`가 유효해야 `arr[y].length`를 참조할 때 `ArrayIndexOutOfBoundsException`을 방지할 수 있기 때문

```java
// 프로그래머스 <거리두기 확인하기> 풀이 소스 중 일부
private boolean isDistanced(char[][] room, int x, int y) {
    for (int d = 0; d < 4; d++) {
        int nx = x + dx[d];
        int ny = y + dy[d];
        // 탐색예정인 위치가 방을 벗어난 경우엔 패스
        if (ny < 0 || ny >= room.length || nx < 0 || nx >= room[ny].length) {
            continue;
        }
        // 각 탐색공간이 P라면 false리턴. 칸막이(X)는 검증 불필요
        switch (room[ny][nx]) {
            case 'P':
                return false;
            case 'O':
                // 빈테이블인 경우, 다시 탐색을 진행한다.
                if (isNextToVolunteer(room, nx, ny, 3 - d)) {
                    return false;
                }
                break;
        }
    }
    return true;
}
```

---

## < 2. 문자열 (String) >

### 1. 기본 개념
* 문자열은 '문자(char)의 배열'
* 표기: 문자는 작은따옴표(`' '`), 문자열은 큰따옴표(`" "`)를 사용

### 2. 아스키코드와 형 변환
문자는 내부적으로 정수(아스키코드)로 취급되어 연산  ('0' => 48, 'A' =>65,  'a' => 97)
* **숫자형 문자 → 정수 변환:** `'0'`을 빼줍니다.
    * `int digit = '9' - '0'; // 9`
<br>
* **대소문자 변환:** `char`끼리 연산하면 `int`로 변환되므로 결과에 다시 `(char)` 캐스팅이 필요
    * `char upper = (char)(lower - ('a' - 'A'));`
    * **원리:** 아스키코드 상에서 소문자 'a'(97)와 대문자 'A'(65)의 차이는 32 모든 알파벳은 순서대로 배치되어 있으므로, 소문자에서 이 차이값(32)만큼 빼면 대응되는 대문자 코드가 됩니다.
  
        ```java
        char lower = 'c'
        char upper = (char) (lower - ('a' - 'A'));

        // 반대로 다시 소문자로 변경
        char lower = (char) (upper + ('a' - 'A'));
        ```
<br>

### 3. StringBuilder 사용 이유와 효율성
자바의 `String`은 불변(Immutable) 객체. 
`+` 연산자로 문자열을 계속 이어 붙이면 성능에 좋지 않음(시간복잡도 관련)

* **String 연결의 시간 복잡도가 $O(N^2)$인 이유:**
    * `String`에 문자를 하나 추가할 때마다 새로운 크기의 배열을 할당하여 내용을 **복사**하는 비용 발생
    * 길이가 1인 문자열 생성(1만큼 복사) → 길이 2 생성(2만큼 복사) → ... → 길이 $N$ 생성($N$만큼 복사)
    * 연산 횟수 합: $1 + 2 + ... + N = \frac{N(N+1)}{2}$
    * 따라서 시간 복잡도는 $O(N^2)$
  ```java
    public static void main(String[] args) {
        String intro = "mynameis"; // 초기 길이: 8
        String name = "leebada";   // 붙일 문자열

        // 반복문을 통해 한 글자씩 붙이기
        for (int i = 0; i < name.length(); i++) {
            char c = name.charAt(i);
            
            intro += c; 
        }
    }

  ```

* **StringBuilder 해결책:**
    * `StringBuilder`는 내부 가변 배열을 사용하여 매번 객체를 새로 만들지 않고 기존 데이터 뒤에 붙이는 동작 원리를 지님
    * 따라서 `append()` 연산의 기대 시간 복잡도는 O(1)
<br>

### 4. 문자열 비교 (NPE 방지)
문자열은 객체이므로 `==`가 아닌 `.equals()`를 사용. 이때 `NullPointerException`(NPE)을 주의!

* **NPE 방지:** 비교 대상 중 확실하게 값이 존재하는 문자열을 앞세워 `.equals()`를 호출
```java
String name = null;

// 'name' null이면 NPE 발생
if (name.equals("bada")) { ... } 

// "bada"는 절대 null이 아니므로 안전하게 false 반환 (안전함)
if ("bada".equals(name)) { ... } 
```
<br>
