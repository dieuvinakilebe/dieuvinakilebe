# Лабораторная работа 3

### Цель работы
Настроить, чтобы после пуша в ваш репозиторий автоматически собирался Docker-образ, а результат его сборки сохранялся куда-нибудь.  
Например, если результат — текстовый файл, он должен автоматически сохраниться на локальную машину, в ваш репозиторий или на ваш сервер.

### Создание Dockerfile

Создам Dockerfile, из которого будет собираться образ.  
Например, пусть он выведет корову, которая сообщит случайное предсказание.

```dockerfile
FROM ubuntu:18.04
RUN apt-get update && apt-get install -y fortune cowsay

CMD ["sh", "-c", "fortune | cowsay > /lab3/message.txt"]
```

Размещу его в папке лабораторной работы. Там его можно будет найти, открыть и посмотреть — в нём будет написано то же самое.

### Создание GitHub Actions

Для автоматической сборки образа настрою GitHub Actions для моего репозитория.  
Для этого в корне репозитория я создала файл `.github/workflows/docker.yml`.

```yml
name: Docker Build

on:
  push:
    branches:
      - main

jobs:
  docker:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Repository
      uses: actions/checkout@v2

    - name: Build Docker Image
      run: docker build -t message-image .

    - name: Save Docker Image Result
      run: |
        docker save message-image -o result.tar
        mkdir -p github_workspace/artifacts
        mv result.tar github_workspace/artifacts
      if: success()

    - name: Upload Result
      uses: actions/upload-artifact@v3
      with:
        name: result
        path: github_workspace/artifacts

```

В этом файле я указала, что при пуше в ветку `main` будет происходить сборка образа и его сохранение.

### Проверка

После пуша в репозиторий на вкладке **Actions** можно увидеть, что сборка прошла успешно.

![Сборка](/images/1_l3.png)
![Сборка](/images/2_l3.png)

На вкладке **Artifacts** можно увидеть файлы, которые были сохранены.

![Артефакты](/images/3_l3.png)

### Выполнение сборки

Так как я использовала GitHub Actions, сборка происходит на удалённом сервере, а не на локальной машине.  
Можно настроить свою локальную машину для сборки образов, но я этого делать не стала, так как GitHub предоставляет бесплатный сервер для сборки образов.  
Подробнее [тут](https://docs.github.com/ru/actions/learn-github-actions/understanding-github-actions).

### Выводы

В данной работе я научилась работать с GitHub Actions, настроила автоматическую сборку Docker-образа и его сохранение в файл.