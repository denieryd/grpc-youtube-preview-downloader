# GRPC Youtube previews (thumbnails) downloader

Данное приложение позволяет скачивать привью видеороликов с видеохостинга [Youtube](https://youtube.com).

Данное приложение использует технологию GRPC для коммуникации клиента и сервера.
Для тестирования использовались grpc клиенты - evans, собственный клиент.

В качестве БД для "кэширования" используется postgresql.

### Запуск (GRPC Server)
1. Клоним репозиторий
2. Прописываем путь до файла с конфигом в переменную окружения `CONFIG_PATH`
3. Поднимаем постгрес (например, в докер контейнере)
4. Проставляем необходимые переменные в конфиг
5. Билдим main.go и запускаем его.
6. Профит


```bash
git clone https://github.com/denieryd/grpc-youtube-preview-downloader
cd grpc-youtube-preview-downloader
export CONFIG_PATH="configs/config.example.json"
docker run --name thumbnail -e POSTGRES_PASSWORD=pass -p "5432:5432" -d postgres
# here we edit ./configs/config.example.json file 
# here we build ./cmd/main.go and run it
go build ./cmd/main.go
./main
```

### Тестирование

Для тестирования нам необходим grpc клиент, я использовал evans.
Допустим, вы установили клиент evans, далее протестировать можно следующим образом:

```bash
evans ./api/proto/getpreview.proto -p 8080
call GetPreview
# here we input video url to download thumbnail
https://www.youtube.com/watch?v=s-7pyIxz8Qg&ab_channel=MovieclipsClassicTrailers
# here we get response, base64 encoded jpg video preview
{
  "image": "/9j/4AAQSkZJRgABAQAAAQABAA....."
}
```

### GRPC Client 

Был реализован собственный GRPC клиент.
Клиент позволяет указать ссылку на видео и скачать его превью в папку `thumbnails_client`.
Он запускается как консольная утилита и позволяет скачать превью видео, указав на него ссылку с ютуба.
Также утилита имеет флаг -async, который позволяет указать несколько урлов и скачать превью параллельно.

```bash
go build ./cmd/grpcclient.go
./grpcclient -async true url1 url2 url3 ...

OR

./grpcclient url1
```

### Что я могу еще сделать, но немножко не успел :)

1. Нормальные тесты (сделал чисто для вида парочку)
2. Завернуть все в докер
3. Добавить CI/CD, который запускает тесты, форматирует код, шиппит код на сервер с помощью джобы и т.д.
