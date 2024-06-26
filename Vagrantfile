# -*- mode: ruby -*-
# vi: set ft=ruby :

MACHINES = {
    :testRAID => {
        :box_name => "centos/7",
        :box_version => "0",
        :cpus => 2,
        :memory => 1024,
        :disks => {
            :sata1 => {
                :dfile => './sata1.vdi',
                :size => 250,
                :port => 1
            },
            :sata2 => {
                :dfile => './sata2.vdi',
                :size => 250,
                :port => 2
            },
            :sata3 => {
                :dfile => './sata3.vdi',
                :size => 250,
                :port => 3
            },
            :sata4 => {
                :dfile => './sata4.vdi',
                :size => 250,
                :port => 4
            },
            :sata5 => {
                :dfile => './sata5.vdi',
                :size => 250,
                :port => 5
            }
        }
    }
}

Vagrant.configure("2") do |config|
    MACHINES.each do |boxname, boxconfig|
        config.vm.define boxname do |box|
            box.vm.box = boxconfig[:box_name]
            box.vm.box_version = boxconfig[:box_version]
            box.vm.host_name = boxname.to_s;
            box.vm.provider "virtualbox" do |v|
	          config.vm.synced_folder ".", "/vagrant", disabled: false
			  v.gui = true
              v.memory = boxconfig[:memory]
              v.cpus = boxconfig[:cpus]
              needsController = false
              boxconfig[:disks].each do |dname, dconf|
                unless File.exist?(dconf[:dfile])
                  v.customize ['createhd', '--filename', dconf[:dfile], '--variant', 'Fixed', '--size', dconf[:size]]                  
                  needsController = true
                end
              end 
                if needsController == true   
                  v.customize ['storagectl', :id, '--name', 'SATA', '--add', 'sata']
                  boxconfig[:disks].each do |dname, dconf|
                    v.customize ['storageattach', :id, '--storagectl', 'SATA', '--port', dconf[:port], '--device', 0, '--type', 'hdd', '--medium', dconf[:dfile]]
                  end
                end
            end

            box.vm.provision "shell", inline: <<-SHELL
              sudo mkdir -p ~root/.ssh
              sudo cp ~vagrant/.ssh/auth* ~root/.ssh
	          sudo yum install -y mdadm smartmontools hdparm gdisk
            SHELL
            box.vm.provision "shell", path: "make_raid.sh"
        end    
    end
end
