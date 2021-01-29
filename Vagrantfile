ENV['VAGRANT_EXPERIMENTAL']="typed_triggers"
ENV["VAGRANT_I_KNOW_WHAT_IM_DOING_PLEASE_BE_QUIET"]="true"
VAGRANTFILE_API_VERSION = "2"


cmd        = '' 
file       = 'footloose.yaml'
image      = 'quay.io/footloose/centos7:0.5.0' 
key        = 'cluster-key' 
name       = 'cluster' 
override   = false 
privileged = false 
replicas   = 5    
status     = '.vagrant/machines/status.json'
upper      = replicas -1 

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "tknerr/managed-server-dummy"

  puts `footloose status -o json | jq -r . > "#{status}" `

  config.trigger.before :environment_plugins_loaded, type: :hook do |trigger|
    trigger.only_on = "node0"
    trigger.info = "bringing up footloose machines with vagrant hook"
    trigger.ruby do |env,machine|
      puts `footloose config create --override -r "#{replicas}" -k "#{key}" -i "#{image}" -n "#{name}" -c "#{file}"`
      puts `footloose create`
      puts `footloose status -o json | jq -r . > "#{status}" `
    end
  end

  config.trigger.after :destroy do |trigger|
    trigger.only_on = "node0"
    trigger.info = "deleting footloose machines and file: #{status} with vagrant hook"
    trigger.ruby do |env,machine|
      puts `footloose delete`
      File.delete(status) if File.exist?(status)
    end
  end

  (0..upper).each do |i|
    config.vm.define "node#{i}" do |subconfig|
      subconfig.vm.hostname = "node#{i}"
      subconfig.vm.provider :managed do |managed, override|
        managed.server = YAML.load_file(status)['machines'][i]['ip']
        override.ssh.username = "root"
        override.ssh.private_key_path = key
      end
    end
  end
end
