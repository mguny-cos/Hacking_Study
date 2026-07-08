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
