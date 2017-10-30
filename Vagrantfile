Vagrant.configure(2) do |config|
  config.vm.define "chinachu"

  config.vm.box = "ubuntu/xenial64"

  config.vm.synced_folder "./conf",     "/chinachu_conf"
  config.vm.synced_folder "./data",     "/usr/local/chinachu/data"
  config.vm.synced_folder "./recorded", "/usr/local/chinachu/recorded"
  config.vm.synced_folder "./log",      "/usr/local/chinachu/log"
  
  config.vm.network "forwarded_port", guest: 10772, host: 10772
  config.vm.network "forwarded_port", guest: 20772, host: 20772
  config.vm.network "forwarded_port", guest: 5353,  host: 5353, protocol: "udp"

  config.vm.provider :virtualbox do |vb|
    vb.customize [
      "modifyvm", :id,
      "--hwvirtex", "on",
      "--nestedpaging", "on",
      "--largepages", "on",
      "--memory", "4096",           # 割り当てるRAM(お好きな値で)
      "--cpus", "4",                # 割り当てるコア数(お好きな値で)
      "--ioapic", "on",
      "--pae", "on",
      "--paravirtprovider", "kvm"
    ]
  end

  if ARGV[0] == "up" || ARGV[0] == "provision" then
    config.vm.provision :shell do |shell|
      shell.inline = <<-SHELL
        MIRAKURUN_IP=`netstat -rn | grep "^0.0.0.0 " | cut -d " " -f10`
        USER_ID="12345"
        USER_NAME="chinachu"
        WORK_DIR="/usr/local/chinachu"
        REPOSITORY="git://github.com/kanreisa/Chinachu.git"
        echo "${MIRAKURUN_IP} container-mirakurun" | sudo tee -a /etc/hosts && \
        sudo sed -i.bak -e "s%http://[^ ]\+%http://ftp.jaist.ac.jp/pub/Linux/ubuntu/%g" /etc/apt/sources.list && \
        sudo apt update && \
        sudo apt install -y \
          build-essential \
          curl \
          git-core \
          vainfo && \
        curl sL https://deb.nodesource.com/setup_6.x | sudo -E bash - && \
        sudo apt install -y nodejs && \
        sudo npm install pm2 -g && \
        sudo pm2 startup && \
        sudo chown -R ubuntu:ubuntu /usr/local/chinachu && \
        git clone ${REPOSITORY} /tmp/chinachu && \
        cp -R /tmp/chinachu/* ${WORK_DIR} && \
        sudo rm -R -f /tmp/chinachu && \
        sudo ln -sf /chinachu_conf/config.json ${WORK_DIR}/config.json && \
        sudo ln -sf /chinachu_conf/rules.json ${WORK_DIR}/rules.json && \
        cd ${WORK_DIR} && \
        echo 1 | ./chinachu installer && \
        sudo pm2 start processes.json && \
        sudo pm2 save && \
        echo "-----SUCCESS!!!!!!-----"
      SHELL
      shell.privileged = false
    end
  end
end
