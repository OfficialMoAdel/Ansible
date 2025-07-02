---
tags:
  - linux
  - course
jinia2: https://notes.kodekloud.com/docs/Jinja2-Basics-Mini-Course/Jinja2/Jinja2-Basics
description: https://notes.kodekloud.com/docs/Ansible-Advanced-Course/Introduction/Course-Introduction
---
[[Linux]]

| number of modules | ansible-doc -l \| grep azure \| wc -l |
| ----------------- | ------------------------------------- |
|                   |                                       |
|                   |                                       |

[with Keroles](https://www.youtube.com/playlist?list=PLdTZTP1X_y5o3p7mpMy4T-3X8ukgFg9kJ)
[with Alaa Mohamed](https://www.youtube.com/playlist?list=PLvbyo73L4vfthKUsf0guv2uxrAy255Ht-)

###### lab
https://kodekloud.com/pages/free-labs/ansible
https://killercoda.com/het-tanis/course/Ansible-Labs


https://kestra.io/
https://semaphoreui.com/

###### link
https://gitlab.com/xavki/devopsland/-/tree/master/ansible?ref_type=heads
https://syshunt.com/ansible-playbook-generator/
https://orhanergun.net/top-10-ansible-playbooks-for-efficient-network-automation
https://medium.com/@jeromedecinco/12-best-practices-to-follow-before-running-an-ansible-playbook-on-red-hat-enterprise-linux-37ccd16b4834#808e
https://linuxconfig.org/introduction-to-ansible-prompts-and-runtime-variables
###### create new user
useradd ansible
echo 'P@ssw0rd' | passwd --stdin ansbile
###### Edit sudo configuration
echo 'ansible ALL=(ALL)       NOPASSWD:ALL' > /etc/sudoers.d/ansible

###### install ansible
yum install epel-release -y
yum install python3 ansible
python3 --version

```bash
for i in {1..3}; do scp /etc/hosts ansible"$i":/etc/hosts; done
```
generate ssh-key and copy it to the managed node
su -ansible
ssh-keygen
```bash
for i in {1..3}; do ssh copy id ansible"$i".example.com:/etc/hosts; done
```


```bash
cat /etc/hostsi

192.168.121.148  control control.example.com
192.168.121.147  ansible1 ansible1.example.com
192.168.121.142  ansible2 ansible2.example.com
192.168.121.146  ansible3 ansible3.example.com


 sudo hostnamectl set-hostname control


for i in {1..3}; do ping -c 2 ansible$i ; done

for i in {1..3}; do ssh ansible$i "useradd ansible" ; done
P@ssw0rd
 for i in {1..3}; do ssh ansible$i "echo P@ssw0rd | passwd --stdin ansbile" ; done

for i in {1..3}; do
  ssh ansible$i "echo \"ansible:P@ssw0rd\" | sudo chpasswd"
done


for i in {1..3}; do ssh ansible$i "echo 'ansible ALL=(ALL)       NOPASSWD:ALL' > /etc/sudoers.d/ansible"; done

for i in {1..3}; do ssh ansible$i "python3 --version" ; done

for i in {1..3}; do ssh-copy-id ansible$i ; done
3972Sknk782&

```

#### Example ansible.cfg
```bash
$cat ansible.cfg


[defaults]
inventory = inventory
remote user = ansible
#host_key_checking = false


[privilege_escalation]
become = True
become method = sudo
become user = root
become_ask_pass = False



 cat inventory
[web]
#ansible1.example.com
ansible1
ansible3

[db]
#ansible2.example.com
ansible2

[datacenter:children]  # <<< Corrected spelling here
web
db



ansible all -m ping
ansible ansible1 -m ping

ansible all -m user -a 'name=blue'

ansible all -m user -a 'name=blue state=absent'
ansible all -m user -a 'name=red state=present'

ansible all -m command -a 'systemctl status sshd'
ansible all -m command -a 'ls -l /etc | grep passwd'

ansible all -m shell -a 'ls -l /etc | grep passwd'




ansible all -m copy -a 'content="Hello World" dest=/etc/motd'
ssh ansible1
Hello World
Last login: Wed Apr 23 22:05:47 2025 from 192.168.121.148
[ansible@ansible1 ~]$

ansible ansible1 -m service -a 'name=httpd enabled=no'

ansible-doc service


ansible ansible2.example.com -m command -a 'echo "Hello Word" > /tmp/testfile'
ansible all -m shell -a 'echo "Hello World from Shell" > /tmp/testfile'
ansible ansible2.example.com -m command -a 'ls -l /tmp/testfile'
ansible ansible3 -m command -a 'ls -l /tmp/testfile'
ansible all -m command -a 'ls -l /tmp/testfile'
ansible3 | CHANGED | rc=0 >>
-rw-r--r--. 1 root root 23 Apr 24 00:52 /tmp/testfile
ansible2.example.com | CHANGED | rc=0 >>
-rw-r--r--. 1 root root 23 Apr 24 00:52 /tmp/testfile
ansible1.example.com | CHANGED | rc=0 >>
-rw-r--r--. 1 root root 23 Apr 24 00:52 /tmp/testfile

yum || apt

community.general.shutdown
# Using the built-in module:
ansible all -m community.general.shutdown -a "delay=1 msg='Shutdown initiated by Ansible'"


 ansible-doc -l | grep openst



```


و انت بتعمل Run لـ Ansible Playbook بالكوماند ansible-playbook ، فيه أوبشن اسمه --step  
ده بيسألك قبل ما ينفذ كل تاسك و بيكون عندك 3 اختيارات للإجابه  
y(es)  
هيخليه ينفذ التاسك عادي و هيسألك تاني في التاسك اللي بعدها  
n(o)  
هيعمل skip للتاسك دي و هيسألك عن اللي بعدها  
c(continue)  
هيعمل skip للـ step mode اصلا و هيكمل running عادي كأنك مش حاطط الأوبشن ده من الأساس  
مثال  
ansible-playbook main.yaml --limit webserver1 --user=ahmed -k -K --step


```bash
vim play1.yaml

---
- name: This my first play
  hosts: ansible1
  tasks:
    - name: Create User Blue
      ansible.builtin.user:  
        name: blue      
        uid: 1010      


ansible-playbook play1.yaml --syntax-check  #chack syntax

ansible-playbook -C  play1.yaml     #chack check code can run simulator
ansible-playbook play1.yaml 
ansible-playbook -v play1.yaml 

```

##### playbook
install httpd and test
```bash
$ ansible web --list-hosts

 cat inventory
[web]
ansible1
[client]
ansible3

[db]
ansible2

[datacenter:children]  
web
db


$ ansible web --list-hosts


$ cat httpd.yaml
---
- name: Install httpd
  hosts:  web
  become: True
  tasks:
    - name: Install Requierd Packages
      yum:
        name:
          - httpd
          - firewalld
        state: present
    - name: Enable httpd service
      service:
        name: httpd
        enabled: yes
        state:  started
    - name: Enable firewalld service
      service:
        name: firewalld
        enabled: yes
        state:  started
    - name: Create Index
      copy:
        content:  "Welcome to my website"
        dest: /var/www/html/index.html
        mode: 0644
        setype: httpd_sys_content_t
    - name: Allow http port
      firewalld:
        service:  http
        permanent: yes
        state:  enabled
        immediate:  yes
- name: Test my website
  hosts:  client
  become: False
  tasks:
    - name: Chack website Availability
      uri:
        url:  http://ansible1
        status_code:  200
        return_content: yes
 
 
$ ansible-playbook httpd.yaml --syntax-check

$ ansible-playbook -v httpd.yaml

```



```bash

cat ./group_vars/all
group_name: general
user_name: yellow
cat ./host_vars/ansible2
group_name: specific
user_name: green
cat inventory
[web]
ansible1
[client]
ansible3

[db]
ansible2

[datacenter:children]
web
db





cat create_user.yaml
---
- name: Howto use variables
  hosts: all
  tasks:
    - name: show var values
      debug:
        msg: Group is {{ group_name }} and user is {{ user_name }}
    - name: Create group {{ group_name }}
      group:
        name: "{{ group_name }}"
        state: absent
    - name: Create User {{ user_name }}
      user:
        name: "{{ user_name }}"
        group: "{{ group_name }}"
        state: absent



ansible-playbook create_user.yaml --syntax-check

playbook: create_user.yaml
ansible-playbook -v create_user.yaml




var in playbook

---
- name: Howto use variables
  hosts: all
  vars:
    user_name: white
    group_name: colors
  tasks:
    - name: show var values
      debug:
        msg: Group is {{ group_name }} and user is {{ user_name }}
    - name: Create group {{ group_name }}
      group:
        name: "{{ group_name }}"
        state: absent
    - name: Create User {{ user_name }}
      user:
        name: "{{ user_name }}"
        group: "{{ group_name }}"
        state: absent



vim vars_files
user_name: black
group_name: colors
---
- name: Howto use variables
  hosts: all
  vars_files:
    - vars_files
  tasks:
    - name: show var values
      debug:
        msg: Group is {{ group_name }} and user is {{ user_name }}
    - name: Create group {{ group_name }}
      group:
        name: "{{ group_name }}"
        state: absent
    - name: Create User {{ user_name }}
      user:
        name: "{{ user_name }}"
        group: "{{ group_name }}"
        state: absent







creat == present
cat create_user.yaml
---
- name: Howto use variables
  hosts: all
  tasks:
    - name: show var values
      debug:
        msg: Group is {{ group_name }} and user is {{ user_name }}
    - name: Create group {{ group_name }}
      group:
        name: "{{ group_name }}"
        state: present
    - name: Create User {{ user_name }}
      user:
        name: "{{ user_name }}"
        group: "{{ group_name }}"
        state: present








ansible-playbook create_user.yaml --syntax-check
ansible-playbook -v create_user.yaml


ansible all -a "tail -n1 /etc/passwd"





```


#### Facts

```bash

ansible ansible1 -m setup | less
ansible ansible1 -m setup -a "filter=ansible_default_ipv4"
ansible1 | SUCCESS => {
    "ansible_facts": {
        "ansible_default_ipv4": {
            "address": "192.168.121.147",
            "alias": "ens160",
            "broadcast": "192.168.121.255",
            "gateway": "192.168.121.2",
            "interface": "ens160",
            "macaddress": "00:0c:29:24:5a:f2",
            "mtu": 1500,
            "netmask": "255.255.255.0",
            "network": "192.168.121.0",
            "prefix": "24",
            "type": "ether"
        },
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false
}

 cat fact.yaml
---
- name: Ansible Facts Playbook
  hosts: ansible1,ansible2
  tasks:
    - name: Display ansible fact
      debug:
        var:  ansible_hostname

ansible-playbook fact.yaml --syntax-check
ansible-playbook -v fact.yaml

oldfact.yaml
---
- name: Ansible Facts Playbook
  hosts: ansible1,ansible2
  tasks:
    - name: Display ansible fact in old way
      debug:
        var:  ansible_default_ipv4.address
    - name: Display ansible fact in new way
      debug:
        var:  ansible_facts['default_ipv4']['address']

oldfact2.yaml
---
- name: Ansible Facts Playbook
  hosts: ansible1,ansible2
  tasks:
    - name: Display ansible fact in old way (still valid but less preferred)
      debug:
        var: ansible_default_ipv4.address # لاحظ: لا توجد مسافة بادئة إضافية هنا

    - name: Display multiple ansible facts using msg
      debug:
        msg: "Default IPv4 Address: {{ ansible_facts['default_ipv4']['address'] }} on Interface: {{ ansible_facts['default_ipv4']['interface'] }}"

    # طريقة بديلة باستخدام var لعرض الكائن الأب بأكمله
    - name: Display the whole default_ipv4 fact structure
      debug:
        var: ansible_facts['default_ipv4']


```




```

---
- name: Extract Specific Mount Info
  hosts: ansible1
  gather_facts: true
  tasks:
    - name: Get block info for /boot mount point
      debug:
        # Use direct fact name inside Jinja, same as in 'when'
        msg: >
          Mount: /boot,
          Block Available: {{ (ansible_mounts | selectattr('mount', 'equalto', '/boot') | first).block_available }},
          Block Size: {{ (ansible_mounts | selectattr('mount', 'equalto', '/boot') | first).block_size }}
      # Use direct fact name in 'when' (already correct)
      when: ansible_mounts | selectattr('mount', 'equalto', '/boot') | list | length > 0
```


###### Custom Facts and Magic Variables
```bash


cat type.fact
[server_type]
server = web_server
location = Location




 cat local_fact.yaml
---
- name: Create cutome fact
  hosts: all
  tasks:
    - name: Create directory for ansible custom facts
      file:
        state: directory
        recurse: yes
        path: /etc/ansible/facts.d
    - name: Copy facts file to nodes
      copy:
        src: type.fact
        dest: /etc/ansible/facts.d/type.fact


 ansible-playbook -v local_fact.yaml













 cat gather_local_fact.yaml
- name: Gather Local Facts
  hosts:  ansible1
  tasks:
    - name: Display local facts
      debug:
       # var: ansible_local.type.server_type
        var: ansible_local['type']

ansible-playbook -v gather_local_fact.yaml
Using /home/ansible/project/project1/ansible.cfg as config file

PLAY [Gather Local Facts] **************************************************************************************

TASK [Gathering Facts] *****************************************************************************************
ok: [ansible1]

TASK [Display local facts] *************************************************************************************
ok: [ansible1] => {
    "ansible_local['type']": {
        "server_type": {
            "location": "Location",
            "server": "web_server"
        }
    }
}

PLAY RECAP *****************************************************************************************************
ansible1                   : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0








```

###### vult
```bash

ansible-vault create password.yml
New Vault password:
Confirm New Vault password:


cat  password.yml
$ANSIBLE_VAULT;1.1;AES256
38356433656634336663383265643530303139373138646537613963366463333536653762336636
3665613965643637646331346561303664356431326365350a353766323233323838363235616438
31353661626263393534623462613039663236343662363633363462636334623737383338616365
6631383361646231310a646666356166303733316266383064366364326232343564646233656234
61613638653032396335363266313165633961386635336331336337333830643537


ansible-vault view password.yml
Vault password:
user_password: "p@ssw0rd"

الافضل عمل هاش للبسورد بطريقة دى 
علشان ميظهرش الخطى ده وكمان السكيورتى لان لو فتحنا etc/shadow هنلاقى كلمة السر زى ماهيا مش معموال لها هاش
ansible-playbook --vault-password-file vault_password user.yaml

PLAY [Example of Ansible vaulte] *******************************************************************************

TASK [Create new user] *****************************************************************************************
[WARNING]: The input password appears not to have been hashed. The 'password' argument must be encrypted for
this module to work properly.
changed: [ansible3]

PLAY RECAP *****************************************************************************************************
ansible3                   : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0



علاشان كدة ممكن نستخدم الطريقة دى
python -c 'import crypt; print(crypt.crypt("p@ssw0rd", crypt.mksalt(crypt.METHOD_SHA512)))'
ansible-vault view password.yml
user_password: $6$OJai.gMb7M.z7sbR$kH3M/fdKbW9QVtHjcB0LHYmpLEcHL93SH.UVDIzeb4ofPJsA2ekvXobGz/.GnTmdRjaTQhn0wLNZzu41rTteD0



 tree
.
├── ansible.cfg
├── gather_local_fact.yaml
├── inventory
├── local_fact.yaml
├── protacted.yml
├── type.fact
├── user.yaml
└── var
    ├── password.yml
    └── users.yaml
    
cat ./var/users.yaml
user_name: orange



 cat user.yaml
- name: Example of Ansible vaulte
  hosts:  ansible3
  gather_facts: no
  vars_files:
    - var/users.yaml
    - var/password.yml
  tasks:
    - name: Create new user
      user:
        name: "{{ user_name }}"
        password: "{{ user_password }}"

ansible-playbook user.yaml--list-hosts

ansible-playbook --ask-vault-pass user.yaml --syntax-check
 
ansible-playbook --vault-id @prompt user.yaml --syntax-check






```



```bash

ansible-playbook playbook.yml --check --diff > change_report.log
ansible-playbook playbook.yml --syntax-check  
ansible-playbook playbook.yml --check



name: Ansible Playbook Validation

on:  
  push:  
    branches:  
      - main  
  pull_request:  
    branches:  
      - mainjobs:  
  lint-and-validate:  
    runs-on: ubuntu-latest  
    steps:  
      - name: Check out repository  
        uses: actions/checkout@v3      - name: Set up Python  
        uses: actions/setup-python@v4  
        with:  
          python-version: '3.x'      - name: Install Ansible and ansible-lint  
        run: |  
          pip install ansible ansible-lint      - name: Lint Playbook  
        run: ansible-lint playbook.yml      - name: Perform Syntax Check  
        run: ansible-playbook playbook.yml --syntax-check      - name: Dry Run Playbook  
        run: ansible-playbook playbook.yml --check

```



```
---  
# Playbook to set up a web server environment for hosting applications.  
# Installs and configures NGINX, sets up basic security settings, and ensures the service is enabled.
# Playbook: web_server_setup.yaml  
# Version: 1.3  
# Last Updated: 2024-12-10  
# Author: Kaito

- name: Install NGINX web server  
ansible.builtin.dnf:  
name: nginx  
state: present
- name: Start and enable NGINX service  
  systemd:  
    name: nginx  
    state: started  
    enabled: true
- name: Add firewall rule for NGINX     
  firewalld:       
    service: http       
    permanent: true       
    state: enabled     
# This ensures NGINX can handle HTTP traffic on port 80.
# Expected outcome: NGINX service is running and starts automatically on boot.
```



```bash
install_repos.yaml
---
- name: Install EPEL and RPM Fusion Repositories
  hosts: all
  become: yes
  vars:
    repo_rpms:
      - https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
      - https://dl.fedoraproject.org/pub/epel/epel-next-release-latest-9.noarch.rpm
      - https://mirrors.rpmfusion.org/free/el/rpmfusion-free-release-9.noarch.rpm
      - https://mirrors.rpmfusion.org/nonfree/el/rpmfusion-nonfree-release-9.noarch.rpm

  tasks:
    - name: Install all repository RPMs together
      ansible.builtin.dnf:
        name: "{{ repo_rpms }}"
        state: present
        disable_gpg_check: yes


ansible all -m shell -a "yum repolist"



```

###### loop
https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#standard-loops

```
 cat loop_simple_vars.yml
numbers:
  - one
  - two
  - three

cat loop_sample_example.yaml
- name: Use lopp
  hosts: localhost
  gather_facts: no
  vars_files:
    - loop_simple_vars.yml
  tasks:
    - name: Display output of list
      debug:
        var: item
      loop: "{{ numbers }}"




 cat loop_dic_playbook.yaml
- name: Use lopp
  hosts: localhost
  gather_facts: no
  vars_files:
    - loop_dict_vars.yml
  tasks:
    - name: Display output of list
      debug:
        msg: this is user "{{ item.username }}" and group "{{ item.groups }}"
      loop: "{{ users }}"



 cat loop_dict_vars.yml
users:
  - username: user1
    groups: group1
  - username: user2
    groups: group2


```



###### Conditionals
https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_conditionals.html
```bash
ansible ansible1 -m setup | less
ansible ansible1 -m setup -a "filter=ansible_default_ipv4"

-C, --check          
-D, --diff

ansible-playbook playbook.yml -C -D
or 
ansible-playbook playbook.yml --check --diff



cat conditi-test.yaml
- name: Install apache
  hosts:  web
  tasks:
    - name: Install httpd on CentOs
      yum:
        name: httpd
        state: present
      when: ansible_facts['distribution'] ==  "CentOS"
    - name: Install apache on Ubuntu
      apt:
        name: apache
        state: present
      when: ansible_facts
      when: ansible_facts['distribution'] ==  "Ubuntu"






```


###### prompts-and-runtime-variable
https://linuxconfig.org/introduction-to-ansible-prompts-and-runtime-variables



###### Handlers
https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_handlers.html#handlers
https://docs.ansible.com/ansible/latest/collections/ansible/builtin/meta_module.html#meta-module




```bash

cat httpd.j2  #fileconfg httpd
 
cat handler.yaml
---
- name: Verify apache installation
  hosts: web
  vars:
    http_port: 8080
  tasks:
    - name: Ensure apache is at the latest version
      ansible.builtin.yum:
        name: httpd
        state: latest
    - name: Write the apache config file
      ansible.builtin.template:
        src: /httpd.j2
        dest: /etc/httpd.conf
      notify:
        - Restart apache
    - name: Ensure apache is running
      ansible.builtin.service:
        name: httpd
        state: started
  handlers:
    - name: Restart apache
      ansible.builtin.service:
        name: httpd
        state: restarted


ansible-playbook -v handler.yaml -C
```



###### Error-Handling
https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_blocks.html#error-handling
https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_error_handling.html


```bash

 cat hosts.j2
{% for host in groups['all'] %}
{{ hostvars[host]['ansible_facts']['default_ipv4']['address']}} {{ hostvars[host]['ansible_facts']['hostname'] }} {{hostvars[host]['ansible_facts']['fqdn'] }}
{% endfor %}





 cat MaFiMudul.yml
---
- name: Playing with file module # Removed $$
  hosts: all # Removed $
  vars: # Removed $
    hosts_file: /etc/hosts # Removed $
  tasks: # Removed $
    - name: Delete old host file and generate new one # Removed $, fixed typos
      block: # Removed $
        - name: Delete existing host file # Removed $
          ansible.builtin.file: # Best practice to use FQCN (Fully Qualified Collection Name) - Removed $
            path: "{{ hosts_file }}" # Removed $
            state: absent # Removed $

        - name: Generate new "{{ hosts_file }}" # Corrected indentation, removed $, fixed typo
          ansible.builtin.template: # Corrected indentation, FQCN - Removed $
            src: hosts.j2 # Corrected indentation - Removed $
            dest: "{{ hosts_file }}" # Corrected indentation - Removed $
            owner: root # Corrected indentation - Removed $
            group: root # Corrected indentation - Removed $
            mode: '0644'  # Corrected indentation, added quotes - Removed $
            setype: net_conf_t # Corrected indentation - Removed $ # Assuming 'setype' is correct for your environment (SELinux context)
      when: ansible_hostname in groups['dc2'] # Removed 
```

```bash
tree
.
├── ansible.cfg
├── fileServer.yml
├── hosts.j2
├── inventory
├── MaFiMudul.yml
├── temp
│   └── ftp_data
│       ├── another_file
│       └── testfile.txt
├── vsftpd_conf.j2
└── vsftpd_vars.yml

 cat vsftpd_conf.j2
listen=YES
anonymous_enable={{ anonymous_enable }}
local_enable={{ local_enable }}
write_enable={{ write_enable }}
local_umask=022
connect_from_port_20=YES
pam_service_name=vsftpd

cat vsftpd_vars.yml
package: vsftpd
service: vsftpd
firewall_service: ftp
ftp_dir: /var/ftp/pub/
src_dir: temp/ftp_data/  # Ensure this relative path is correct for your setup
config_file: /etc/vsftpd/vsftpd.conf # Corrected typo: fite -> file
### Configuration file related vars
anonymous_enable: YES # Corrected typo: enabte -> enable
local_enable: YES
write_enable: YES[



 cat fileServer.yml
---
- name: Build your file server
  hosts: file_server
  vars_files:
    - vsftpd_vars.yml
  tasks:
    - name: Install vsftpd
      ansible.builtin.yum:
        name: "{{ package }}"
        state: latest

    # Configure BEFORE starting/enabling
    - name: Configure {{ service }}
      ansible.builtin.template:
        src: vsftpd_conf.j2       # Make sure this has listen=NO (or YES)
        dest: "{{ config_file }}" # Make sure var name is correct
        owner: root
        group: root
        mode: '0644'
      notify: restart vsftpd # Notify still useful for subsequent changes

    # Start/Enable AFTER configuration is applied
    - name: Enable and start service vsftpd
      ansible.builtin.service:
        name: "{{ service }}"
        state: started
        enabled: yes

    - name: Allow port in firewall
      ansible.posix.firewalld:
        service: "{{ firewall_service }}"
        immediate: yes
        permanent: yes
        state: enabled

    - name: Copy file to {{ ftp_dir }}
      ansible.builtin.copy:
        src: "{{ src_dir }}" # Ensure this exists on control node
        dest: "{{ ftp_dir }}"
        owner: ansible
        group: ansible
        mode: '0664'
        setype: public_content_t

  handlers:
    - name: restart vsftpd
      ansible.builtin.service:
        name: "{{ service }}"
        state: restarted



ansible-playbook fileServer.yml --syntax-check
 ansible-playbook fileServer.yml --list-host

playbook: fileServer.yml

  play #1 (file_server): Build your file server TAGS: []
    pattern: ['file_server']
    hosts (1):
      ansible1

 ansible-playbook -v fileServer.yml


lftp ansible1
lftp ansible1:~> ls
drwxr-xr-x    2 0        0              46 Apr 29 21:13 pub
lftp ansible1:/> cd pub/
lftp ansible1:/pub> ls
-rw-rw-r--    1 1001     1001            0 Apr 29 21:13 another_file
-rw-rw-r--    1 1001     1001           28 Apr 29 21:13 testfile.txt
lftp ansible1:/pub> exit

```

#### Ansible Galaxy
https://galaxy.ansible.com/ui/standalone/roles/azure/azure_modules/versions/
https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html
https://docs.ansible.com/ansible/latest/galaxy/user_guide.html
https://github.com/Azure/azure_modules
```bash

 ansible-galaxy role -h

ansible-galaxy search nginx --author geerlingguy

 ansible-galaxy info geerlingguy.nginx

 ansible-config  dump | grep -i galaxy

ansible-config  dump | grep -i roles
 ansible-galaxy list



ls /usr/share/ansible/roles/   #roles redhat
 cd /usr/share/ansible/roles/rhel-system-roles.
```

#### ansible roles
https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html
https://docs.ansible.com/ecosystem.html
```

```



```

---
- name: Example
  hosts: all
  tasks:
    - name: Install apache on CentOS server
      yum:
        name: httpd
        state: latest
      when: (ansible_fact.distribution== "CentOS") and (ansible_hostname in groups['web'])
    - name: Install apache on Ubuntu server
      apt:
        name: apache2
        state: latest
      when: (ansible_fact.distribution== "Ubuntu") and (ansible_hostname in groups['web'])
    - name: verivfy web server is installed
      debug:
        var: ansible_fact.pakages.NetworkManager[0].version
      when: ansible_host == "ansible1"
  
```


#### last
https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_strategies.html
https://docs.ansible.com/ansible/latest/reference_appendices/faq.html#how-do-i-generate-encrypted-passwords-for-the-user-module






#### Alerts
####  Send Discord messages
```
- name: Send a message to the Discord channel
  community.general.discord:
    webhook_id: "00000"
    webhook_token: "XXXYYY"
    content: "This is a message from ansible"

- name: Send a message to the Discord channel with specific username and avatar
  community.general.discord:
    webhook_id: "00000"
    webhook_token: "XXXYYY"
    content: "This is a message from ansible"
    username: Ansible
    avatar_url: "https://docs.ansible.com/ansible/latest/_static/images/logo_invert.png"

- name: Send a embedded message to the Discord channel
  community.general.discord:
    webhook_id: "00000"
    webhook_token: "XXXYYY"
    embeds:
      - title: "Embedded message"
        description: "This is an embedded message"
        footer:
          text: "Author: Ansible"
        image:
          url: "https://docs.ansible.com/ansible/latest/_static/images/logo_invert.png"

- name: Send two embedded messages
  community.general.discord:
    webhook_id: "00000"
    webhook_token: "XXXYYY"
    embeds:
      - title: "First message"
        description: "This is my first embedded message"
        footer:
          text: "Author: Ansible"
        image:
          url: "https://docs.ansible.com/ansible/latest/_static/images/logo_invert.png"
      - title: "Second message"
        description: "This is my first second message"
        footer:
          text: "Author: Ansible"
          icon_url: "https://docs.ansible.com/ansible/latest/_static/images/logo_invert.png"
        fields:
          - name: "Field 1"
            value: "Value of my first field"
          - name: "Field 2"
            value: "Value of my second field"
        timestamp: "{{ ansible_date_time.iso8601 }}"
```
#### Send notify to Telegram

https://galaxy.semaphoreui.com/views/jokerwrld999/ansible-role-telegram-alerts/overview

```
- name: Send notify to Telegram
  community.general.telegram:
    token: '9999999:XXXXXXXXXXXXXXXXXXXXXXX'
    api_args:
      chat_id: 000000
      parse_mode: "markdown"
      text: "Your precious application has been deployed: https://example.com"
      disable_web_page_preview: true
      disable_notification: true

- name: Forward message to someone
  community.general.telegram:
    token: '9999999:XXXXXXXXXXXXXXXXXXXXXXX'
    api_method: forwardMessage
    api_args:
      chat_id: 000000
      from_chat_id: 111111
      disable_notification: true
      message_id: '{{ saved_msg_id }}'
```

####  Ansible – Calling REST APIs and Sending to Webhooks
##### A Basic Scenario

Lets take a basic scenario where we want to query a REST API, and if it contains a certain value, we want to send an alert to a remote Webhook. We **could** simply say do this if the task fails, but this may not be what we want to happen, and the criteria that is needed to be considered a failure might not be triggered, this would also preclude us from performing a number of API queries in a single playbook.

In this example we’re going to send an alert when the remote API returns a _Status_ value of anything other than **Operational**. We’ll also assume that the API is accepting authentication by means of a Bearer Token.

##### Performing the API Query

Let’s take a look at the _Task_ below which we can use to query the API using the Ansible **uri** Module:
```
- name: Perform API GET to return the status of remote service
  uri:
    url: "{{ your_api_endpoint }}"
    method: GET
    return_content: yes
    body_format: json
    status_code: 200
    headers:
      Authorization: "Bearer {{ your_bearer_token }}"
  register: results_of_get
  no_log: True
```

A few things going on here:

1. **Line 7**: We’re defining the return code as **HTTP 200**, any other return code will result in failure
2. **Lines 8-9**: We’re defining that a **Bearer Token** be sent as a header
3. **Line 10**: The results of the GET will be saved in a new variable called **results_of_get**
4. **Line 11**: We’re specifying that no log be displayed in the console, as the token will be displayed in the clear and as this is secret data we probably don’t want it to be displayed

##### Manipulating the Returned Data and Sending to a Webhook

Now that we have queried the endpoint, we need to determine what we actually want to do with it, and if we want to send anything out to our Webhook or not. Below is the task that we’ll use for this. Once again we will be using the **uri** module:
```
- name: Read the return data and POST to webhook if anything other than Opeartional is found in Status
  uri:
    url: "{{ your_incoming_webhook }}"
    method: POST
    return_content: yes
    body: '{ YOUR BODY HERE }'
    body_format: json
  when: 
    - results_of_get.json.value[0].Status != 'Operational'
```

##### A Practical Example – Microsoft Teams Webhooks

Now that we’ve seen some snippets, let’s look at a functional example of a full playbook, sending to a Microsoft Teams Webhook:

---
```
---
- name: GET_and_POST
  hosts: localhost
  gather_facts: false
  any_errors_fatal: true
  vars:
    your_api_endpoint: "https://some.imaginaryorg.com/fictional/endpoint"
    your_incoming_webhook: "https://outlook.office.com/webhook/<TEAMS_WEBHOOK_UID>"
  
  tasks:
  - name: Perform API GET to return the status of remote service
    uri:
      url: "{{ your_api_endpoint }}"
      method: GET
      return_content: yes
      body_format: json
      status_code: 200
      headers:
        Authorization: "Bearer {{ your_bearer_token }}"
    register: results_of_get
    no_log: True
    
  - name: Read the return data and POST to webhook if anything other than Opeartional is found in Status
    uri:
      url: "{{ your_incoming_webhook }}"
      method: POST
      return_content: yes
      body: '{"@type":"MessageCard","@context":"http://schema.org/extensions","themeColor":"0076D7","summary":"FAILURE"}'
      body_format: json
    when: 
      - results_of_get.json.value[0].Status != 'Operational'
```

This will ensure that a Message Card notification is generated in Teams with the summary **FAILURE** when the Status is returned with anything other than **Operational**. This is a very basic message which can be spruced up significantly, a fantastic utility for designing this payload exists at [MessageCard Playground](https://messagecardplayground.azurewebsites.net/).

This can then be linked to a schedule either with **cron** or if using **Ansible Tower**, via the native _beat_ scheduler.