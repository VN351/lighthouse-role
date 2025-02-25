# Ansible role для установки Lighthouse

## Содержание
- [Обзор](#обзор)
- [Требования](#требования)
- [Переменные](#переменные)
- [Использование](#использование)
- [Логика работы](#логика-работы-роли)

## Обзор
Данная роль объединяет в себе всю необходимую логику для настройки и развертывания сервиса Lighthouse, а также конфигурации веб-сервера Nginx. 

## Требования

- **Ansible:** Версия 2.9 или выше.
- **Управляемые хосты:**
  - Операционная система: Совместима с дистрибутивами на основе RPM (например, CentOS, RHEL, Fedora).
  - Менеджер пакетов: Должен быть доступен yum.
- **Сетевой доступ:**
  - Возможность скачивания пакетов с внешних URL.
  - SSH-доступ к управляемым хостам.

## Переменные

# defaults/main.yml
- vlighthouse_git – Репозиторий Github проекта lighthouse. 
  *Пример:* "https://github.com/VKCOM/lighthouse.git"
# vars/main.yml
- lighthouse_dir – Дирректория установки lighthouse.  
  *Пример:* "/etc/nginx/lighthouse"


## Использование

1. **Установка роли:**  
   Склонируйте или добавьте роль lighthouse-role в ваш проект Ansible.

2. **Интеграция в playbook:**  
    Добавьте роль в ваш основной playbook:
    ```yml
    - name: Install Nginx and Lighthouse
      hosts: lighthouse
      become: true
      roles:
        - lighthouse
    ```

## Логика работы роли

1. **Установка пакетов и зависимостей:**  
   При выполнении роли производится установка необходимых пакетов (например, nginx), если они ещё не установлены.

2. **Клонирование репозитория Lighthouse:**  
   Роль использует переменную lighthouse_git для указания URL репозитория и клонирует его в заданный каталог, определённый переменной lighthouse_dir.

3. **Копирование и генерация конфигурационных файлов:**  
   - **Nginx:** Используется шаблон nginx.conf.j2, который содержит настройки основного конфигурационного файла. 
   - **Lighthouse:** Шаблон lighthouse.conf.j2 генерирует конфигурационный файл с параметрами для сервиса Lighthouse.

4. **Обработка изменений и перезагрузка сервисов:**  
   Все задачи, связанные с копированием шаблонов или установкой пакетов, уведомляют обработчик, который находится в файле handlers/main.yml. Этот обработчик перезагружает сервис Nginx, что гарантирует применение новых настроек.

5. **Разделение переменных для гибкости конфигурации:**  
   Переменные вынесены в разные файлы:
   - defaults/main.yml позволяет задавать дефолтное значение для клонируемого репозитория.
   - vars/main.yml отвечает за путь установки или хранения данных сервиса Lighthouse.
