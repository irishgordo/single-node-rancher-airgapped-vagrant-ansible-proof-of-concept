# Pre-reqs:
- ansible
- vagrant
- qemu 
- libvirt-daemon-system 
- libvirt-clients 
- ebtables 
- dnsmasq-base
- libvirt

# Setup:
- ansible-galaxy collection install community.general
- vagrant plugin install vagrant-libvirt

# Run
- ansible-playbook ansible/setup_rancher_single_node.yml --extra-vars "@settings.yml"

# Cleanup
- vagrant destroy (from within root folder)