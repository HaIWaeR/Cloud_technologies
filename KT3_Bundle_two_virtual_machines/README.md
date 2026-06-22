# Установка Ansible на управляющий узел

Обновление пакетов
```bash
sudo apt update
```

Установка Ansible
```bash
sudo apt install ansible -y
```

Проверка версии:
```bash
ansible --version
```

# Генерация SSH-ключа на управляющем узле

Создание ключа
```bash
ssh-keygen -t rsa -b 4096 -C "ansible-key"
```

Копирование SSH-ключа
```
ssh-copy-id marsgg233@100.112.211.11
```

# Проверка подключения без пароля

Подключаемся
```bash
ssh marsgg233@100.112.211.11
```

Если подключились без пароля
```bash
exit
```

# Настройка sudo без пароля

На второй вируталке 
```
sudo visudo 
```

ищем `%sudo   ALL=(ALL:ALL) ALL` и меняем на `%sudo   ALL=(ALL:ALL) NOPASSWD: ALL`

Проверяем 
```bash
sudo -k
sudo whoami
```

Должно вывести `root`

# Создание инвентарного файла

Создаём файл hosts
```bash
nano hosts
```

Редактируем файл
```bash
[servers]
100.112.211.11 ansible_user=marsgg233
```

# Проверка подключения Ansible

Проверяем
```bash
ansible -i hosts servers -m ping
```

Должно показать
```bash
100.112.211.11 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

# Создание playbook

Создаём файл __playbook.yaml__

```bash
nano playbook.yaml
```

Редактируем файл
```bash
- name: Настройка сервера через Ansible
  hosts: servers
  become: true
  tasks:
    - name: Обновить список пакетов
      apt:
        update_cache: true
        cache_valid_time: 3600

    - name: Обновить все пакеты
      apt:
        upgrade: dist

    - name: Установить Git
      apt:
        name: git
        state: present

    - name: Создать пользователя ansible_papkov
      user:
        name: ansible_ВашаФамилия
        state: present
        shell: /bin/bash
        create_home: yes

    - name: Установить PostgreSQL
      apt:
        name: postgresql
        state: present

    - name: Установить Nginx
      apt:
        name: nginx
        state: present

    - name: Создать index.html
      copy:
        dest: /var/www/html/index.html
        content: |
          <html>
            <head><title>Ansible Server</title></head>
            <body>
              <h1>Привет от Ansible!</h1>
              <p>Сервер настроен автоматически</p>
            </body>
          </html>
        owner: www-data
        group: www-data
        mode: '0644'

    - name: Запустить Nginx
      service:
        name: nginx
        state: started
        enabled: yes
```

Запуск playbook 
```bash
ansible-playbook -i hosts playbook.yaml
```

Ждём завершения 5-15 минут.

```bash
PLAY RECAP
100.112.211.11 : ok=8    changed=6    unreachable=0    failed=0
```

# Проверки 
Проверяем Nginx
```bash
systemctl status nginx
```
Проверяем пользователя
```bash
id ansible_papkov
```
Проверяем Git
```bash
git --version
```
Проверяем PostgreSQL
```bash
sudo -u postgres psql -c "SELECT version();"
```
Проверяем index.html
```bash
curl http://localhost
```