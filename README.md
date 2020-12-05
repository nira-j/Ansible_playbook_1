# Ansible_playbook_1
Ansible playbook for configuration of httpd (webserver) on docker
- hosts: all
  tasks:
    - name: "configuration of yum repo"
      yum_repository:
        name: docker
        description: repo for docker
        file: docker_repos
        baseurl: https://download.docker.com/linux/centos/7/x86_64/stable/
        gpgcheck: no
        
    - command: "yum install docker-ce --nobest -y"
         
    - name: "start docker service"
      service:
        name: docker
        state: started
        enabled: yes      
    - name: "install docker python lib"
      pip:
        name: docker-py
        state: present
        
    - name: "Pull an image"
      docker_image:
        name: httpd
        tag: latest
        source: pull
          
    - name: "pull centos image"
      docker_image:
        name: centos
        tag: latest
        source: pull
    - name: "copying a webpage"
      copy:
        src: "/root/hello.html"
        dest: "/var/www/html/"
        
    - name: "launching the docker container"
      docker_container:
        name: "ansible_docker"
        image: httpd
        state: started
        ports: "1234:80"
        
    - name: "firewall rule declaration"
      firewalld:
        port: "1234/tcp"
        state: enabled
        permanent: yes
        immediate: yes


