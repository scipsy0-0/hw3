## Запуск


скопируйте пример настроек
```
cp .env.example .env
```
В `.env` переменная `HOST_PORT` задаёт порт, а `GREETING` приветствие.

#### На 6 баллов

```
docker compose -f compose.level6.yaml up -d --build
```

#### На 8 баллов

```
docker compose up -d --build
docker compose ps
```


## Отчет

на 4 балла:

```
curl -fsS http://127.0.0.1:8080/
{"greeting": "Hello from a container!", "count": 0, "hostname": "261a7c55e495", "storage": "sqlite"}        
```

```
curl -fsS -X POST http://127.0.0.1:8080/increment
{"greeting": "Hello from a container!", "count": 1, "hostname": "261a7c55e495", "storage": "sqlite"}        
```

```
curl -fsS http://127.0.0.1:8080/
{"greeting": "Hello from a container!", "count": 1, "hostname": "261a7c55e495", "storage": "sqlite"}%       
```

логи в level4.log

на 6 баллов:

```
docker compose -f compose.level6.yaml ps
NAME                  IMAGE               COMMAND              SERVICE   CREATED          STATUS                    PORTS
hw_3_template-app-1   hw_3_template-app   "python -u app.py"   app       18 seconds ago   Up 17 seconds (healthy)   127.0.0.1:8080->8000/tcp
```

```
curl -fsS -X POST http://127.0.0.1:8080/increment       
{"greeting": "Hello from Compose!", "count": 1, "hostname": "88c6a718cb4c", "storage": "sqlite"}
```

перезапустим и проверим сохранился ли счетчик

```
docker compose -f compose.level6.yaml down && docker compose -f compose.level6.yaml up -d
```

```
curl -fsS http://127.0.0.1:8080
{"greeting": "Hello from Compose!", "count": 1, "hostname": "f5fc5760f0a5", "storage": "sqlite"}
```

сохранился

на 8 баллов:

```
docker ps
CONTAINER ID   IMAGE               COMMAND                  CREATED          STATUS                    PORTS                      NAMES
f5cd3697a0b1   hw_3_template-app   "python -u app.py"       25 seconds ago   Up 21 seconds (healthy)   127.0.0.1:8080->8000/tcp   hw_3_template-app-1
f8e526390d63   redis:7.4-alpine    "docker-entrypoint.s…"   26 seconds ago   Up 25 seconds (healthy)   6379/tcp                   hw_3_template-redis-1
```


```
curl -fsS -X POST http://127.0.0.1:8080/increment       
{"greeting": "Hello from Compose!", "count": 1, "hostname": "f5cd3697a0b1", "storage": "redis"}
```

```
curl -fsS -X POST http://127.0.0.1:8080/increment
{"greeting": "Hello from Compose!", "count": 2, "hostname": "f5cd3697a0b1", "storage": "redis"}
```

```
docker compose down && docker compose up -d
```

```
docker compose ps                          
NAME                    IMAGE               COMMAND                  SERVICE   CREATED          STATUS                   PORTS
hw_3_template-app-1     hw_3_template-app   "python -u app.py"       app       10 seconds ago   Up 6 seconds (healthy)   127.0.0.1:8080->8000/tcp
hw_3_template-redis-1   redis:7.4-alpine    "docker-entrypoint.s…"   redis     10 seconds ago   Up 9 seconds (healthy)   6379/tcp
```

```
curl -fsS http://127.0.0.1:8080/        
{"greeting": "Hello from Compose!", "count": 2, "hostname": "bf768d1c69ac", "storage": "redis"}
```

```
docker compose stop redis
[+] stop 1/1
 ✔ Container hw_3_template-redis-1 Stopped  
```

```
curl -fsS http://127.0.0.1:8080/healthz 
{"status": "alive"}
```

```
curl -fsS http://127.0.0.1:8080/readyz 
curl: (22) The requested URL returned error: 503
```

```
curl -fsS -X POST http://127.0.0.1:8080/increment
curl: (22) The requested URL returned error: 503
```

```
docker compose start redis
[+] start 1/1
 ✔ Container hw_3_template-redis-1 Started   
```

```
docker compose ps 
NAME                    IMAGE               COMMAND                  SERVICE   CREATED         STATUS                    PORTS
hw_3_template-app-1     hw_3_template-app   "python -u app.py"       app       7 minutes ago   Up 7 minutes (healthy)    127.0.0.1:8080->8000/tcp
hw_3_template-redis-1   redis:7.4-alpine    "docker-entrypoint.s…"   redis     7 minutes ago   Up 13 seconds (healthy)   6379/tcp
```

```
curl -fsS http://127.0.0.1:8080/readyz           
{"status": "ready", "storage": "redis"}
```

```
curl -fsS http://127.0.0.1:8080/                 
{"greeting": "Hello from Compose!", "count": 2, "hostname": "bf768d1c69ac", "storage": "redis"}
```

```
curl -fsS -X POST http://127.0.0.1:8080/increment
{"greeting": "Hello from Compose!", "count": 3, "hostname": "bf768d1c69ac", "storage": "redis"}
```

