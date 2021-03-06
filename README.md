# simplefilesync-ansible-role
Ansible role to deploy https://github.com/diademiemi/SimpleFileSync on a set of servers

## Required variables
| Variable | Description |
|---|---|
| `synced_files` | List of files to sync across hosts |

## Optional variabls
| Variable | Default | Description |
|---|---|---|
| `bind_ip` | Will attempt to get default IP | IP to bind the program to. Highly recommended to set this manually per host! Ansible is very unreliable when getting IPs |
| `other_ips` | Will attempt to get default IP for other hosts | IPs of the remote hosts to connect to and allow connections from. Highly recommended to set this manually per host! Ansible is very unreliable when getting IPs |
| `destination` | `/opt/simplefilesync` | Location to clone the program to
| `config_location` | `/opt/simplefilesync/sync.yaml` | Config file location
| `bind_port` | `54321` | Port to bind to
| `statefile` | `/tmp/simplefilesync.state` | Where to put the statefile

## Example playbook
To make a pool syncing the file `/home/vagrant/test.txt` across 3 servers `192.168.50.11`, `192.168.50.12` and `192.168.50.13`.
Check the collapsed host_vars section for the variables set per host.  

<details><summary> Example Vagrantfile </summary><p>

```ruby
Vagrant.configure("2") do |config|

  config.vm.box = "generic/centos8"
  (1..3).each do |i| 
    config.vm.define "server#{i}" do |host|
    host.vm.hostname = "server#{i}"
    host.vm.network "private_network", ip: "192.168.50.1#{i}"

    host.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--memory", "1024"]
      end
    end
  end
end


```

</details>

<details><summary> <code> host_vars/ </code> </summary><p>

<details open><summary> <code> server1.yml </code> </summary><p>

```yaml
bind_ip: 192.168.50.11
other_ips:
- 192.168.50.12
- 192.168.50.13
```
</p>

</details>

<details><summary> <code> server2.yml </code> </summary><p>

```yaml
bind_ip: 192.168.50.12
other_ips:
- 192.168.50.11
- 192.168.50.13
```
</p>

</details>
<details><summary> <code> server2.yml </code> </summary><p>

```yaml
bind_ip: 192.168.50.13
other_ips:
- 192.168.50.11
- 192.168.50.12
```
</p>

</details>


</p>
</details>

```yaml
- hosts: servers
  become: True
  roles:
    - simplefilesync-ansible-role
  vars:
    synced_files:
      - '/home/vagrant/test.txt
```