# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  # =========================
  # VM 1: HAProxy
  # =========================
  config.vm.define "haproxy" do |proxy|
    proxy.vm.box = "bento/ubuntu-24.04"
    proxy.vm.hostname = "haproxy"

    proxy.vm.network "private_network", ip: "192.168.56.10"
    proxy.vm.network "forwarded_port", guest: 80, host: 8080

    proxy.vm.provider "virtualbox" do |vb|
      vb.name = "haproxy"
      vb.memory = "512"
      vb.cpus = 1
    end

    proxy.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y haproxy curl
      systemctl enable haproxy
      systemctl start haproxy
    SHELL
  end

  # =========================
  # VM 2: Frontend
  # =========================
  config.vm.define "frontend" do |front|
    front.vm.box = "bento/ubuntu-24.04"
    front.vm.hostname = "frontend"

    front.vm.network "private_network", ip: "192.168.56.11"
    front.vm.network "forwarded_port", guest: 8080, host: 8081

    front.vm.provider "virtualbox" do |vb|
      vb.name = "frontend"
      vb.memory = "1536"
      vb.cpus = 1
    end

    front.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y ca-certificates curl git

      install -m 0755 -d /etc/apt/keyrings
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
      chmod a+r /etc/apt/keyrings/docker.asc

      echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" > /etc/apt/sources.list.d/docker.list

      apt-get update
      apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

      systemctl enable docker
      systemctl start docker

      if [ ! -d /home/vagrant/microservice-app-example ]; then
        git clone https://github.com/bortizf/microservice-app-example.git /home/vagrant/microservice-app-example
        chown -R vagrant:vagrant /home/vagrant/microservice-app-example
      fi
    SHELL
  end


  # =========================
  # VM 3: Auth API (Go)
  # =========================
  config.vm.define "auth" do |a|
    a.vm.box = "bento/ubuntu-24.04"
    a.vm.hostname = "auth"

    a.vm.network "private_network", ip: "192.168.56.12"
    a.vm.network "forwarded_port", guest: 8000, host: 8002

    a.vm.provider "virtualbox" do |vb|
      vb.name = "auth"
      vb.memory = "1024"
      vb.cpus = 1
    end

    a.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y ca-certificates curl git

      install -m 0755 -d /etc/apt/keyrings
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
      chmod a+r /etc/apt/keyrings/docker.asc

      echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" > /etc/apt/sources.list.d/docker.list

      apt-get update
      apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

      systemctl enable docker
      systemctl start docker

      usermod -aG docker vagrant

      if [ ! -d /home/vagrant/microservice-app-example ]; then
        git clone https://github.com/bortizf/microservice-app-example.git /home/vagrant/microservice-app-example
        chown -R vagrant:vagrant /home/vagrant/microservice-app-example
      fi
    SHELL
  end


  # =========================
  # VM 4: Users API (Java)
  # =========================
  config.vm.define "users" do |u|
    u.vm.box = "bento/ubuntu-24.04"
    u.vm.hostname = "users"

    u.vm.network "private_network", ip: "192.168.56.13"
    u.vm.network "forwarded_port", guest: 8083, host: 8083

    u.vm.provider "virtualbox" do |vb|
      vb.name = "users"
      vb.memory = "1536"
      vb.cpus = 1
    end

    u.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y ca-certificates curl git

      install -m 0755 -d /etc/apt/keyrings
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
      chmod a+r /etc/apt/keyrings/docker.asc

      echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" > /etc/apt/sources.list.d/docker.list

      apt-get update
      apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

      systemctl enable docker
      systemctl start docker

      usermod -aG docker vagrant

      if [ ! -d /home/vagrant/microservice-app-example ]; then
        git clone https://github.com/bortizf/microservice-app-example.git /home/vagrant/microservice-app-example
        chown -R vagrant:vagrant /home/vagrant/microservice-app-example
      fi
    SHELL
  end


  # =========================
  # VM 5: Redis
  # =========================
  config.vm.define "redis" do |r|
    r.vm.box = "bento/ubuntu-24.04"
    r.vm.hostname = "redis"

    r.vm.network "private_network", ip: "192.168.56.16"
    r.vm.network "forwarded_port", guest: 6379, host: 6379

    r.vm.provider "virtualbox" do |vb|
      vb.name = "redis"
      vb.memory = "512"
      vb.cpus = 1
    end

    r.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y ca-certificates curl git

      install -m 0755 -d /etc/apt/keyrings
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
      chmod a+r /etc/apt/keyrings/docker.asc

      echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" > /etc/apt/sources.list.d/docker.list

      apt-get update
      apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

      systemctl enable docker
      systemctl start docker

      usermod -aG docker vagrant
    SHELL
  end

  # =========================
  # VM 6: TODOs API
  # =========================
  config.vm.define "todos" do |t|
    t.vm.box = "bento/ubuntu-24.04"
    t.vm.hostname = "todos"

    t.vm.network "private_network", ip: "192.168.56.14"
    t.vm.network "forwarded_port", guest: 8082, host: 8084

    t.vm.provider "virtualbox" do |vb|
      vb.name = "todos"
      vb.memory = "1024"
      vb.cpus = 1
    end

    t.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y ca-certificates curl git

      install -m 0755 -d /etc/apt/keyrings
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
      chmod a+r /etc/apt/keyrings/docker.asc

      echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" > /etc/apt/sources.list.d/docker.list

      apt-get update
      apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

      systemctl enable docker
      systemctl start docker

      usermod -aG docker vagrant

      if [ ! -d /home/vagrant/microservice-app-example ]; then
        git clone https://github.com/bortizf/microservice-app-example.git /home/vagrant/microservice-app-example
        chown -R vagrant:vagrant /home/vagrant/microservice-app-example
      fi
    SHELL
  end



  # =========================
  # VM 7: Log Message Processor
  # =========================
  config.vm.define "processor" do |p|
    p.vm.box = "bento/ubuntu-24.04"
    p.vm.hostname = "processor"

    p.vm.network "private_network", ip: "192.168.56.15"

    p.vm.provider "virtualbox" do |vb|
      vb.name = "processor"
      vb.memory = "1024"
      vb.cpus = 1
    end

    p.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y ca-certificates curl git

      install -m 0755 -d /etc/apt/keyrings
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
      chmod a+r /etc/apt/keyrings/docker.asc

      echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" > /etc/apt/sources.list.d/docker.list

      apt-get update
      apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

      systemctl enable docker
      systemctl start docker

      usermod -aG docker vagrant

      if [ ! -d /home/vagrant/microservice-app-example ]; then
        git clone https://github.com/bortizf/microservice-app-example.git /home/vagrant/microservice-app-example
        chown -R vagrant:vagrant /home/vagrant/microservice-app-example
      fi
    SHELL
  end



  # =========================
  # VM 7b: TODOs API - segunda instancia
  # =========================
  config.vm.define "todos2" do |t|
    t.vm.box = "bento/ubuntu-24.04"
    t.vm.hostname = "todos2"

    t.vm.network "private_network", ip: "192.168.56.17"
    t.vm.network "forwarded_port", guest: 8082, host: 8085

    t.vm.provider "virtualbox" do |vb|
      vb.name = "todos2"
      vb.memory = "1024"
      vb.cpus = 1
    end

    t.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y ca-certificates curl git

      install -m 0755 -d /etc/apt/keyrings
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
      chmod a+r /etc/apt/keyrings/docker.asc

      echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" > /etc/apt/sources.list.d/docker.list

      apt-get update
      apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

      systemctl enable docker
      systemctl start docker

      usermod -aG docker vagrant

      if [ ! -d /home/vagrant/microservice-app-example ]; then
        git clone https://github.com/bortizf/microservice-app-example.git /home/vagrant/microservice-app-example
        chown -R vagrant:vagrant /home/vagrant/microservice-app-example
      fi
    SHELL
  end

  # =========================
  # VM 8: Zipkin (Tracing)
  # =========================
  config.vm.define "zipkin" do |z|
    z.vm.box = "bento/ubuntu-24.04"
    z.vm.hostname = "zipkin"

    z.vm.network "private_network", ip: "192.168.56.18"
    z.vm.network "forwarded_port", guest: 9411, host: 9411

    z.vm.provider "virtualbox" do |vb|
      vb.name = "zipkin"
      vb.memory = "512"
      vb.cpus = 1
    end

    z.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y ca-certificates curl git

      install -m 0755 -d /etc/apt/keyrings
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
      chmod a+r /etc/apt/keyrings/docker.asc

      echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" > /etc/apt/sources.list.d/docker.list

      apt-get update
      apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

      systemctl enable docker
      systemctl start docker
      usermod -aG docker vagrant
    SHELL
  end


end