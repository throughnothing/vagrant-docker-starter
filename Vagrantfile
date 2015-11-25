require 'yaml'
vagrant_config = YAML.load_file('vagrant-config.yml')

unless Vagrant.has_plugin?('nugrant')
  warn "ERROR: Please run: vagrant plugin install nugrant"
  exit -1
end

Vagrant.configure(2) do |config|

  config.user.defaults = vagrant_config['docker_host']

  # Set up docker containers
  vagrant_config['containers'].keys.each do |name|
    config.vm.define name do |cont|
      cont.vm.synced_folder ".", "/vagrant", disabled: true
      cont.vm.provider "docker" do |docker|
        node = vagrant_config['containers'][name]

        # Attach each docker container to docker-host defined below
        docker.force_host_vm = true
        docker.vagrant_vagrantfile = "./Vagrantfile"
        docker.vagrant_machine = "docker-host"
        docker.name = name

        if node['links']
          node['links'].each do |link|
            docker.link(link)
          end
        end

        if node['image']
          docker.image = node['image']
        end

        if node['volumes']
          docker.volumes = node['volumes']
        end

        if node['environment']
          docker.env = node['environment']
        end

        if node['ports']
          docker.ports = node['ports']
        end

        if node['cmd']
          docker.cmd = node['cmd']
        end

        if node['build_dir']
          docker.build_dir = node['build_dir']
        end

        if node['dockerfile']
          docker.dockerfile = node['dockerfile']
        end
      end
    end
  end


  # docker-host machine
  # This is the Main VM that will run all the docker containers
  config.vm.define "docker-host" do |dh|
    dh.vm.hostname = "docker-host"
    dh.vm.box = "ubuntu/trusty64"
    dh.ssh.insert_key = false

    dh.vm.provision "docker"
    # Assumes its running from ops/
    dh.vm.synced_folder "../", "/vagrant"

    dh.vm.provider "virtualbox" do |vb|
      vb.gui = config.user.box.gui
      vb.memory = config.user.box.memory
      vb.cpus = config.user.box.cpus
    end

    config.user.box.ports.each do |port|
      config.vm.network "forwarded_port", guest: port.guest, host: port.host
    end

    # The following line terminates all ssh connections. Therefore
    # Vagrant will be forced to reconnect.
    # That's a workaround to have the docker command in the PATH and
    # add Vagrant to the docker group by logging in/out
    config.vm.provision "shell", :inline =>
      "ps aux | grep 'sshd:' | awk '{print $2}' | xargs kill"
  end

end

# -*- mode: ruby -*-
# vi: set ft=ruby :

