# phpreg
| 항목 | 내용 |
|------|------|
| 📅 풀이 날짜 | 2026-07-08 |
| 📚 해킹 분야 | Web Hacking |
| 🔗 문제 링크 | [phpreg](https://dreamhack.io/wargame/challenges/873)|


## 분석
웹페이지에 접속하면 아래의 화면이 보인다.
<img width="1143" height="479" alt="image" src="https://github.com/user-attachments/assets/03edbb51-e126-40b1-a518-86223156aedb" />
이 웹페이지에서 무엇을 할지 알기 위해 문제파일을 다운받아 웹페이지 제작에 사용 된 php소스코드를 보도록 하자.<br>
문제파일에는 `index.php`와 `step2.php` 파일이 있다.<br>
플래그 위치는 `../dream/flag.txt` 이라고 한다.

## `index.php`
```html
<div class="container">
      <div class="box">
        <h4>Step 1 : Open the door & Go to Step 2 !!</h4>
        <div class="door"><div class="door_cir"></div></div>
        <p>
          <form method="post" action="/step2.php">
              <input type="text" placeholder="Nickname" name="input1">
              <input type="text" placeholder="Password" name="input2">
              <input type="submit" value="제출">
          </form>
        </p>
      </div>
    </div> 
```

위의 코드는 `index.php`코드의 일부이다. 웹페이지 메인 화면에서 보였던 `Nickname`칸과 `Password` 작성하는 칸을 각각 `input1`과 `input2`에 저장한 후 `step2.php`문서에 POST방식으로 보내는 형식으로 작동하는 것을 알 수 있다.

## `step2.php`
```php
<?php
          // POST request
          if ($_SERVER["REQUEST_METHOD"] == "POST") {
            $input_name = $_POST["input1"] ? $_POST["input1"] : "";
            $input_pw = $_POST["input2"] ? $_POST["input2"] : "";

            // pw filtering
            if (preg_match("/[a-zA-Z]/", $input_pw)) {
              echo "alphabet in the pw :(";
            }
            else{
              $name = preg_replace("/nyang/i", "", $input_name);
              $pw = preg_replace("/\d*\@\d{2,3}(31)+[^0-8\"]\!/", "d4y0r50ng", $input_pw);
              
              if ($name === "dnyang0310" && $pw === "d4y0r50ng+1+13") {
                echo '<h4>Step 2 : Almost done...</h4><div class="door_box"><div class="door_black"></div><div class="door"><div class="door_cir"></div></div></div>';

                $cmd = $_POST["cmd"] ? $_POST["cmd"] : "";

                if ($cmd === "") {
                  echo '
                        <p><form method="post" action="/step2.php">
                            <input type="hidden" name="input1" value="'.$input_name.'">
                            <input type="hidden" name="input2" value="'.$input_pw.'">
                            <input type="text" placeholder="Command" name="cmd">
                            <input type="submit" value="제출"><br/><br/>
                        </form></p>
                  ';
                }
                // cmd filtering
                else if (preg_match("/flag/i", $cmd)) {
                  echo "<pre>Error!</pre>";
                }
                else{
                  echo "<pre>--Output--\n";
                  system($cmd);
                  echo "</pre>";
                }
              }
              else{
                echo "Wrong nickname or pw";
              }
            }
          }
          // GET request
          else{
            echo "Not GET request";
          }
      ?>
```
이 코드의 주요 기능은 아래와 같다<br>
1. POST로 전달 받은 `input1` 과 `input2`변수를 각각 `input_name`과 `input_pw`에 저장한다.
2. `input_pw`에 저장된 값을 검사한다.<br><br>
   php코드에서 `preg_match`함수는 매치되는 값이 있는지 검사하는 역할을 하는 함수이다. 이 코드에서는
   ```php
   if (preg_match("/[a-zA-Z]/", $input_pw)) {
     echo "alphabet in the pw :(";
   }
   ```
   이렇게 사용되어 있다. 이는 `input_pw`에 어떤 알파벳이든 들어가 있으면 `alphabet in the pw :(` 가 출력되게 한다.
3. `input_name`과 `input_pw`에 저장된 값을 검사하고 다른 문자로 대체한다.<br><br>
    php 코드에서 `preg_replace`함수는 매치되는 값이 있는지 검사하고 검사한 값을 다른 문자로 대체하는 역할을 한다. 이 코드에서는
   ```php
   $name = preg_replace("/nyang/i", "", $input_name);
   $pw = preg_replace("/\d*\@\d{2,3}(31)+[^0-8\"]\!/", "d4y0r50ng", $input_pw);
   ```
   이렇게 사용되었다. 해석을 해보자면 `input_name`에 `nyang`이라는 문자가 있으면 `공백`으로 대체하여 `name`변수에 저장하겠다는 뜻이고<br>
   `input_pw`에 `/\d*\@\d{2,3}(31)+[^0-8\"]\!/` 이 정규 표현식과 매치하는 값이면 `d4y0r50ng`로 바꾸어 `pw`변수에 저장하겠다는 뜻이다.<br>
   그 다음 줄을 보면
   ```php
   if ($name === "dnyang0310" && $pw === "d4y0r50ng+1+13") {
     echo '<h4>Step 2 : Almost done...</h4><div class="door_box"><div class="door_black"></div><div class="door"><div class="door_cir"></div></div></div>';
   ```
   `name`변수에 있는 값이 `dnyang0310`과 같고 `pw`변수에 있는 값이 `d4y0r50ng+1+13`과 같다는 이 두가지 조건이 충족되면 step2로 넘어가는 식이다.
4. `cmd`값 입력 받기
   ```php
   $cmd = $_POST["cmd"] ? $_POST["cmd"] : "";

   if ($cmd === "") {
     echo '
       <p><form method="post" action="/step2.php">
           <input type="hidden" name="input1" value="'.$input_name.'">
           <input type="hidden" name="input2" value="'.$input_pw.'">
           <input type="text" placeholder="Command" name="cmd">
           <input type="submit" value="제출"><br/><br/>
       </form></p>
     ';
    }
   ```
   이 부분이 `cmd`값을 입력받는 곳이다. 입력받은 값을 `cmd`변수에 저장한다.
5. 입력된 `cmd`검사하기
   ```php
   else if (preg_match("/flag/i", $cmd)) {
     echo "<pre>Error!</pre>";
   }
   ```
   `preg_match`함수를 사용하여 `cmd`변수에 `flag`문자가 들어가면 `Error!`를 출력하는 기능을 한다.
6. `system` 함수 호출하기
   ```php
   else{
     echo "<pre>--Output--\n";
     system($cmd);
     echo "</pre>";
    }
   ```
   위의 모든 과정을 통과하면 `system`함수를 호출 시킨 후 `system`함수에 `cmd`변수에 있는 값을 넣어 실행시키고 결과를 출력한다.

## 풀이
<img width="558" height="318" alt="image" src="https://github.com/user-attachments/assets/592d4a7b-a3f2-447e-9734-b0fb1a16a533" /><br>
분석과정에서 얻은 정보에 따르면 위의 화면에서
`Nickname`칸은 `input_name`에 저장되는 값이므로 `dnyang0310`가 들어가야 하며 `nyang`글자를 입력하면 공백으로 대체된다. <br>
`Password`칸은 `input_pw`에 저장되는 값이므로 `d4y0r50ng+1+13`가 들어가야 하며 문자가 들어가면 안되고 `/\d*\@\d{2,3}(31)+[^0-8\"]\!/`이 정규표현식에 매치되는 값을 입력하면 `d4y0r50ng`으로 대체된다.<br><br>
우선 `/\d*\@\d{2,3}(31)+[^0-8\"]\!/`이 [정규표현식을 시각화 해주는 사이트](https://regexper.com) 에서 시각화를 해보면 다음과 같이 나온다.
<img width="453" height="95" alt="image" src="https://github.com/user-attachments/assets/0b622e01-05df-4958-973c-ce2fd9bf8215" /><br>
시각화 결과를 보면 `정수 0개 이상 + @ + 2개 이상 3개 이하의 정 + 31 + 0-8 혹은 " 를 제외한 문자 한 개+ !` 이에 매치되는 문자를 입력하면 매치되는 정규 표현식이다. 여기서 시각화 한 사진에는
정규표현식에서 `\d{2,3}` 부분이 `digit 1...2times`라고 표시 되어있는데 이는 오류로 보인다. `\d{2,3}` 이 정규 표현식은 `정수를 2회 이상 3회 이하` 넣으라는 뜻이므로 정규표현식 대로 진행해주면 된다.<br>
나는 `1@34319!`로 하겠다.<br>
<br>
정규 표현식은 해석을 완료 하였으니 `Nickname`칸에 `dnyang0310`을 어떻게 넣을지가 관건이다.<br>
`nyang`을 직접 넣으면 공백으로 치환하니 이를 이용하면 되겠다. `dnynyangang0310`이런 식으로 입력하면 `dny***nyang***ang` nyang 부분이 공백 처리 되면서 `dnyang0310`이 입력되게 된다.<br>
<br>
`Nickname`칸도 해결 하였으니 이제 `Password`칸을 해결하면 된다.<br>
이 웹페이지 php코드에서 요구하는 `password`칸 입력값은 `d4y0r50ng+1+13`이지만
 ```php
   if (preg_match("/[a-zA-Z]/", $input_pw)) {
     echo "alphabet in the pw :(";
   }
 ```
이 코드 때문에 문자 입력이 되지 않으므로<br>
정규 표현식을 해석하여 나온 결과인 `1@34319!`를 입력하여 `d4y0r50ng`로 변환시킨다. 위의 php코드에서 요구하는 입력값은 `d4y0r50ng+1+13` 이므로<br>
`1@34319!`이 정규 표현식에 `+1+13`을 붙인 `1@34319!+1+13`를 입력해주면 되겠다.<br>
<img width="455" height="339" alt="image" src="https://github.com/user-attachments/assets/dc802bb3-67e0-4b6c-969a-b9e36dd3621e" /><br>
<img width="296" height="334" alt="image" src="https://github.com/user-attachments/assets/c6784d6e-d7b7-4d14-8966-558393aef68c" /><br>
위의 사진과 같이 성공적으로 `step2`로 넘어올 수 있다.<br>
<br>
이 화면에서 해야 할 일은 `분석 4번`부터 관련이 있다. `cmd` 변수에 웹페이지 `Command` 칸에 입력받은 값을 `system`함수에서 실행시키는 것이 이 웹페이지의 기능이다.<br>
`system` 함수는 셸을 통해 외부 프로그램을 실행해주는 역할을 해주므로 `Command`칸에 입력되어야 하는 명령어는 파일의 내용을 볼 수 있는 명령어인 `cat`명령어이다. 따라서 입력해야 하는 명령어 전체는 `cat ../dream/flag.txt/`가 되어야 하는데..
 ```php
   else if (preg_match("/flag/i", $cmd)) {
     echo "<pre>Error!</pre>";
   }
 ```
이 부분 때문에 `flag`라는 글자는 못들어간다.<br>
해결 방법은 `와일드 카드(*)`를 사용하는 것이다. 셸에는 와일드 카드라는 것이 존재한다. 파일 명을 모르지만 실행은 시키고 싶을 때 사용하는 것이 와일드 카드이다. 사용법은 `*`을 붙이는 것이다. 이 `*`의 의미는 `0개 이상의 임의의 문자와 매칭하겠다.`라는 뜻이다. 예를 들면
| 패턴 | 매칭되는 파일 |
|------|------|
| `*.txt` | `a.txt`, `flag.txt`, `hello.txt` |
| `fla*.txt` | `flag.txt`, `flash.txt`, `flame.txt` |
<br>
이런 식으로 기능을 한다. 따라서 이 와일드 카드를 사용하면 해결할 수 있다. `cat ../dream/flag.txt/ `를 입력하면 `flag`가 걸러지게 되니까 `cat ../dream/fla*.txt`를 입력해주자.<br>
<img width="787" height="368" alt="image" src="https://github.com/user-attachments/assets/8337e3ea-445b-4162-bc6b-bebb7a8ca09d" /><br>
<img width="1008" height="345" alt="image" src="https://github.com/user-attachments/assets/b8054e2c-bae5-4d0d-94e8-6fcfc67613c4" /><br>
위의 사진과 같이 FLAG를 성공적으로 획득하여 문제를 풀 수 있게 된다.

## 마무리
이 문제는 웹페이지의 PHP 코드와 정규 표현식과 셸의 기능을 알고 있어야 풀 수 있는 문제였다. 나는 `PHP 기초 지식`과 `정규 표현식의 지식`이 있어 이 부분은 어렵지 않았지만 셸 기능중 하나인 `와일드 카드`기능과 `system()`함수를 알고 있지 못해 많은 어려움을 겪었다. AI의 도움을 받아 풀 수 있었지만 나 자신의 힘으로 풀어내지 못해 아쉬움이 남는다.




