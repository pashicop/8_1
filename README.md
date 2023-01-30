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
1. Попробуйте запустить playbook на окружении из test.yml, зафиксируйте какое значение имеет факт some_fact для указанного хоста при выполнении playbook'a.    
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
2. Найдите файл с переменными (group_vars) в котором задаётся найденное в первом пункте значение и поменяйте его на 'all default fact'.  
Сделал.
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
    "msg": "all default fact"
}

PLAY RECAP ****************************************************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```
3. Воспользуйтесь подготовленным (используется docker) или создайте собственное окружение для проведения дальнейших испытаний.  
Запустил 2 машины в docker
`sudo docker run -d --name=ubuntu -it -p 22 ubuntu`   
`sudo docker run -d --name=centos7 -it -p 22 centos:7`  
в Ubuntu дополнительно установил python3

4. Проведите запуск playbook на окружении из prod.yml. Зафиксируйте полученные значения some_fact для каждого из managed host.  
```
pashi@pashi-docker:~/GolandProjects/8_1$ ansible-playbook -i playbook/inventory/prod.yml playbook/site.yml 

PLAY [Print os facts] *****************************************************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************************************************
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] ***********************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] *********************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "el"
}
ok: [ubuntu] => {
    "msg": "deb"
}

PLAY RECAP ****************************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```  
5. Добавьте факты в group_vars каждой из групп хостов так, чтобы для some_fact получились следующие значения: для deb - 'deb default fact', для el - 'el default fact'.    
Поменял
6. Повторите запуск playbook на окружении prod.yml. Убедитесь, что выдаются корректные значения для всех хостов.  
```
pashi@pashi-docker:~/GolandProjects/8_1$ ansible-playbook -i playbook/inventory/prod.yml playbook/site.yml 

PLAY [Print os facts] *****************************************************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************************************************
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] ***********************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] *********************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP ****************************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```
7. При помощи ansible-vault зашифруйте факты в group_vars/deb и group_vars/el с паролем netology.  
Зашифровал паролем `netology`    
8. Запустите playbook на окружении prod.yml. При запуске ansible должен запросить у вас пароль. Убедитесь в работоспособности.  
  Запустил с ключём --ask-vault-pass
```
pashi@pashi-docker:~/GolandProjects/8_1$ ansible-vault encrypt playbook/group_vars/deb/examp.yml 
New Vault password: 
Confirm New Vault password: 
Encryption successful
pashi@pashi-docker:~/GolandProjects/8_1$ ansible-vault encrypt playbook/group_vars/el/examp.yml 
New Vault password: 
Confirm New Vault password: 
Encryption successful
pashi@pashi-docker:~/GolandProjects/8_1$ ansible-playbook -i playbook/inventory/prod.yml playbook/site.yml 

PLAY [Print os facts] *****************************************************************************************************************************************************************
ERROR! Attempting to decrypt but no vault secrets found
pashi@pashi-docker:~/GolandProjects/8_1$ ansible-playbook -i playbook/inventory/prod.yml playbook/site.yml --ask-vault-pass
Vault password: 

PLAY [Print os facts] *****************************************************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************************************************
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] ***********************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] *********************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP ****************************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

pashi@pashi-docker:~/GolandProjects/8_1$ 
```  
9. Посмотрите при помощи ansible-doc список плагинов для подключения. Выберите подходящий для работы на control node.  
  Если правильно понял задание, то  
```
pashi@pashi-docker:~/GolandProjects/8_1$ ansible-doc -l | grep 'control node'
  [WARNING]: dellemc.openmanage.ome_active_directory has a documentation
  pashi@pashi-docker:~/GolandProjects/8_1$ ansible-doc dellemc.openmanage.ome_active_directory
> DELLEMC.OPENMANAGE.OME_ACTIVE_DIRECTORY    (/home/pashi/.local/lib/python3.8/site-packages/ansible_collections/dellemc/openmanage/plugins/modules/ome_active_directory.py)

        This module allows to add, modify, and delete OpenManage Enterprise connection with Active Directory Service.

ADDED IN: version 4.0.0 of dellemc.openmanage

OPTIONS (= is mandatory):

- ca_path
        The Privacy Enhanced Mail (PEM) file that contains a CA certificate to be used for the validation.
        [Default: (null)]
        type: path
        added in: version 5.0.0 of dellemc.openmanage


- certificate_file
        Provide the full path of the SSL certificate.
        The certificate should be a Root CA Certificate encoded in Base64 format.
        This is applicable when `validate_certificate' is `yes'.
        [Default: (null)]
        type: path
:
...
```
10. В prod.yml добавьте новую группу хостов с именем local, в ней разместите localhost с необходимым типом подключения.  
  Добавил.
11. Запустите playbook на окружении prod.yml. При запуске ansible должен запросить у вас пароль. Убедитесь что факты some_fact для каждого из хостов определены из верных group_vars.  
  Запустил. some_fact отобразился из группы all. Создал папку `group_var/local` и в неё внёс `some_fact: 'local default fact'`, тогда отобразилось `local default fact`  
```
pashi@pashi-docker:~/GolandProjects/8_1$ ansible-playbook -i playbook/inventory/prod.yml playbook/site.yml --ask-vault-pass
Vault password: 

PLAY [Print os facts] *****************************************************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************************************************
ok: [localhost]
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] ***********************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}
ok: [localhost] => {
    "msg": "Ubuntu"
}

TASK [Print fact] *********************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}
ok: [localhost] => {
    "msg": "local default fact"
}

PLAY RECAP ****************************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

pashi@pashi-docker:~/GolandProjects/8_1$ 
```
12. Заполните README.md ответами на вопросы. Сделайте git push в ветку master. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым playbook и заполненным README.md.  
Заполнил playbook/README.md

## Необязательная часть

* При помощи ansible-vault расшифруйте все зашифрованные файлы с переменными.
    
* Зашифруйте отдельное значение PaSSw0rd для переменной some_fact паролем netology. Добавьте полученное значение в group_vars/all/exmp.yml.
  
* Запустите playbook, убедитесь, что для нужных хостов применился новый fact.
  
* Добавьте новую группу хостов fedora, самостоятельно придумайте для неё переменную. В качестве образа можно использовать этот.
  
* Напишите скрипт на bash: автоматизируйте поднятие необходимых контейнеров, запуск ansible-playbook и остановку контейнеров.
  
* Все изменения должны быть зафиксированы и отправлены в вашей личный репозиторий.
