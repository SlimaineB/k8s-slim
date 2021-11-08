# ####################################################################
# ################### CONFIGURATION VARIABLES ########################
# ####################################################################
IMAGE_NAME = "bento/ubuntu-21.04"   	# Image to use
NFS_NAME="nfs-server"                	# Worker prefix node name 
NFS_MEM = 2048                   	# Amount of RAM
NFS_CPU = 2                   	# Amount of RAM
NFS_IP = "192.168.56.15"           	# IP of NFS SERVER
WORKER_NAME="worker"                	# Worker prefix node name
WORKER_NBR = 1                      	# Number of workers node  
WORKER_MEM = 4096                   	# Amount of RAM
WORKER_CPU = 3                      	# Number of processors (Minimum value of 2 otherwise it will not work)
MASTER_MEM = 2048                   	# Amount of RAM
MASTER_CPU = 2                      	# Number of processors (Minimum value of 2 otherwise it will not work)
MASTER_NAME="master"                	# Master node name
NODE_NETWORK_BASE = "192.168.56"    	# First three octets of the IP address that will be assign to all type of nodes
POD_NETWORK = "192.168.100.0/16"    	# Private network for inter-pod communication


# ################### NFS CREATION ########################
# ############################################################
Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    # RAM and CPU config
    config.vm.provider "virtualbox" do |v|
        v.memory = NFS_MEM
        v.cpus = NFS_CPU
    end

    # Master node config
    config.vm.define NFS_NAME do |nfssever|
        
        # Hostname and network config
        nfssever.vm.box = IMAGE_NAME
        nfssever.vm.network "private_network", ip: "#{NFS_IP}"
        nfssever.vm.hostname = NFS_NAME

        # Ansible role setting
        nfssever.vm.provision "ansible" do |ansible|
            
            # Ansbile role that will be launched
            ansible.playbook = "roles/main.yml"

            # Groups in Ansible inventory
            ansible.groups = {
                "nfs-server" => ["#{NFS_NAME}"]
            }

            # Overload Anqible variables
            ansible.extra_vars = {
#                node_ip: "#{NODE_NETWORK_BASE}.10",
#                node_name: "master",
#                pod_network: "#{POD_NETWORK}"
            }
        end
    end
end

# ################### MASTER CREATION ########################
# ############################################################
Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    # RAM and CPU config
    config.vm.provider "virtualbox" do |v|
        v.memory = MASTER_MEM
        v.cpus = MASTER_CPU
    end

    # Master node config
    config.vm.define MASTER_NAME do |master|
        
        # Hostname and network config
        master.vm.box = IMAGE_NAME
        master.vm.network "private_network", ip: "#{NODE_NETWORK_BASE}.10"
        master.vm.hostname = MASTER_NAME

        # Ansible role setting
        master.vm.provision "ansible" do |ansible|
            
            # Ansbile role that will be launched
            ansible.playbook = "roles/main.yml"

            # Groups in Ansible inventory
            ansible.groups = {
                "masters" => ["#{MASTER_NAME}"],
                "workers" => ["worker-[1:#{WORKER_NBR}]"]
            }

            # Overload Anqible variables
            ansible.extra_vars = {
                node_ip: "#{NODE_NETWORK_BASE}.10",
                node_name: "master",
                pod_network: "#{POD_NETWORK}"
            }
        end
    end
end



# ################### WORKERS CREATION ########################
# #############################################################
Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    # RAM and CPU config
    config.vm.provider "virtualbox" do |v|
        v.memory = WORKER_MEM
        v.cpus = WORKER_CPU
    end

    # Worker node config
    (1..WORKER_NBR).each do |i|
        config.vm.define "#{WORKER_NAME}-#{i}" do |worker|

            # Hostname and network config
            worker.vm.box = IMAGE_NAME
            worker.vm.network "private_network", ip: "#{NODE_NETWORK_BASE}.#{i + 10}"
            worker.vm.hostname = "worker-#{i}"

            # Ansible role setting
            worker.vm.provision "ansible" do |ansible|

                # Ansbile role that will be launched
                ansible.playbook = "roles/main.yml"

                # Groups in Ansible inventory
                ansible.groups = {
                    "masters" => ["#{MASTER_NAME}"],
                    "workers" => ["worker-[1:#{WORKER_NBR}]"]
                }

                # Overload Anqible variables
                ansible.extra_vars = {
                    node_ip: "#{NODE_NETWORK_BASE}.#{i + 10}"
                }
            end
        end
    end
end
