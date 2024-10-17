# Задание 1: Анализ и планирование
## Подзадание 1.1: Анализ и планирование

### Код
Монолит представляет из себя MVC
c несколькими url
не включает в себя ни авторизацию ни регистрацию
имеет две таблицы одна из которых
хранит конфигурацию HeatingSystem
хранит показания датчиков TemperatureSensor
работает только с одной системой

### DDD
Область компании мониторинг и управление датчиками (Домен)
Часть системы отвечающая за сбор данных от датчиков (Поддомен)
Часть системы отвечающая за управление отоплениями (Поддомен)
Датчик (контекст)
Управление отоплением/температурой (контекст)

### Архитектура

Приложение монолит упакованный в докер

Написан helm чарт у которого есть в зависимостях база данных postgresql
  pod1 монолит в котором развернут smart-home-monolith
  pod2 база данных postgresql

### Диаграмма

[Диаграмма](./1.1.puml)

### План перехода
На текущем  уровне приложения для перехода по сути ничего не нужно
скорее нужно доработать приложение
Написать сервисы
 управление отоплением/темпретурой
 сбор данных с датчиков
Наисать Dockerfile
Доработать helm chart для запуска микросервисов

## Подзадание 1.2: Архитектура микросервисов

### Диаграмма
[Диаграмма контейнеров](./1.2.containers.puml)
[Диаграмма Компонентов](./1.2.components.puml)
[Диаграмма Кода](./1.2.code.puml)

В диаграмме кода описанна строение одного микросервиса
eventBus используется для передачи событий об изменениях для их отката при ошибке в каком либо из микросервисов

## Подзадание 1.3: ER-диаграмма
Диаграмма основных связей
[Диаграмма Кода](./1.3.er-diagram.puml)

## Подзадание 1.4: Создание и документирование API
Я обычно использую Code First описание документации  (использую NestJs swagger)
И в ручном описании возможно будут пробелы и скорее всего потребуется доработка
[OpenApi Документация](./api.yaml)

Описание взаимодейтсвия AsyncApi
Документирование апи сложная задача без написания кода
и в текущем спринте обычно это был последний шаг после этапа разработки
[AsyncApi Документация](./asyncapi.yaml)
# Базовая настройка

## Запуск minikube

[Инструкция по установке](https://minikube.sigs.k8s.io/docs/start/)

```bash
minikube start
```


## Добавление токена авторизации GitHub

[Получение токена](https://github.com/settings/tokens/new)

```bash
kubectl create secret docker-registry ghcr --docker-server=https://ghcr.io --docker-username=<github_username> --docker-password=<github_token> -n default
```


## Установка API GW kusk

[Install Kusk CLI](https://docs.kusk.io/getting-started/install-kusk-cli)

```bash
kusk cluster install
```


## Настройка terraform

[Установите Terraform](https://yandex.cloud/ru/docs/tutorials/infrastructure-management/terraform-quickstart#install-terraform)


Создайте файл ~/.terraformrc

```hcl
provider_installation {
  network_mirror {
    url = "https://terraform-mirror.yandexcloud.net/"
    include = ["registry.terraform.io/*/*"]
  }
  direct {
    exclude = ["registry.terraform.io/*/*"]
  }
}
```

## Применяем terraform конфигурацию

```bash
cd terraform
terraform apply
```

## Настройка API GW

```bash
kusk deploy -i api.yaml
```

## Проверяем работоспособность

```bash
kubectl port-forward svc/kusk-gateway-envoy-fleet -n kusk-system 8080:80
curl localhost:8080/hello
```


## Delete minikube

```bash
minikube delete
```
