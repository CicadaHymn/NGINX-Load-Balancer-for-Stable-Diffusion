# NGINX Load Balancer для Stable Diffusion

Этот репозиторий предоставляет конфигурацию NGINX для балансировки нагрузки между несколькими экземплярами Stable Diffusion, обеспечивая отказоустойчивость и равномерное распределение запросов.

## Требования

- Установленный NGINX
- Запущенные экземпляры Stable Diffusion

## Установка и настройка

### 1. Настройка NGINX

1. Скопируйте конфигурационный файл из этого репозитория в `/etc/nginx/nginx.conf`.
2. В блоке `upstream stable_diffusion_servers` замените адрес сервера и порт сервера `11.11.11:7680` на IP-адрес вашего сервера или домен (например, `MyDomain.ngrok-free.dev`). Пример конфигурации:

    upstream stable_diffusion_servers {
        server MyDomain.ngrok-free.dev max_fails=1 fail_timeout=6s weight=1;
        server 342.268.3.31:7861 max_fails=1 fail_timeout=6s weight=1;
    }

### 2. Запуск Stable Diffusion

Для корректной работы Stable Diffusion с NGINX необходимо включить прослушивание внешних запросов и включать SD на нужном вам порте:

 При запуске Stable Diffusion через Python добавьте параметр `--listen -port ####`. Пример конфигурации:

    python stable_diffusion.py --listen --port 7680

 Если вы используете `webui-user.bat` для запуска, добавьте в файл параметр:

    set COMMANDLINE_ARGS=--listen --port 7681

Для второго экземпляра используйте другой порт, например, `7861`.

### 3. Локальный хостинг
Для успешного локального хостинга NGINX и SD вы должны выполнить предыдущие шаги, но в upstream `stable_diffusion_servers` вы вводите свой IP,а не привычный локальный адрес "127.0.0.1:7680"
То есть блок апстрим для локального хостинга будет выглядеть так [IP]:[PORT]
Пример конфигурации:
    upstream stable_diffusion_servers {
        server 123.321.2.23:7860 max_fails=1 fail_timeout=6s weight=1;
        server 123.321.2.23:7861 max_fails=1 fail_timeout=6s weight=1;
    }

### Примечания
Проверьте NGINX файлы, помимо nginx.conf, которые могут перехватывать запрос, и в итоге балансировщик не будет работать, для корректной работы вам надо будет определить какой файл перехватывает запрос и отключить файл либо устранить конфликт, всё зависит от ситуации.
