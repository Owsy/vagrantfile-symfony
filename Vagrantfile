
require "yaml"

# vim filetype=ruby

dir = File.dirname(File.expand_path(__FILE__))

if File.exist?("#{dir}/settings.yml")
    settings = YAML.load_file("#{dir}/settings.yml")
else
    settings = YAML.load_file("#{dir}/settings.yml.dist")
end

Vagrant.configure("2") do |config|

  symfony_host = "../symfony-webapp/"
  symfony_guest = "/opt/symfony-webapp"
  symfony_user = "user"
  symfony_group = "group"

  if File.directory?(symfony_host) then
    if settings['sync_type'] == 'rsync' then
      unless Vagrant.has_plugin? 'vagrant-gatling-rsync'
        raise 'You need to install the "vagrant-gatling-rsync" plugin.'
      end

      config.vm.synced_folder symfony_host, symfony_guest, type: "rsync", owner: symfony_user, group: symfony_group,
      rsync__args: ["--verbose", "--archive", "--delete", "-z", "--copy-links"],
      rsync__exclude: settings['rsync_exclude']

      if Vagrant.has_plugin?("vagrant-bindfs") then
          settings['synced_folders'].each do |folder|
            config.vm.synced_folder symfony_host + folder, "/vagrant-nfs/" + folder, type: "nfs", nfs_udp: false
            config.bindfs.bind_folder  "/vagrant-nfs/" + folder, symfony_guest + '/' + folder, 'force-user' => symfony_user, 'force-group' => symfony_group
          end
      end

      config.gatling.latency = 0.2
      config.gatling.time_format = "%H:%M:%S"
      config.gatling.rsync_on_startup = settings['rsync_watch']
    else
      config.vm.synced_folder symfony_host, symfony_guest, type: "nfs", nfs_udp: false
    end
  end

end
