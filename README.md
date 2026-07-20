# Configuration Management & Kubernetes Bare-Metal Cluster Deploy

Репозиторий содержит автоматизированные сценарии управления конфигурацией серверов и развертывания базового системного ПО с помощью Ansible.

## Состав сценариев (Playbooks)
* [deploy_postgres.yml](https://github.com/EvgenKli/my-ansible-automation/blob/main/deploy_postgres.yml) — Идемпотентный сценарий развертывания СУБД PostgreSQL 16. Автоматизирует установку, конфигурирует системные лимиты, файлы сетевой безопасности (`pg_hba.conf`), инициализирует пользователя и базу данных для бэкенда.
* [deploy_monitoring.yml](https://github.com/EvgenKli/my-ansible-automation/blob/main/deploy_monitoring.yml) — Сценарий автоматического развертывания стека SRE-мониторинга (Prometheus Server + Grafana) в Docker-контейнерах на выделенном сервере баз данных.
* [install_k8s_tools.yml](https://github.com/EvgenKli/my-ansible-automation/blob/main/install_k8s_tools.yml) — Сценарий подготовки нод кластера Kubernetes. Отключает Swap, активирует сетевые фильтры ядра Linux (`br_netfilter`), разворачивает контейнерный движок Containerd, устанавливает менеджер пакетов Helm и фиксирует версии утилит через dpkg hold.

## Сетевые особенности и автоматизация
* **Jump Host (Bastion):** Настроена маршрутизация Ansible через «прыжковый» сервер (Мастер-ноду `5.35.28.230`) с помощью параметров ProxyCommand в файле инвентаря [hosts](https://github.com/EvgenKli/my-ansible-automation/blob/main/hosts). Это позволяет конфигурировать Воркер-ноду, скрытую в приватной сети.
* **Доверенные ключи:** Сценарий автоматически связывает ключи операционных систем Мастера и Воркера для их беспрепятственного автономного общения внутри кластера.

## Связанные компоненты экосистемы
* Развертывание облачной инфраструктуры (IaC): [my-infrastructure-iac-project](https://github.com/EvgenKli/my-infrastructure-iac-project)
* Исходный код микросервиса и CI/CD конвейер: [my-spring-backend](https://github.com/EvgenKli/my-spring-backend)

## Инструкция по запуску
1. Актуализируйте IP-адреса серверов в файле инвентаря `hosts`.
2. Запустите быструю проверку связи (ad-hoc ping):
```bash
ansible all -i hosts -m ping
```
3. Запустите требуемый плейбук автоматизации:
```bash
ansible-playbook -i hosts <имя_файла>.yml
```
