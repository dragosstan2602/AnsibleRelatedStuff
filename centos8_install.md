Ansible AWX on CentOS8
https://computingforgeeks.com/install-and-configure-ansible-awx-on-centos/

# Step 1: Install Epel Release Repo and Dependencies

```
sudo dnf update -y && sudo dnf -y install epel-release && sudo dnf -y install dnf-plugins-core && sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo dnf config-manager --set-enabled PowerTools && sudo dnf install -y git python3-pip curl ansible gcc nodejs gcc-c++  gettext lvm2 device-mapper-persistent-data pwgen bzip2 tree vim
```

# Disable SELinux

```
sudo sed -i 's/^SELINUX=.*/SELINUX=permissive/g' /etc/selinux/config
sudo setenforce 0
```

# Step 2: Install Docker and Docker Compose

```
sudo curl  https://download.docker.com/linux/centos/docker-ce.repo -o /etc/yum.repos.d/docker-ce.repo
sudo yum makecache
sudo dnf -y  install docker-ce --nobest
sudo systemctl enable --now docker
systemctl status  docker
sudo usermod -aG docker $USER
sudo python3 -m pip install -U docker docker-compose
docker-compose version
```


# Step 3: Clone AWX from git

```
cd ~ && git clone --depth 50 https://github.com/ansible/awx.git
cd ~/awx/installer/
```

# inventory file should have these options set - AT LEAST
```
[all:vars]
dockerhub_base=ansible
awx_task_hostname=awx
awx_web_hostname=awxweb
postgres_data_dir="~/.awx/pgdocker"
host_port=80
host_port_ssl=443
docker_compose_dir="~/.awx/awxcompose"
pg_username=awx
pg_password=awxpass
pg_database=awx
pg_port=5432
admin_user=admin
admin_password=SuperSecret
create_preload_data=True
project_data_dir=/var/lib/awx/projects  ##Directory For playbooks inside the server 
awx_alternate_dns_servers="8.8.8.8,8.8.4.4"
secret_key=yBs76VurxRiBwtDHrrF2JJlLgVrcv3
awx_official=true
```

# Alter Firewall Rules

```
sudo firewall-cmd --zone=public --add-masquerade --permanent && sudo firewall-cmd --permanent --add-service={http,https} && sudo firewall-cmd --reload
```

# Add AWX project data folder

```
sudo mkdir -p /var/lib/awx/projects
```

# Execute install playbook
```
sudo ansible-playbook -i inventory install.yml
```

# When finished chcke docker containers are up (4 of them)
# Login into the console http://IP_ADDRESS (admin/password default creds)
# If stuck in updating database phase reboot Ansible AWX VM/Host

