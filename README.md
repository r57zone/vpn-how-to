# Максимально простые инструкции по созданию своего персонального VPN сервера

(старая инстрцкция для Outline - [вот тут](https://github.com/ClusterM/vpn-how-to/tree/outline))

Если в вашей стране по какой-то причине тормозит YouTube, вы можете использовать это руководство, чтобы создать свой собственный VPN сервер. Есть и другие, более простые способы, но я считаю, что это самый надёжный способ, который скорее всего не заблокируют, т.к. у вашего VPN сервера будет свой персональный IP адрес, а в качестве протокола мы будем использовать хорошо замаскированный **vless**. Этот метод достаточно хорошо показывает себя в Китае.

Да, способ платный. Однако, это стоит совсем немного, обычно 500-1000 рублей в месяц. Хотя и это может быть для кого-то много. Но! Вы можете скинуться с друзьями и сделать один общий VPN сервер на всех. Скидываетесь вдесятером - каждый платит по 50-100 рублей. К тому же, всё это делается абсолютно легально и без рисков отправить свои деньги неизвестно кому. Конкретные компании указывать не буду, это обобщённая инструкция.

Необходимые VPN клиенты есть почти под всё: компьютеры, смартфоны, телевизоры и пр.

Итак, приступим...

## Заказываем аренду VPS сервера.
Наверное самый сложный шаг. VPS сервер - это аренда собственного виртуального сервера, т.е. у вас как бы будет свой свобственный компьютер, работающий где-то круглосуточно.

Компаний, которые предоставляют VPS, очень много, я не буду предлагать конкретную Можете просто набрать в Google "VPS". Или "VPS оплата картой мир"/"VPS оплата криптовалютой", если вам принципиален метод оплаты. Это абсолютно легальные услуги, в России много таких компаний, они официально зарегистрированы, никто их не запрещает. Ничего подпольного.

На что стоит обратить внимание при выборе:
* Местонахождение сервера. Обычно VPS хостеры предоставляют разные страны на выбор. Нам нужна какая-то, где проблем с YouTube нет. Это что угодно кроме России и Китая.
* Безлимитный трафик. Мы же не хотим считать, сколько часов в месяц мы может смотреть YouTube. Он почти у всех хостеров безлимитный, но лучше всё-таки проверить.
* Ширина канала. Не особо принципиальная характеристика, но чем он шире, тем быстрее будет работать VPN. Если хотите делить VPN с друзьями, лучше брать пошире. Почему-то далеко не все хостеры указывают эту характеристику у себя на сайте, или её там сложно найти.

Находим на сайте хостера пункт VPS, и там обычно нужно выбрать конфигурацию нашего виртуального компьютера. Что нам нужно:
* Количество процессоро/ядер/cores. Для таких задач обычно хватит одного. Можете взять два, если VPN будет прям очень активно использоваться.
* Оперативная память/RAM. Хватит двух гигабайт.
* Диск/SSD/HDD. Хватит и 10Гб, но так мало обычно не дают выбрать. Выбираем минимум.
* IP адреса. Хостеры обычно предлагают купить дополнительные IP адреса. Именно **дополнительные** нам не нужны. Нам нужен только один. Почти всегда хостеры предоставляется один адрес по умолчанию, но в редком случае бывает иначе.
* Операционная система. Рекомендую Debian. Он сейчас есть у всех хостеров. Очень важно не пропустить момент, где это выбирается, это важно. Если предлагается выбирать разные версии, то лучше самую свежую (сейчас это 12 aka "Bookworm").
* Нам могут навязывать всякое дополнительное ПО вроде панелей управления или CMS - это всё нам не нужно. Нам нужен просто чистый Debian Linux.
* Если можно выбрать ширину канала, то тут уже на ваше усмотрение. Зависит от целей и количества пользователей, как я уже писал выше.

После оплаты обычно нужно некоторое время подождать, пока для вас развернут сервер. После этого вам должны выдать:
* IP адрес сервера
* Логин
* Пароль
Одни хостеры высылают эти данные на почту, другие отображают непосредственно на сайте. Лучше сразу их куда-то сохраните, хоть они скорее всего и понадобятся только один раз.

## Настройкиа VPS

Работает с серверами на Linux происходит через SSH. Для этого есть разные программы. Если у вас Windows, то скачайте putty тут: https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html. Там можно выбрать как установщик, то и просто исполняемый файл. Вот прямая ссылка на версию под 64-битный x86 (скорее всего у вас такой) Windows: https://the.earth.li/~sgtatham/putty/latest/w64/putty.exe.

После запуска putty появляется вот такое окно:

![image](https://github.com/user-attachments/assets/3c7835bc-ccf4-446f-9240-1db26f30cacc)

В поле Host Name вводим IP адрес, который вам выдал хостер (в нашем примере это *135.125.201.170*, замените на свой!). После этого нижимаем кнопку "Open". Если всё верно, то появится такое окно:

![image](https://github.com/user-attachments/assets/13db59ce-9861-4063-8f67-9268ee1072c0)

Тут нужно ввести логин, который вам дал хостер. В программе putty чтобы вставлять скопированный текст нужно кликнуть на окно правой кнопкой мыши. После ввода нажмите enter.

Далее у вас спросят пароль. Тут нужно ввести пароль, который вам дал хостер. Есть нюанс: вводимые символы не отображаются даже в виде звёздочек, т.е. если вы будете печатать пароль, то ничего не будет происходить, такие меры предосторожности. Просто скопируйте пароль, кликните правой кнопкой мыши в окне программы putty и нажмите enter. Должно появиться что-то вроде этого:

![image](https://github.com/user-attachments/assets/ac7c94d3-76bd-4d08-a713-b7734225109c)

Это командная строка Linux. На этом этапе в зависимости от хостера ситуация может немного отличаться, например некоторые хостеры могут сразу попросить вас изменить пароль, постарайтесь внимательно прочитать то, что вам там пишут.

Сначала скорее всего вам надо ввести команду (и нажать `enter`):
```bash
sudo -i
```
Если после ввода этой команды у вас спросят пароль, то введите всё тот же самый ваш пароль, отображаться на экране он опять же не будет. Если всё сделано верно, то в последгей строке перед курсором должен отображться символ `#`:

![image](https://github.com/user-attachments/assets/8543373a-c370-4638-8db5-9dafef94bfb6)

Теперь установим [3x-ui](https://github.com/MHSanaei/3x-ui), для этого просто введите (скопируйте и вставьте правой кнопкой мыши) команду и нажмите `enter`:
```bash
bash <(curl -Ls https://raw.githubusercontent.com/mhsanaei/3x-ui/master/install.sh)
```

![image](https://github.com/user-attachments/assets/8d2c52f7-44f9-4acd-9d57-bb6bd854cf23)

Если всё прошло хорошо, то появится жёлтая надпись, что установка выполнена успешно.

![image](https://github.com/user-attachments/assets/5f07bc04-13c1-4a6a-8b17-21483e292a94)

Дальше будет предложено вручную изменить настройки:

![image](https://github.com/user-attachments/assets/6baafdbb-003e-4791-9dda-7546ced0c5c9)

Если вы не шарите, то давайте лучше откажемся: наберите букву `n` и нажмите `enter`:

![image](https://github.com/user-attachments/assets/c0624d1a-483e-4f33-b8e2-01a20b535a3b)

Дальше зелёными буквами вам должны будут вывестись ваши настройки, как-то так:

![image](https://github.com/user-attachments/assets/926eff7b-8444-4b2b-97db-7487a093570b)

Если из данных отобразиться только webBasePath, то введите `x-ui`, нажмите `enter`, после чего введите `10` и снова нажмите `enter`, чтобы отобразались все необходимые данные.

Сохраните эти данные себе куда-нибудь в надёжное место! А putty нам больше не нужен, его можно закрывать.

Теперь мы уже можем настраивать наш собственный VPN сервер через красивый веб-интерфейс. Осталось только в него попасть, для этого нам придётся собрать ссылку на него по частям из данных, что нам дали выше. Откройте их в любой текстовом редакторе и соберите ссылку таким образом:

**http://`<IP>`:`<Port>`/`<WebBasePath>`/**

В нашем примере хостер дал IP адрес *135.125.201.170*, а программа выдала порт *48996* и WebBasePath *AY7ZGed24cR4uqW*, получается такая строка:

![image](https://github.com/user-attachments/assets/b92c461f-a08f-41ef-b2bd-a74333f85e42)

```
http://135.125.201.170:48996/AY7ZGed24cR4uqW/
```

Это ссылка на админку нашего VPN-сервера. Открываем её в любом браузере (сохраните её сразу куда-нибудь в закладки). Браузер может предупредить, что соединение небезопасно (т.к. у нас нет шифрования):

![image](https://github.com/user-attachments/assets/27580d1e-1ec1-4375-8e33-15e2a50d4498)

В таком случае просто нажимаем "continue to site". Дальше всё достаточно дружелюбно, у нас спрашивают логин и пароль:

![image](https://github.com/user-attachments/assets/a2f6a646-97a9-40d8-b18c-d7dd41f29fe1)

Вводим тут логин и пароль, которые нам только что выдали зелёными буквами (*F57I0ix8d8* и *aPXvxNLx8q* в текущем примере). И да, тут можно выбрать русский язык, я буду дальше писать инструкцию для интерфейса на русском, но имейте в виду - перевод местами кривоват.

Теперь мы наконец-то в красивом интерфейсе, откуда можно управлять собственным VPN сервером:

![image](https://github.com/user-attachments/assets/7d646b1c-c6db-4cad-880d-10962db30b2c)

Предупреждение красным сверху можете проигнорировать и закрыть.

Выберите слева "подключения":

![image](https://github.com/user-attachments/assets/504e68a3-70a0-420d-ab23-f4e8bd66d853)

Здесь можно легко создавать VPN сервера самых разных протоколов и с самыми разными настройками. Давайте сделаем VPN сервер на базе **vless**+**reality**, что сейчас практически не отслеживается и не блокируется.

Нажмите кнопку "добавить подключение", появится диалог создания VPN сервера, заполняем поля так:

* "Включить": включить :)
* "Примечание": любое название
* "Протокол": **vless**
* "Порт": **443**
* "Безопасность": **reality**
* "uTLS": **chrome**

![image](https://github.com/user-attachments/assets/07fb61dc-6990-4d2f-afb2-cb4b585775bd)

Разверните блок "клиент", там в качестве "Email" можно указать любое имя клиента (логин), а в поле "Flow" надо выбрать **xtls-rprx-vision** (если поля "Flow" нет, значит вы не выбрали **reality** в поле "безопасность"!)

![image](https://github.com/user-attachments/assets/50bda000-44f6-4678-b649-979fcdfa615b)

Ниже есть поля Dest и SNI, по умолчанию там yahoo.com:

![image](https://github.com/user-attachments/assets/5545b080-c80f-4092-b280-dbe2e9ecd518)

Это сайт, под который будем маскироваться наш сервер. В принципе можно так и оставить, но лучше поменять на какой-то альтернативный. Важно, чтобы он не был заблокирован в вашей стране. Например, можно сделать так:
* Dest: www.microsoft.com:443 и SNI: www.microsoft.com
* Dest: dl.google.com:443 и SNI: dl.google.com

![image](https://github.com/user-attachments/assets/c44986aa-2449-4d83-b37a-61be656b6025)

Под полями "Приватный ключ" и "Публичный ключ" нажимаем кнопку "Get New Cert", это заполнит поля автоматически.

![image](https://github.com/user-attachments/assets/ba24a034-507a-4d01-810a-67185a93b636)

"Sniffing" выключаем:

![image](https://github.com/user-attachments/assets/39304ee8-1c36-42eb-9a40-7e2d0bfea7e9)

Теперь нажимаем кнопку "Создать" и...

![image](https://github.com/user-attachments/assets/5142f328-1200-4309-a988-83901ea19692)

Всё, наш VPN сервер создан и работает. Разверните его, нажав на "плюсик" слева, появится список клиентов:

![image](https://github.com/user-attachments/assets/9ab73058-db87-4ce7-9b4b-214a023a97ac)

Для подключения клиента нужен ключ-URL. Посмотреть его можно, нажав на ⓘ:

![image](https://github.com/user-attachments/assets/aa28c4c4-8c2e-46e3-8922-25cbf3596b23)

Вот он, внизу. Можно его скопировать. В нашем примере он выглядит вот так:

```
vless://b679b1a1-79bf-489f-a0aa-4d2c5b127312@135.125.201.170:443?type=tcp&security=reality&pbk=69fZdFdwUs17syAyfaxCILrK2T_C-BovX5Sd4rLsL0g&fp=chrome&sni=www.microsoft.com&sid=3ac4&spx=%2F&flow=xtls-rprx-vision#%D0%BC%D0%BE%D0%B9%20%D0%BA%D1%80%D1%83%D1%82%D0%BE%D0%B9%20vpn-mama
```

Всё, теперь для подключения к нешему собственному VPN серверу нужна только эта строка (и клиент).

## Использование VPN клиента

Нужен любой **vless VPN клиент**. Их существует много самых разных под самые разные платформы: компьютеры, смартфоны, телевизоры и пр. Для Android я обычно использую [v2RayTun](https://play.google.com/store/apps/details?id=com.v2raytun.android) просто по той причине, что он отлично работает на телевизорах. Настройка там прямолинейнее некуда - копируем ключ в буфер обмена (на телевизор строку можно копировать через приложение [Android TV на смартфоне](https://support.google.com/androidtv/answer/6122465?hl=ru)), нажимаем "импортировать ключ из буфера обмена" и подключаемся. При желании в настройках можно выбрать, чтобы через VPN работали только отдельные приложения, например YouTube. Для Windows можно использовать [NekoBox](https://github.com/MatsuriDayo/nekoray), в котором создается локальная виртуальная Socks прокси, которую можно указать в настройках браузерах.

За состоянием работы можно сделать всё в той же админке:

![image](https://github.com/user-attachments/assets/17ee7b84-c148-43a4-9e40-ca3fe41ac941)

В случае с другими платформами и VPN клиентами всё максимально аналогично и прямолинейно.

Теперь вы можете использовать свой личный VPN сервер, ура!

## Добавление пользователей

Если вы хотите поделиться вашим VPN сервером с кем-то ещё, то лучше создать для этого человека отдельную учётку, для этого в строке с вашим VPN-сервером нажимаем на три точки и выбираем "добавить пользователя":

![image](https://github.com/user-attachments/assets/c989d98b-4ae3-4292-9f78-5cc26e78a9cf)

Тут в качестве "Email" указываем имя пользователя, а в качестве "Flow" важно выбрать **xtls-rprx-vision**). После добавления он должен будет отобразиться в списке:

![image](https://github.com/user-attachments/assets/48db0839-f17e-4b20-9164-e7d7007b8f19)

## Проблемы
Если вы все настроили верно, но подключиться все равно не получается, то попробуйте удалить в настройках соединения порт 443, очистить поле, сохранить, включить и выключить соединение.

# Заключение

Если вы вдруг хотите внести в эту инструкцию какие-то уточнения/правки, то можете смело открывать pull-request. В [issue](https://github.com/ClusterM/vpn-how-to/issues) можно задать вопрос или сделать какое-либо предложение.
