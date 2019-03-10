master_cnt = 1
node_cnt = 3
base_master_ip = "10.0.0"
base_node_ip = "10.0.1"

$bootstrap_script = <<SCRIPT
if [ ! -f ~/.ssh/id_rsa ]; then
    cp /vagrant/ssh/id_rsa ~/.ssh/id_rsa
    cat /vagrant/ssh/id_rsa.pub >> ~/.ssh/authorized_keys
    cp /vagrant/ssh/config ~/.ssh/config
fi
chmod 600 ~/.ssh/*

for x in {11..#{10+master_cnt}}; do
  grep #{base_master_ip}.${x} /etc/hosts &>/dev/null || {
      echo #{base_master_ip}.${x} master${x##?} | sudo tee -a /etc/hosts &>/dev/null
  }
done

for x in {11..#{10+node_cnt}}; do
  grep #{base_node_ip}.${x} /etc/hosts &>/dev/null || {
      echo #{base_node_ip}.${x} node${x##?} | sudo tee -a /etc/hosts &>/dev/null
  }
done

SCRIPT

$ansible_hosts = <<SCRIPT
cat >> /etc/ansible/hosts <<EOF
[nodes]
node[1:#{node_cnt}]
EOF
SCRIPT

Vagrant.configure("2") do |config|
  # for mac os https://www.vagrantup.com/docs/synced-folders/rsync.html
  config.vm.synced_folder ".", "/vagrant", type: "rsync", rsync__auto: true

  config.vm.box = "centos/7"

  # use aliyun yum repo
  config.vm.provision :shell,
    privileged:true,
    inline: "mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup; " \
            "curl http://mirrors.aliyun.com/repo/Centos-7.repo --output /etc/yum.repos.d/CentOS-Base.repo; " \
            "rm -rf /var/cache/yum; yum clean all; yum makecache"

  config.vm.provision "shell", privileged: false, inline: $bootstrap_script

  (1..1).each do |i|
    config.vm.define "master#{i}" do |master|
      master.vm.hostname = "master#{i}"
      master.vm.network :private_network, ip: "#{base_master_ip}.#{i + 10}"

      master.vm.provision "shell", privileged:true, inline: "yum install ansible --assumeyes"
      master.vm.provision "shell", privileged:true, inline: $ansible_hosts
    end
  end

  (1..3).each do |i|
    config.vm.define "node#{i}" do |node|
      node.vm.hostname = "node#{i}"
      node.vm.network :private_network, ip: "#{base_node_ip}.#{i + 10}"

      node.vm.provision "shell",
        inline: "echo hello from node #{i}"
    end
  end
end
