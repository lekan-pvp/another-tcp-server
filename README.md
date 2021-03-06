# another-tcp-server
Эта версия имеет гораздо лучшую структуру кода и во многих отношениях улучшает наш первый пример TCP-сервера, 
используя всего около 80 строк кода!

### Какие улучшения?
* Адрес и порт сервера не запрограммированы жестко в программе, а задаются в командной строке и считываются через *flag* пакет. 
Обратите внимание на использование `flag.NArg()` для подачи сигнала, если не указаны 2 ожидаемых аргумента:

        if flag.NArg() != 2 {
            panic("usage: host port")
        }

Затем аргументы форматируются в строку с помощью функции `fmt.Sprintf`:

    hostAndPort := fmt.Sprintf("%s:%s", flag.Arg(0), flag.Arg(1))
  
* Адрес и порт сервера контролируются в функции `initServer` через `net.ResolveTCPAddr`, и эта функция возвращает `*net.TCPListener`.
* Для каждого подключения функция `connectionHandler` запускается как горутина. Это начинается с отображения адреса клиента с 
расширением `conn.RemoteAddr()`.
* Пишет рекламное Go-сообщение клиенту с помощью функциии `conn.Write`.
* Она читает от клиента кусками по 25 байт и распечатывает их один за другим; в случае ошибки при чтении бесконечный цикл чтения 
покидает предложение переключателя по умолчанию, и это клиентское соединение закрывается. Если ОС выдает `EAGAIN` ошибку, чтение повторяется.
* Вся проверка ошибок реорганизована в отдельную функцию `checkError`, которая выдает панику с контекстным сообщением об ошибке 
в случае возникновения ошибки.

Запустите эту серверную программу в командной строке с помощью: simple_tcp_server localhost 50000 и запустите несколько 
клиентов с помощью [client.go](https://github.com/lekan-pvp/simple-tcp-client-server/blob/main/client/client.go) в отдельных командных окнах. Типичный вывод сервера из двух клиентских подключений следует, где мы видим, что каждый клиент имеет свой собственный адрес.
