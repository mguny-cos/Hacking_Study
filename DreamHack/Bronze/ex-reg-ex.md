# 1. 문제 파악하기
우선 이 문제가 무엇을 구하는 것인지 파악해야 한다.<br>
문제 파일을 다운 받아보니 파이썬로 작성된 코드가 나온다.

```python
#!/usr/bin/python3
from flask import Flask, request, render_template
import re

app = Flask(__name__)

try:
    FLAG = open("./flag.txt", "r").read()       # flag is here!
except:
    FLAG = "[**FLAG**]"

@app.route("/", methods = ["GET", "POST"])
def index():
    input_val = ""
    if request.method == "POST":
        input_val = request.form.get("input_val", "")
        m = re.match(r'dr\w{5,7}e\d+am@[a-z]{3,7}\.\w+', input_val)
        if m:
            return render_template("index.html", pre_txt=input_val, flag=FLAG)
    return render_template("index.html", pre_txt=input_val, flag='?')

app.run(host="0.0.0.0", port=8000)

```

이 코드를 기반으로 만들어진 사이트를 들어가보자.<br>
<img width="765" height="203" alt="image" src="https://github.com/user-attachments/assets/04dc093f-2934-49dc-8686-99f1e1ca4a0c" /><br>
<br>
사이트에 들어가보니 입력할 수 있는 곳이 나온다.<br>
<br>
문제를 파악해 보자면 17번째 줄에
```python
m = re.match(r'dr\w{5,7}e\d+am@[a-z]{3,7}\.\w+', input_val)
```
이라는 정규표현식이 있다. 이 정규 표현식과 매치되는 문자를 입력하면 FLAG를 얻을 수 있는 코드임을 알 수 있다.<br>
여기서 `re.match`함수는 문자열 시작 부분에서 패턴에 매치하는 문자열을 검색한다.

# 2. 정규 표현식 해석하기
정규 표현식과 매치되는 문자가 뭔지를 알아야 하는 문제이기 때문에 정규 표현식을 해석해봐야 한다.<br>
https://regexper.com/ 이 사이트로 들어가 정규표현식을 입력하면 정규표현식을 시각화 할 수 있다. 시각화를 해보면<br>
<br>
<img width="566" height="86" alt="image" src="https://github.com/user-attachments/assets/0fd9aca5-13ac-4ebc-9b36-92dc48181aa3" />
<br>
이렇게 결과가 나온다. 해석을 해보자면 <br>`dr + 영문, 숫자, 밑줄(_) 중에서 4~6개 + e + 정수 + am@ + a~z사이 알파벳 하나 2~6번 + . + 아무 문자 하나`<br> 라고 볼 수 있다.
나는 `drrrrrre1am@bbb.b`으로 했다. 결과는<br>
<img width="560" height="49" alt="image" src="https://github.com/user-attachments/assets/6014320f-4f93-4afd-99b4-22a9cf5de4dd" /><br>
성공적으로 FLAG를 찾아낼 수 있었다.
