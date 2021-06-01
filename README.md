# Отчёт по lab09

## Tutorial

1. Создаём переменные

_export GITHUB_USERNAME=Dan10022002<br/>
export PACKAGE_MANAGER='sudo apt'<br/>
export GPG_PACKAGE_NAME=gpg_

2. Устанавливаем xclip и необходимые алисы

_$PACKAGE_MANAGER install xclip<br/>
alias gsed=sed<br/>
alias pbcopy='xclip -selection clipboard'<br/>
alias pbpaste='xclip -selection clipboard -o'_

![xclip](https://github.com/Dan10022002/lab09/blob/master/xclip.png)

3. Переходим в рабочую директорию и активируем скрипт

_cd ${GITHUB_USERNAME}/workspace<br/>
pushd .<br/>
source scripts/activate<br/>
go get github.com/aktau/github-release_

![go](https://github.com/Dan10022002/lab09/blob/master/go.png)

4. Клонируем репозиторий и изменияем URL

_git clone https://github.com/${GITHUB_USERNAME}/lab08 projects/lab09<br/>
cd projects/lab09<br/>
git remote remove origin<br/>
git remote add origin https://github.com/${GITHUB_USERNAME}/lab09_

5. Заменяем lab08 на lab09

_gsed -i 's/lab08/lab09/g' README.md_

6. Устанавливаем gpg, выводим уже существующие ключи и задаём формат отображения идентификаторов ключей, генерируем ключ и снова выводим, задаём новые переменные GPG_KEY_ID и GPG_SEC_KEY_ID, переводим GPG_KEY_ID в ASKII, копируем в буфер обмена и выводим его содержимое, обновляем в конфигурацию git информацию о ключе gpg.

_$PACKAGE_MANAGER install ${GPG_PACKAGE_NAME}<br/>
gpg --list-secret-keys --keyid-format LONG<br/>
gpg --full-generate-key<br/>
gpg --list-secret-keys --keyid-format LONG<br/>
gpg -K ${GITHUB_USERNAME}<br/>
GPG_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep ssb | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')<br/>
GPG_SEC_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep sec | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')<br/>
gpg --armor --export ${GPG_KEY_ID} | pbcopy<br/>
pbpaste<br/>
open https://github.com/settings/keys<br/>
git config user.signingkey ${GPG_SEC_KEY_ID}<br/>
git config gpg.program gpg_

![gpg](https://github.com/Dan10022002/lab09/blob/master/gpg.png)

![key](https://github.com/Dan10022002/lab09/blob/master/key1.png)

7. Проверяем, есть ли доступ к .bash_profile и записываем переменную GPG_TTY со значением $(tty), такую же переменную записываем в файл .profile

_test -r ~/.bash_profile && echo 'export GPG_TTY=$(tty)' >> ~/.bash_profile<br/>
echo 'export GPG_TTY=$(tty)' >> ~/.profile_

8. Запускаем комапилицию проекту и выполняем сборку с созданием пакета

_cmake -H. -B_build -DCPACK_GENERATOR="TGZ"<br/>
cmake --build _build --target package_

![build1](https://github.com/Dan10022002/lab09/blob/master/build1.png)

![build2](https://github.com/Dan10022002/lab09/blob/master/build2.png)

9. Создаём метку и "подписываем" её ключом gpg

_git tag -s v0.1.0.0<br/>
git tag -v v0.1.0.0<br/>
git show v0.1.0.0<br/>
git push origin master --tags_

![tag](https://github.com/Dan10022002/lab09/blob/master/tag.png)

10. Сoздаём релиз нашего пректа на GITHUB

```sh
github-release --version
github-release info -u ${GITHUB_USERNAME} -r lab09
github-release release \
    --user ${GITHUB_USERNAME} \
    --repo lab09 \
    --tag v0.1.0.0 \
    --name "libprint" \
    --description "my first release"
```

