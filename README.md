# 8_1

Установил ansible:
```
pashi@pashi-docker:~/GolandProjects/8_1$ ansible --version
ansible [core 2.13.7]
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/pashi/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /home/pashi/.local/lib/python3.8/site-packages/ansible
  ansible collection location = /home/pashi/.ansible/collections:/usr/share/ansible/collections
  executable location = /home/pashi/.local/bin/ansible
  python version = 3.8.10 (default, Nov 14 2022, 12:59:47) [GCC 9.4.0]
  jinja version = 3.1.2
  libyaml = True
pashi@pashi-docker:~/GolandProjects/8_1$ ansible -m ping localhost
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```
## Основная часть
* Попробуйте запустить playbook на окружении из test.yml, зафиксируйте какое значение имеет факт some_fact для указанного хоста при выполнении playbook'a.
Запустил
```
pashi@pashi-docker:~/GolandProjects/8_1$ ansible-playbook -i playbook/inventory/test.yml playbook/site.yml 

PLAY [Print os facts] *****************************************************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************************************************
ok: [localhost]

TASK [Print OS] ***********************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ubuntu"
}

TASK [Print fact] *********************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": 12
}

PLAY RECAP ****************************************************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```
* Найдите файл с переменными (group_vars) в котором задаётся найденное в первом пункте значение и поменяйте его на 'all default fact'.
  
* Воспользуйтесь подготовленным (используется docker) или создайте собственное окружение для проведения дальнейших испытаний.
  
* Проведите запуск playbook на окружении из prod.yml. Зафиксируйте полученные значения some_fact для каждого из managed host.
  
* Добавьте факты в group_vars каждой из групп хостов так, чтобы для some_fact получились следующие значения: для deb - 'deb default fact', для el - 'el default fact'.
  
* Повторите запуск playbook на окружении prod.yml. Убедитесь, что выдаются корректные значения для всех хостов.
  
* При помощи ansible-vault зашифруйте факты в group_vars/deb и group_vars/el с паролем netology.
  
* Запустите playbook на окружении prod.yml. При запуске ansible должен запросить у вас пароль. Убедитесь в работоспособности.
  
* Посмотрите при помощи ansible-doc список плагинов для подключения. Выберите подходящий для работы на control node.
  
* В prod.yml добавьте новую группу хостов с именем local, в ней разместите localhost с необходимым типом подключения.
  
* Запустите playbook на окружении prod.yml. При запуске ansible должен запросить у вас пароль. Убедитесь что факты some_fact для каждого из хостов определены из верных group_vars.
  
* Заполните README.md ответами на вопросы. Сделайте git push в ветку master. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым playbook и заполненным README.md.


## Необязательная часть

* При помощи ansible-vault расшифруйте все зашифрованные файлы с переменными.
    
* Зашифруйте отдельное значение PaSSw0rd для переменной some_fact паролем netology. Добавьте полученное значение в group_vars/all/exmp.yml.
  
* Запустите playbook, убедитесь, что для нужных хостов применился новый fact.
  
* Добавьте новую группу хостов fedora, самостоятельно придумайте для неё переменную. В качестве образа можно использовать этот.
  
* Напишите скрипт на bash: автоматизируйте поднятие необходимых контейнеров, запуск ansible-playbook и остановку контейнеров.
  
* Все изменения должны быть зафиксированы и отправлены в вашей личный репозиторий.
