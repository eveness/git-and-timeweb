# Работа с удаленным репозиторием на виртуальном хостинге Timeweb

Так как на тарифах виртуального хостинга Timeweb нет возможности добавить иной SSH-доступ, кроме основного, то работа в пределах такого тарифа возможна только под одним юзером.

1) Для начала необходимо включить SSH-доступ, для чего требуется привязка телефона с подтверждением SMS-кодом.

2) Заходим на сервер, где `<user>` соответственно логин, а `<server>` хост, который можно узнать в панели управления:
```
ssh <user>@<server>.timeweb.ru
```

3) Заходим в папку проекта, для примера `my-site`, и инициализируем там репозиторий:
```
cd my-site
git init
```

4) Добавляем в конфиг свою почту и имя пользователя (если до этого не было):
```
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```
Посмотреть настройки можно командой:
```
git config --list
```

5) Добавляем какой-нибудь файл, например `.gitignore`, коммитим:
```
git add .
git commit -m "init"
```

6) Создаем в корне папку `git` и переходим в нее:
```
cd ~
mkdir git
cd git
```

7) Тут инициализируем bare-репозиторий:
```
git clone --bare ../my-site my-site.git
```

8) Возвращаемся в папку проекта и добавляем туда:
```
cd ~/my-site
git remote add shared ../git/my-site.git

```

9) Добавляем хук `post-update` в bare-репозиторий:
```
cd ~/git/my-site.git/hooks
```
С содержимым:
```
#!/bin/sh

cd /home/u/user/my-site || exit
unset GIT_DIR
git pull shared master

exec git-update-server-info
```
Где `/u/user/` - это первая буква логина и сам логин. Даем права на запуск:
```
chmod +x post-update
```

10) Добавляем хук `post-commit` в репозиторий проекта:
```
cd ~/my-site/.git/hooks
```
С содержимым:
```
#!/bin/sh
git push shared
```
Даем права на запуск:
```
chmod +x post-commit
```

11) Возвращаемся в папку проекта:
```
cd ~/my-site
```
И пушимся:
```
git push --set-upstream shared master
```

12) Локально добавлеям удаленный репозиторий и обновляемся:
```
git remote add origin ssh://<user>@<server>.timeweb.ru/home/<u>/<user>/git/my-site.git
git pull origin master
```

<i>Примечание:<br>
в случае работы с IDE JetBrains PhpStorm/WebStorm достаточно:<br>
а) в стартовом окне выбрать Checkout from Version Control -> Git
<br>
б) в произвольном открытом проекте в верхнем меню выбрать VCS -> Checkout from Version Control -> Git
<br>
и в поле Git Repository URL ввести:<br></i>
```
ssh://<user>@<server>.timeweb.ru/home/<u>/<user>/git/my-site.git
```
<i>После запуска проекта пулить сразу уже не требуется.</i><br><br>

Работаем как и с обычным репозиторием. Конец, успех.

