# Ansible, ne (sasvim) obična automatizacija

## Šta pruža Ansible

### Automatizacija
-   Omogućava __predvidivost__ operacija
-   Eliminiše ručno _ponavljanje_ operacija
-   Omogućava __usitnjavanje__ problema
-   Ali _ne_ obavezno i pojednostavljuje rešavanje problema

### Playbook
-   Jednostavna sintaksa
-   Lista operacija koje se izvršavaju
-   Operacije su shell komande ili moduli
-   Opciono grupisane po rolama
-   Promenljive definisane na raznim nivoima
-   Handler operacije koje čekaju na neki događaj

### Push model
-   Sa kontrolne mašine (vaš laptop) se pokreću operacije nad nodovima
-   Nodovi se navode u inventaru, opciono grupisani
-   Bez agenta na nodovima – ssh i python su zahtevi
-   Autentifikacija SSH ključevima ili šifrom
-   Rezultat je odmah vidljiv u JSON formatu

### Moduli
-   Veliki broj (800+) gotovih modula
-   Mogu biti napisani u raznim jezicima
-   Idempotentnost je značajna osobina


## Kako radi Ansible

### Konfiguracija
```
$ cat ansible.cfg
[defaults]
inventory = hosts
remote_user = root
command_warnings = False
```

### Inventar:
```
$ cat hosts
[local]
localhost

[mday]
mns-mday-[1:2]
```
### Playbook
```
$ cat playbooks/01-hello.yml 
#!/usr/bin/env ansible-playbook
---
- name: Say hello
hosts: all
gather_facts: False
tasks:

- debug: msg="Hello World. My name is {{ inventory_hostname }}"
```

### Kako radi Ansible
```
$ playbooks/01-hello.yml -l mday

PLAY [Say hello]
********************************************************

TASK [debug]
************************************************************

ok: [mns-mday-1] => {
"msg": "Hello World. My name is mns-mday-1"
}

ok: [mns-mday-2] => {
"msg": "Hello World. My name is mns-mday-2"
}

PLAY RECAP
**************************************************************

mns-mday-1 : ok=1 changed=0 unreachable=0 failed=0
mns-mday-2 : ok=1 changed=0 unreachable=0 failed=0
```

## Ansible automatizacija
Dva izbora:
- By the book reusability
- Sam svoj majstor...

### Reusability primer

```
$ git clone https://github.com/ansible/ansible-examples.git
$ cd ansible-examples/wordpress-nginx_rhel7
$ cp ~/ansible.cfg ~/hosts .
$ ansible-playbook site.yml
```

### Sam svoj majstor
```
$ git clone https://github.com/mnspodrska/ansible-examples.git
```

 - __01-hello.yml__ - Da se upoznamo...
 - __02-install.yml__ - Malo da zapetljamo
 - __03-lineinfile.yml__ - Interaktivni search&replace linija fajla
 - __04-diff.yml__ - Prikazuje razlike između fajlova na udaljenim mašinama uz mogućnost promene sadržaja
 - __05-users.yml__ - Dodavanje SSH korisnika pomoću YAML dict-a
 - __06-email.yml__ - Korišćenje template-a za personalizovane mejlove
 - __07-stdout.yml__ - Demonstrira ružnu stranu Ansible prikaza stdout-a
 - __08-stdout-human.yml__ -  Na primeru prikaza stdout-a za ljude pokazuje kako je Ansible lako prilagoditi
 - __09-script.yml__ - Idempotencija je važna, ali ne i obavezna ili uvek potrebna… Nekada je potrebno izvršiti gotovu skriptu na većem broju nodova
 - __10-stateful-nginx-config.yml__
   - Instalira nginx
   - Priprema inicijalnu konfiguraciju i sadržaj lokalno na kontrolnoj mašini
   - Omogućava naknadnu izmenu inicijalne konfiguracije i sadržaja lokalno, uz upload na sve nodove i restart kad je potrebno
   - Pravi bekap izmenjene konfiguracije i briše stare bekape
 

