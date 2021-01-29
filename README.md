## vagrant-footloose

This is a hack boilerplate setup using a couple of different ideas/tools to create an immutable multi-node dev environment.

Its principally built with docker as the pseudo-hyervisor, footloose as an easy way to spin up multiple vm-like containers, vagrant to manage their provisioning/validation and VScode remote-containers to handle environment.

The only two things you should require installed are docker and VScode. I've included some common configuration testing tools (ansible, cinc-auditor).

### setup

The only two things you should require installed are docker and VScode. I've included some common configuration testing tools (ansible, cinc-auditor). you will need to add the remote-containers extension (https://code.visualstudio.com/docs/remote/containers-tutorial) which, once installed, should detect the .devcontainer forloder and automatically prompt to  redeploy inside it.

Once thats conmplete you should have all the binaries and config needed to simply run commands pertaining to the tools

### usage

you should be able to customize the vars at the top of the vagrant file to your desired spec and then `vagrant up` should be enough to deploy the containers and provisioning ssh access. the included vagrant plugin ensures that the `vagrant ssh` works and should also ensure that other provisioners (chef, ansible, shell) can  also access the vm's. `vagrant destroy` will delink the containers and destroy the harness. 

there is one additional custom command - `vagrant provision --provision-with audit` which utilises cinc-auditor to access the machines on a per node basis and scan them using the inspec compliance framework. you could swap commmand out for any ssh-based integration testing tool, I just find this convenient (and free!).

I have included ansible in the dev env but it hasnt been added to vagrant (the intention is to fork this repo and add in config testing at as and when you need).

### links

remote-containers - https://code.visualstudio.com/docs/remote/containers-tutorial
ansible - https://docs.ansible.com/ansible/latest/index.html
cinc-auditor- https://cinc.sh/start/auditor/
vagrant - https://www.vagrantup.com
vagrant-managed-ssh - https://github.com/tknerr/vagrant-managed-servers