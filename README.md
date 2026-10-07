# Практична робота № 2

**Дисципліна:** Основи побудови інформаційних систем та мереж (ОК-13)

**Тема:** Структура повідомлень прикладного протоколу HTTP. Формування запиту вручну

| Поле | Значення |
|---|---|
| Студент (прізвище, ім'я, по батькові) | Жигало Влад |
| Група | F5 2.01 |
| Номер варіанта | 7 |
| Індивідуальний домен | mercurial-scm.org |
| «Чужий» домен для завдання A.3.1 (варіант ± 15) | example.net, варіант 22 |
| Середовище виконання | Windows 11; PowerShell 7.6.5; curl 8.21.0; OpenSSL 3.5.4 |
| Дата виконання | 07.10.2026 |

Для A.3.1 використано правило з PDF завдання: 7 + 15 = 22. У бланку позначку «± 20» виправлено на «± 15» відповідно до завдання.

У A.1–A.4 використано засоби PowerShell із Додатка Г. Текст запиту записувався у змінній та надсилався через TCP-з'єднання; інтерактивне введення з клавіатури не відтворювалося. У командних блоках наведено код окремих дослідів. Виводи містять повні відповіді, отримані зі з'єднання. Час у полі Date подано сервером у GMT.

---

## Частина A. Збір експериментальних даних

### Завдання A.1. Формування запиту вручну

**Команда:**

```powershell
$d = "mercurial-scm.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$s.ReadTimeout = 15000
$w = New-Object System.IO.StreamWriter($s, [System.Text.Encoding]::ASCII)
$w.Write("GET / HTTP/1.1`r`nHost: $d`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Набраний запит:**

```http
GET / HTTP/1.1
Host: mercurial-scm.org
Connection: close

```

**Відповідь:**

```http
HTTP/1.1 404 Not Found
Server: nginx/1.18.0
Date: Wed, 07 Oct 2026 08:29:51 GMT
Content-Type: text/html
Content-Length: 153
Connection: close

<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.18.0</center>
</body>
</html>
```

---

### Завдання A.2. Запит без поля `Host` у версії 1.1

**Команда:**

```powershell
$d = "mercurial-scm.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$s.ReadTimeout = 15000
$w = New-Object System.IO.StreamWriter($s, [System.Text.Encoding]::ASCII)
$w.Write("GET / HTTP/1.1`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Вивід:**

```http
HTTP/1.1 400 Bad Request
Server: nginx/1.18.0
Date: Wed, 07 Oct 2026 08:29:51 GMT
Content-Type: text/html
Content-Length: 157
Connection: close

<html>
<head><title>400 Bad Request</title></head>
<body>
<center><h1>400 Bad Request</h1></center>
<hr><center>nginx/1.18.0</center>
</body>
</html>
```

---

### Завдання A.3. Вплив поля `Host` на відповідь сервера

#### A.3.1. Чуже доменне ім’я в полі `Host`

**Команда:**

```powershell
$d = "mercurial-scm.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$s.ReadTimeout = 15000
$w = New-Object System.IO.StreamWriter($s, [System.Text.Encoding]::ASCII)
$w.Write("GET / HTTP/1.1`r`nHost: example.net`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Вивід:**

```http
HTTP/1.1 404 Not Found
Server: nginx/1.18.0
Date: Wed, 07 Oct 2026 08:29:52 GMT
Content-Type: text/html
Content-Length: 153
Connection: close

<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.18.0</center>
</body>
</html>
```

#### A.3.2. Неіснуюче ім’я в полі `Host`

**Команда:**

```powershell
$d = "mercurial-scm.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$s.ReadTimeout = 15000
$w = New-Object System.IO.StreamWriter($s, [System.Text.Encoding]::ASCII)
$w.Write("GET / HTTP/1.1`r`nHost: opism-pr02.invalid`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Вивід:**

```http
HTTP/1.1 404 Not Found
Server: nginx/1.18.0
Date: Wed, 07 Oct 2026 08:29:52 GMT
Content-Type: text/html
Content-Length: 153
Connection: close

<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.18.0</center>
</body>
</html>
```

#### A.3.3. Запит без поля `Host` у версії 1.0

**Команда:**

```powershell
$d = "mercurial-scm.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$s.ReadTimeout = 15000
$w = New-Object System.IO.StreamWriter($s, [System.Text.Encoding]::ASCII)
$w.Write("GET / HTTP/1.0`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Вивід:**

```http
HTTP/1.1 301 Moved Permanently
Server: nginx/1.18.0
Date: Wed, 07 Oct 2026 08:29:53 GMT
Content-Type: text/html
Content-Length: 169
Connection: close
Location: https://www.mercurial-scm.org/

<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx/1.18.0</center>
</body>
</html>
```

Зведення результатів наведено в **Додатку Д**.

---

### Завдання A.4. Два запити в одному з’єднанні

**Команда:**

```powershell
$d = "mercurial-scm.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$s.ReadTimeout = 15000
$w = New-Object System.IO.StreamWriter($s, [System.Text.Encoding]::ASCII)
$w.Write("GET /opism-pr02-12345 HTTP/1.1`r`nHost: $d`r`n`r`nGET / HTTP/1.1`r`nHost: $d`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Вивід:**

```http
HTTP/1.1 404 Not Found
Server: nginx/1.18.0
Date: Wed, 07 Oct 2026 08:29:53 GMT
Content-Type: text/html
Content-Length: 153
Connection: keep-alive

<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.18.0</center>
</body>
</html>
HTTP/1.1 404 Not Found
Server: nginx/1.18.0
Date: Wed, 07 Oct 2026 08:29:53 GMT
Content-Type: text/html
Content-Length: 153
Connection: close

<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.18.0</center>
</body>
</html>
```

**Кількість отриманих відповідей:** 2.

**Коди стану отриманих відповідей:** `404 Not Found`, `404 Not Found`.

---

### Завдання A.5. Запит за допомогою клієнтської програми

**Команда:**

```powershell
curl.exe -v --http1.1 http://mercurial-scm.org/ -o NUL
```

**Вивід:**

```text
*   Trying 140.211.166.78:80...
* Host mercurial-scm.org:80 was resolved.
* IPv6: (none)
* IPv4: 140.211.166.78
* Established connection to mercurial-scm.org (140.211.166.78 port 80) from 192.168.0.134 port 64481 
  % Total    % Received % Xferd  Average Speed  Time    Time    Time   Current
                                 Dload  Upload  Total   Spent   Left   Speed

  0      0   0      0   0      0      0      0                              0* using HTTP/1.x
> GET / HTTP/1.1
> Host: mercurial-scm.org
> User-Agent: curl/8.21.0
> Accept: */*
> 
* Request completely sent off
< HTTP/1.1 404 Not Found
< Server: nginx/1.18.0
< Date: Wed, 07 Oct 2026 08:30:13 GMT
< Content-Type: text/html
< Content-Length: 153
< Connection: keep-alive
< 
{ [153 bytes data]

100    153 100    153   0      0    310      0                              0
100    153 100    153   0      0    310      0                              0
100    153 100    153   0      0    309      0                              0
* Connection #0 to host mercurial-scm.org:80 left intact
```

---

### Завдання A.6. Запит через захищене з'єднання

**Ресурс, на якому виконано завдання:** `iana.org`.

**Підстава для використання резервного ресурсу:** у A.1 отримано `404 Not Found`, а не код класу 3xx. За умовою завдання в цьому випадку A.6 виконують на `iana.org`.

**Команда:**

```powershell
& "C:\Program Files\Git\usr\bin\openssl.exe" s_client -connect iana.org:443 -servername iana.org -crlf -quiet
```

**Набраний запит:**

```http
GET / HTTP/1.1
Host: iana.org
Connection: close

```

**Вивід:**

```text
Connecting to 192.0.43.8
depth=2 C=GB, O=Sectigo Limited, CN=Sectigo Public Server Authentication Root R46
verify return:1
depth=1 C=GB, O=Sectigo Limited, CN=Sectigo Public Server Authentication CA OV R36
verify return:1
depth=0 C=US, ST=California, O=Internet Corporation For Assigned Names and Numbers, CN=*.iana.org
verify return:1
HTTP/1.1 301 Moved Permanently
Date: Wed, 07 Oct 2026 08:30:18 GMT
Server: Apache
Location: https://www.iana.org/
Cache-Control: max-age=345600
Expires: Sun, 11 Oct 2026 08:30:18 GMT
Content-Length: 229
Connection: close
Content-Type: text/html; charset=iso-8859-1

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="https://www.iana.org/">here</a>.</p>
</body></html>
100000000A000000:error:0A000126:SSL routines::unexpected eof while reading:ssl/record/rec_layer_s3.c:696:
```

Захищене з'єднання встановлено, отримано HTTP-відповідь `301 Moved Permanently` з адресою `https://www.iana.org/`. Наприкінці OpenSSL повідомив `unexpected eof while reading`: під час завершення з'єднання він не отримав очікуваного завершення TLS. Повідомлення залишено у повному виводі; перед ним уже надійшли заголовки та тіло HTTP-відповіді.

---

## Частина B. Розбір полів заголовка

Розбирається відповідь, отримана в завданні A.1.

**Загальна кількість полів заголовка у відповіді:** 5. Рядок `HTTP/1.1 404 Not Found` є стартовим рядком, тому до цієї кількості не входить.

| № | Поле заголовка | Значення | Призначення (власне формулювання) | Походження: сервер / проміжний вузол / не визначено | Обґрунтування |
|---|---|---|---|---|---|
| 1 | Server | nginx/1.18.0 | Повідомляє назву та версію програми, яка обслуговує запит. | сервер (найімовірніше) | Та сама назва nginx/1.18.0 є у тілі сторінки помилки. Проте лише цей рядок не доводить, що це кінцевий сервер. |
| 2 | Date | Wed, 07 Oct 2026 08:29:51 GMT | Указує дату і час формування відповіді. | не визначено | Таке поле може сформувати сервер або проміжний вузол. Самого значення часу недостатньо, щоб їх розрізнити. |
| 3 | Content-Type | text/html | Показує, що в тілі відповіді міститься HTML. | сервер (найімовірніше) | Тіло справді є HTML-сторінкою помилки nginx. |
| 4 | Content-Length | 153 | Указує розмір тіла відповіді: 153 байти. | сервер (найімовірніше) | Розмір відповідає отриманій сторінці помилки. Ознак зміни тіла проміжним вузлом не видно. |
| 5 | Connection | close | Повідомляє, що після відповіді з'єднання буде закрито. | не визначено | Поле стосується поточного з'єднання. Воно може надійти від сервера або від вузла між клієнтом і сервером. |

Позначка «сервер» тут є припущенням за отриманою відповіддю. Явних ознак окремого проміжного вузла в A.1 немає.

---

## Частина D. Висновки

**D.1.** Що з поведінки сервера виявилося неочевидним або несподіваним. Конкретно, з посиланням на рядок виводу.

Неочікуваним було те, що навіть із правильним `Host: mercurial-scm.org` у A.1 отримано рядок `HTTP/1.1 404 Not Found`. У A.3.1 із `Host: example.net` та у A.3.2 із вигаданим іменем відповідь мала той самий код і те саме тіло розміром 153 байти. Отже, у цих трьох пробах заміна імені не змінила результату. Водночас без Host у HTTP/1.1 сервер повернув `400 Bad Request`, а в HTTP/1.0 без цього поля — `301 Moved Permanently` і `Location: https://www.mercurial-scm.org/`. Це показує, що важливим є не тільки шлях, а й версія протоколу та поля запиту. У A.4 надійшли дві відповіді `404`: для вигаданого шляху і для головної сторінки. Перша містила `Connection: keep-alive`, тому з'єднання дозволило отримати ще одну відповідь.

**D.2.** Яке з полів заголовка викликало найбільше утруднення при визначенні походження (частина B) та з якої причини.

Найскладніше було визначити походження поля Date. Воно показує час, але не називає вузол, який його додав. Рядок `Server: nginx/1.18.0` називає програму, проте не пояснює, чи працює вона безпосередньо із сайтом, чи передає запити далі. Тому для Date обрано «не визначено», без довільного припущення.

**D.3.** Яке питання залишилося без відповіді після виконання роботи.

Залишилося питання, чому правильний Host у HTTP/1.1 приводить до `404`, а HTTP/1.0 без Host — до перенаправлення. Отримані відповіді показують різницю, але не розкривають налаштування сервера. Для точного пояснення потрібні його налаштування або журнал запитів.

---

## Контрольні питання

**1.** У завданні A.1 сервер не надсилав відповіді, доки не було введено порожній рядок. Чим це зумовлено?

Порожній рядок означає, що заголовки запиту закінчилися. Без нього сервер може чекати наступне поле заголовка. У переданому тексті цей кінець позначено `\r\n\r\n`; у PowerShell це послідовність зі зворотними апострофами: `` `r`n`r`n ``. У цій роботі запит передавався блоком PowerShell, тому окреме очікування після натискання Enter не спостерігалося, але потрібний порожній рядок був у тексті запиту.

**2.** Порівняйте результати завдань A.1, A.2 та A.3.1–A.3.3 (таблиця Додатка Д). За яких значень поля `Host` і за якої версії протоколу сервер обслуговує запит, а за яких — ні? Яку задачу розв'язує поле `Host`? Відповідь має посилатися на конкретні рядки ваших виводів.

У A.1, A.3.1 та A.3.2 сервер прийняв запити HTTP/1.1 з полем Host, але повернув `HTTP/1.1 404 Not Found`: запитаний ресурс не надано. Тіла всіх трьох відповідей однакові. У A.2 без Host отримано `HTTP/1.1 400 Bad Request`, тобто запит відхилено як неправильний. У A.3.3 без Host, але за HTTP/1.0, отримано `HTTP/1.1 301 Moved Permanently` та `Location: https://www.mercurial-scm.org/`. Версія у стартовому рядку відповіді може відрізнятися від версії запиту. Host указує потрібний сайт: одна IP-адреса може обслуговувати кілька доменів. У HTTP/1.1 поле обов'язкове; у HTTP/1.0 такої вимоги немає. Відсутність цієї вимоги сама по собі не гарантує `200 OK`.

**3.** Скільки відповідей надійшло у завданні A.4 і з якими кодами стану? Чи залежить відповідь сервера на порту 80 від запитаного шляху — і що це говорить про роль цього сервера? Якщо надійшла одна відповідь, знайдіть у ній поле заголовка, яке це пояснює, або зазначте, що такого поля немає. Якщо надійшло дві — що це означає для клієнтської програми, яка завантажує сторінку з великою кількістю вкладених ресурсів?

Надійшли дві відповіді, обидві з рядком `HTTP/1.1 404 Not Found`. Для `/opism-pr02-12345` і `/` отримано однакові тіла помилки по 153 байти. Отже, для цих двох шляхів результат не змінився. У цьому досліді вузол на порту 80 повертав сторінку помилки; стверджувати, що він завжди тільки перенаправляє запити, не можна. Перша відповідь містить `Connection: keep-alive`, друга — `Connection: close`. Повторне використання з'єднання дозволяє клієнту отримувати кілька ресурсів без установлення нового TCP-з'єднання для кожного з них.

**4.** Які поля заголовка програма `curl` додала самостійно (завдання A.5)? Ці поля не є обов'язковими — сервер відповів і без них у завданні A.1. З якою метою їх додано?

Curl сам додав `User-Agent: curl/8.21.0` та `Accept: */*`. User-Agent повідомляє, яка програма надсилає запит. Accept зі значенням `*/*` означає, що клієнт приймає будь-який тип вмісту. Також curl сформував обов'язкове для HTTP/1.1 поле `Host: mercurial-scm.org`. У A.1 поля User-Agent та Accept не надсилалися, і відповідь однаково була отримана.

**5.** За якими ознаками у вашому виводі виявляється присутність проміжного вузла? Якщо таких ознак не виявлено, поясніть, що з цього випливає.

У виводах цих дослідів немає явних позначок окремого проміжного вузла: наприклад, полів Via, Age або спеціальних позначок служби доставки вмісту. `Server: nginx/1.18.0` указує на програму, але nginx може працювати і як сервер, і як посередник. Тому за цими виводами підтвердити наявність такого вузла не вдалося. Це також не доводить, що проміжних вузлів немає.

**6.** Три рядки, про які не йшлося на лекції, наведено в **Додатку В**.

---

## Додаток В. Відповіді на питання 6

| № | Рядок виводу | Джерело (номер завдання) |
|---|---|---|
| 1 | `depth=2 C=GB, O=Sectigo Limited, CN=Sectigo Public Server Authentication Root R46` | A.6 |
| 2 | `verify return:1` | A.6 |
| 3 | `Cache-Control: max-age=345600` | A.6 |

---

## Додаток Д. Зведення результатів завдання A.3

**Вузол, з яким установлювалося з'єднання (у всіх пробах однаковий):** `mercurial-scm.org`, IPv4 `140.211.166.78`, порт 80.

| Проба | Значення поля `Host` | Версія | Код стану | Обсяг тіла відповіді | Збігається з A.1 (так / ні) |
|---|---|---|---|---|---|
| A.1 (вихідна) | mercurial-scm.org | 1.1 | 404 Not Found | 153 байти | — |
| A.2 | поле відсутнє | 1.1 | 400 Bad Request | 157 байтів | ні |
| A.3.1 | example.net | 1.1 | 404 Not Found | 153 байти | так |
| A.3.2 | opism-pr02.invalid | 1.1 | 404 Not Found | 153 байти | так |
| A.3.3 | поле відсутнє | 1.0 | 301 Moved Permanently | 169 байтів | ні |

«Збігається» означає однаковий код стану та однакове тіло відповіді. Date не порівнюється: проби виконувалися в різний час. Обсяг тіла перевірено за отриманим текстом; він відповідає Content-Length.

**Висновок за таблицею (2–4 речення):**

З'єднання в усіх пробах встановлювалося з тим самим вузлом; змінювалися наявність і значення Host, а в A.3.3 — ще й версія протоколу. За HTTP/1.1 правильне, чуже і вигадане ім'я дали однакові код `404` і тіло відповіді. Відсутність Host у HTTP/1.1 дала `400`, а запит HTTP/1.0 без Host — `301` з переходом на `https://www.mercurial-scm.org/`.

---

## Декларування використання технологій штучного інтелекту

Для цієї роботи встановлено **рівень Р3 — ШІ як співвиконавець**.

Виводи команд частини A не згенеровані: їх отримано внаслідок фактичного виконання команд.

**Чи використовувалися технології ШІ під час виконання роботи:** так.

| № | Інструмент (назва, версія) | Етап роботи | Дослівний текст запиту (промпту) | Як використано результат |
|---|---|---|---|---|
| 1 | ChatGPT / Codex; номер версії моделі не зазначено | Вибір програми та підготовка команд | У якій програмі це треба робити, і розкажи потім, що саме туди вставляти? | Отримано пояснення: на Windows використовувати PowerShell, для A.1–A.4 вставляти блоки з Додатка Г зі своїм доменом, для A.5 вводити команду curl.exe, а звіт заповнювати у Markdown. Пояснення використано для підготовки команд і оформлення роботи. |
| 2 | ChatGPT / Codex; номер версії моделі не зазначено | Виконання завдання A.6 | Розкажи, що треба зробити в завданні А6. | Отримано пояснення: спочатку перевірити код A.1, потім установити TLS-з'єднання через OpenSSL на порті 443 і надіслати GET, Host та Connection із порожнім рядком наприкінці. Оскільки у A.1 отримано 404, використано передбачений завданням резервний ресурс iana.org. |

ШІ також допоміг виконати команди, розібрати фактичні відповіді та оформити звіт. Текст пояснень підготовлено простими словами з опорою на лекції 1 і 2. Виводи отримано під час реальних мережевих запитів, а не створено як приклади.

**Підтвердження:** усі виводи команд, наведені в частині A, отримано внаслідок фактичного виконання команд: A.1–A.5 на індивідуальному домені `mercurial-scm.org`, A.6 — на резервному ресурсі `iana.org` відповідно до умови завдання.

---

*ОПІСМ (ОК-13) · Практична робота № 2 · бланк звіту*
