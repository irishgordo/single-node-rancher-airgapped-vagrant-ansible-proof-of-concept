# vi: set ft=ruby ts=2 :

require 'yaml'

VAGRANTFILE_API_VERSION = "2"

# check for required plugins
_required_plugins_list = %w{vagrant-libvirt}
exit(1) unless _required_plugins_list.all? do |plugin|
  Vagrant.has_plugin?(plugin) || (
    STDERR.puts "Required plugin '#{plugin}' is missing; please install using:"
    STDERR.puts "  % vagrant plugin install #{plugin}"
    false
  )
end

# ensure libvirt is the default provider in case the vagrant box config
# doesn't specify it
ENV['VAGRANT_DEFAULT_PROVIDER'] = "libvirt"

@root_dir = File.dirname(File.expand_path(__FILE__))
@settings = YAML.load_file(File.join(@root_dir, "settings.yml"))

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.vm.graceful_halt_timeout = 120

    config.vm.define :rancher_box do |rancher_box|
        rancher_box.vm.box = 'peru/ubuntu-20.04-desktop-amd64'
        rancher_box.vm.hostname = 'rancher-single-node'
        rancher_box.vm.provider :libvirt do |libvirt|
            libvirt.cpu_mode = 'host-passthrough'
            libvirt.memory = '16000'
            libvirt.cpus = '8'
            libvirt.storage :file,
                size: @settings['rancher_config']['node_disk_size'],
                type: 'qcow2',
                bus: 'virtio',
                device: 'vdb'
            libvirt.boot 'hd'
        end
        # Do some magic with ansible to provision the server
        rancher_box.vm.provision :ansible do |ansible|
            #ansible.verbose ="vvv"
            ansible.playbook = 'ansible/setup_rancher_node.yml'
            ansible.extra_vars = {
                settings: @settings
            }
        end
    
    end

end
