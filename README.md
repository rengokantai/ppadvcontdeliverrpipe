# ppadvcontdeliverrpipe

```
apt install software-properties-common
apt-add-repository ppa:ansible/ansible
apt update && apt install -y ansible
```
```
vim /etc/ansible/hosts
```
copy local windows private key to remote
```
scp ~/.ssh/id_rsa root@ip:~/.ssh/
```

ke.yml
```
---
- hosts: web1
  become: yes
  become_method: sudo
  tasks:
  - name: ensure
    apt: name=apache2 state=latest
    name: ensure apache is running
    service: name=apache2 state=starting enabled=yes
```

java.yml
```
ke.yml
```
---
- hosts: web1
  become: yes
  become_method: sudo
  tasks:
  - name: eusure java runtime is installed
    apt:
      name: default-jre
      state: present
  - name: create directory for calculator
    file:
      path: /var/calculator
      state: directory
  - name: configure service
    file:
      path: /etc/init.d/calculator
      state: link
      force: yes
      src: /var/calculator/calculator.jar
```    

```
ke.yml
```
---
- hosts: web1
  become: yes
  become_method: sudo
  tasks:
  - name: add docker
    apt_key:
      keyserver: hkp://p80:pool.sks-keyservers.net:80
      id:58118E89F3A912897C070ADBF76221572C52609D
  - name: update apt
    apt_repository:
      repo: deb https://apt.dockerproject.org/repo ubuntu-xenial main
      state: present
  - name: install docker
    apt:
      name: docker-engine
      update_cache: yes
      state: present
  - name: add ubuntu to docker group
    user:
      name: root
      groups: docker
      append: yes
  - name: install pip
    apt:
      name: python-pip
      state: present
  - name: install docker-py
    pip:
      name: docker-py
  - name: install docker compose
    pip:
      name: docker-compose
      version: 1.9.0
  - name: copy docker-compose
    copy:
      src: ./docker-compose.yml
      dest: ./docker-compose.yml
  - name: run docker-compose
    environment:
      BUILD_TIMESTAMP: "{{ lookup('env', 'BUILD TIMESTAMP') }}"
    docker_service:
      project_src: .
      state: present
      restarted: yes
```
docker-compose.yml
```
version: "2"
services:
  calculator:
    image: leszko/calculator:latest
    ports:
    - 8080
  redis:
    deploy:
      replicas: 1
    imate: redis:latest
```
