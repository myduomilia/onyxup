## onyxup
Микро фреймворк для разработки веб приложений

[![Build Status](https://travis-ci.org/myduomilia/onyxup.svg?branch=master)](https://travis-ci.org/myduomilia/onyxup)

## Используемы сторонние библиотеки
[Plog - portable, simple and extensible C++ logging library](https://github.com/SergiusTheBest/plog/tree/master/include/plog)

[PicoHTTPParser - tiny HTTP parser](https://github.com/h2o/picohttpparser)

[Gzip C++ lib for gzip compression and decompression](https://github.com/mapbox/gzip-hpp)

[JSON for modern C++](https://github.com/nlohmann/json)

## Зависимости
```bash
sudo apt-get install zlib1g-dev
sudo apt-get install libcurl3-dev (only for tests)

```


## Установка стабильной версии:
```bash
wget -O onyxup-1.0.0.tar.gz  https://codeload.github.com/myduomilia/onyxup/tar.gz/1.0.0
tar -zxvf onyxup-1.0.0.tar.gz 
cd onyxup-1.0.0
mkdir build
cd build/
cmake ..
make
sudo make install
```

## Установка последней версии:
```bash
mkdir build
cd build/
cmake ..
make
sudo make install
```

## Установка и запуск тестов:
```bash
mkdir build
cd build/
cmake -DBUILD_TESTING=on  ..
make
make test
sudo make install
```

## Установка в режиме debug:
```bash
mkdir build
cd build/
cmake -DBUILD_DEBUG_MODE=on  ..
make
make test
sudo make install
```

## Пример использования:

```C++
#include <onyxup/server/server.h>
#include <onyxup/response/response-json.h>
#include <onyxup/response/response-html.h>
#include <onyxup/response/response-file.h>
#include <string>

onyxup::ResponseBase html(onyxup::PtrCRequest request);

onyxup::ResponseBase json(onyxup::PtrCRequest request);

onyxup::ResponseBase file(onyxup::PtrCRequest request);

onyxup::ResponseBase params(onyxup::PtrCRequest request);

onyxup::ResponseBase multipart_form(onyxup::PtrCRequest request);

int main() {

    onyxup::HttpServer server(7000, 16);

    /*
     * Routes
     */
    server.addRoute("GET", "^/html$", html, onyxup::EnumTaskType ::LOCAL_TASK);
    server.addRoute("GET", "^/json", json, onyxup::EnumTaskType ::LOCAL_TASK);
    server.addRoute("GET", "^/file$", file,onyxup::EnumTaskType ::LOCAL_TASK);
    server.addRoute("GET", "^/params.+$", params,onyxup::EnumTaskType ::LOCAL_TASK);
    server.addRoute("POST", "^/multipart-form", multipart_form, onyxup::EnumTaskType ::LOCAL_TASK);
    /*
     * Route для статических файлов
     */
    server.addRoute("GET", "^/static/.+$", onyxup::HttpServer::default_callback_static_resources, onyxup::EnumTaskType ::STATIC_RESOURCES_TASK);

    /*
     * Путь до расположения статических файлов
     */
    std::string path_to_static_files = "/project/";
    onyxup::HttpServer::setPathToStaticResources(path_to_static_files);

    /*
    *   Максимальное время выполнения запроса на сервер (по умолчанию 60 с)
    */
    onyxup::HttpServer::setTimeLimitRequestSeconds(30);

    /*
    *   Масимальное количество задач в очереди на сервере (по умолчанию 100)
    */
    onyxup::HttpServer::setLimitLocalTasks(100);

    /*
     * Включаем сбор статистики и указываем url доступа (по умолчанию "^/onyxup-status-page$")
     */
    onyxup::HttpServer::setStatisticsEnable(true);
    onyxup::HttpServer::setStatisticsUrl("^/statistics$");

    server.run();
}

onyxup::ResponseBase html(onyxup::PtrCRequest request) {
    return onyxup::ResponseHtml("index.html");
}

onyxup::ResponseBase json(onyxup::PtrCRequest request) {
    return onyxup::ResponseJson(R"({"status":"success"})", true);
}

onyxup::ResponseBase file(onyxup::PtrCRequest request) {
    std::string path_to_file;
    return onyxup::ResponseFile(path_to_file);
}

onyxup::ResponseBase params(onyxup::PtrCRequest request) {
    /*
     * Запрос /params?param-first=1&param-second=data
     */
    auto & request_params = request->getParams();
    if(request_params.find("param-first") != request_params.end()){
        int param1 =  std::stoi(request_params.at("param-first"), nullptr);
        std::cout << "Param first = " << param1 << std::endl;
    }
    if(request_params.find("param-second") != request_params.end()){
        std::string param1 =  request_params.at("param-second");
        std::cout << "Param second = " << param1 << std::endl;
    }
    return onyxup::ResponseJson(R"({"status":"success"})");
}

onyxup::ResponseBase multipart_form(onyxup::PtrCRequest request) {
    auto form_fields = onyxup::HttpServer::multipartFormData(request);
    std::vector<char> data = form_fields["image"].getData();
    std::string filename = "./" + form_fields["image"].getFilename();
    std::ofstream f(filename, std::ios::binary);
    std::copy(data.begin(), data.end(), std::ostream_iterator<char>(f, ""));
    return onyxup::ResponseJson(R"({"status":"success"})", true);
}
```

## Лицензия

MIT License

## История версий

***Version: 1.0.1 (3 Sep 2019)***

<ul>
 <li>Поддержка HTTP Range Requests</li>
 <li>Поддержка сжатия gzip</li>
 <li>Кеширование статических ресурсов</li>
 <li>Поддержка запросов GET, POST, PUT, DELETE, HEAD</li>
 <li>Поддержка многопоточной работы сервера</li>
 <li>Поддержка логирования</li>
 <li>Поддержка запросов application/x-www-form-urlencoded</li>
 <li>Поддержка запросов multipart/form-data</li>
 <li>Ограничение времени запроса на сервере и поиск зависших сокетов</li>
 <li>Поддержка Keep-Alive соединений</li>
 <li>Возможность управление настройкой сервера через конфигурационный файл</li>
 <li>Страница статистики сервера</li>
</ul>

