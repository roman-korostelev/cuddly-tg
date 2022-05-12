# cuddly-tg
На основе консольного Telegram-клиента [telegram-cli](https://github.com/vysheng/tg) нами был разработан свой клиент со следующим функционалом: просмотр секретного ключа аккаунта и ключа секретного чата.
### Установка
Сразу нужно клонировать репозиторий

     git clone --recursive https://github.com/roman-korostelev/cuddly-tg.git && cd tg


Далее нужно установить все необходимые библиотеки 

    sudo apt-get install libreadline-dev libconfig-dev libssl-dev lua5.2 liblua5.2-dev libevent-dev libjansson-dev libpython3-dev

Потом запускаем конфигурацию и собираем проект

    ./configure && make
    
Для запуска выполняем следующую команду

    ./bin/telegram-cli -k tg-server.pub
    
### Секретный ключ аккаунта
Для просмотра секретного ключа аккаунта в файле `main.c` было добавлено следующее
```C
char con[1000];
FILE* f = fopen("tg-server.pub", "r");
if (!f){
return 1;
}
while (fgets(con,1000, f)!=NULL)
{
  printf("%s",con); 
}
fclose(f);

```
В файл `tg-server.pub` после входа в аккаунт записывается секретный ключ для входа, который мы благополучно считываем и показываем при запуске приложения.

### Ключ секретного чата
В оригинальном клиенте уже была функция `visualize_key`, которая визуализировала секретный ключ аккаунта, так что оставалось её только доработать. Для этого в файле `interface.c` в реализации функции было добавлено следующее

```C
static unsigned char buf[16]; // Тут будет храниться ключ
memset (buf, 0, sizeof (buf));// Выделяем память для него
tgl_do_visualize_key (TLS, args[0].peer_id, buf);// Записываем ключ в массив 
mprint_start (ev);
  
// Поэлементно выводим в шестнадцатиричном виде ключ
for (int i = 0; i < 16; i++)
{
    mprintf(ev, "%x", buf[i]);
}
mprintf(ev, "\n");
```
