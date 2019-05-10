---
title: Kurz – nasazení aplikací do škálovací sady virtuálních počítačů v Azure pomocí Ansible | Dokumentace Microsoftu
description: Zjistěte, jak použít Ansible k konfigurace škálovací sady virtuálních počítačů Azure a nasazení aplikace ve škálovací sadě
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: a44fd06ace9b21122f5f4253ac7d9601b54e6b62
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231049"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Kurz: Nasazení aplikací do škálovací sady virtuálních počítačů v Azure pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Načíst informace o hostiteli pro skupinu virtuálních počítačů Azure
> * Klonovat a sestavení ukázkové aplikace
> * Instalace JRE (Java Runtime Environment) ve škálovací sadě
> * Nasazení aplikace v Javě do škálovací sady

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **git** - [git](https://git-scm.com) se použije ke stažení ukázky v Javě použité v tomto kurzu.
- **Java SE Development Kit (JDK)** – Sada [JDK](https://aka.ms/azure-jdks) se použije k sestavení ukázkového projektu v Javě.
- **Nástroje Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) slouží k vytvoření ukázkového projektu jazyka Java.

## <a name="get-host-information"></a>Získání informací o hostiteli

Playbook kód v této části načte informace o hostiteli pro skupinu virtuálních počítačů. Kód získá veřejné IP adresy a pro load balancer v zadané skupině prostředků a vytvoří skupinu hostitelů s názvem `scalesethosts` v inventáři.

Uložte následující ukázkový playbook jako `get-hosts-tasks.yml`:

  ```yml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_facts.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Příprava aplikace na nasazení

Playbook kód v této části používá `git` naklonujte ukázkový projekt v Javě z Githubu a sestaví projekt. 

Uložte následující ukázkový playbook jako `app.yml`:

  ```yml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks:
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

Pomocí následujícího příkazu spusťte ukázkový playbook Ansible:

  ```bash
  ansible-playbook app.yml
  ```

Po spuštění playbooku, se zobrazí výstup podobný následující výsledky:

  ```Output
  PLAY [localhost] 

  TASK [Gathering Facts] 
  ok: [localhost]

  TASK [Git Clone sample app] 
  changed: [localhost]

  TASK [Build sample app] 
  changed: [localhost]

  PLAY RECAP 
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-a-scale-set"></a>Nasazení aplikace do škálovací sady

Playbook kód v této části se používá pro:

* Nainstalovat prostředí JRE na skupinu hostitelů s názvem `saclesethosts`
* Nasazení aplikace v Javě do skupiny hostitelů s názvem `saclesethosts`

Existují dva způsoby, jak získat ukázkové scénáře:

* [Stáhnout příručku](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) a uložit ho. tím `vmss-setup-deploy.yml`.
* Vytvořte nový soubor s názvem `vmss-setup-deploy.yml` a zkopírujte do něj následující obsah:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    scaleset_name: myScaleSet
    loadbalancer_name: myScaleSetLb
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
  - include: get-hosts-tasks.yml

- name: Install JRE on a scale set
  hosts: scalesethosts
  become: yes
  vars:
    workspace: ~/src/helloworld
    admin_username: azureuser

  tasks:
  - name: Install JRE
    apt:
      name: default-jre
      update_cache: yes

  - name: Copy app to Azure VM
    copy:
      src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
      dest: "/home/{{ admin_username }}/helloworld.jar"
      force: yes
      mode: 0755

  - name: Start the application
    shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
    async: 5000
    poll: 0
```

Před spuštěním playbooku, viz následující poznámky:

* V `vars` části, nahraďte `{{ admin_password }}` zástupný text pomocí vlastního hesla.
* Použít ssh typ připojení s hesly, instalaci sshpass programu:

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    CentOS:

    ```bash
    yum install sshpass
    ```

* V některých prostředích může se zobrazit chyba o použití heslo SSH místo klíče. Pokud se zobrazí tato chyba, můžete zakázat kontrolu tak, že přidáte následující řádek, který klíč hostitele `/etc/ansible/ansible.cfg` nebo `~/.ansible.cfg`:

    ```bash
    [defaults]
    host_key_checking = False
    ```

Pomocí následujícího příkazu spusťte playbook:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Výstup z příkazu ansible playbook označuje, že byla nainstalována ukázkové aplikace v Javě do skupiny hostitelů škálovací sady:

  ```Output
  PLAY [localhost]

  TASK [Gathering Facts]
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups]
  ok: [localhost]

  TASK [Get loadbalancer info]
  ok: [localhost]

  TASK [Add all hosts]
  changed: [localhost] ...

  PLAY [Install JRE on scale set]

  TASK [Gathering Facts]
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM]
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application]
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

## <a name="verify-the-results"></a>Zkontrolujte výsledky

Zkontrolujte výsledky své práce tak, že přejdete na adresu URL z nástroje pro vyrovnávání zatížení pro svou škálovací sadu:

![Aplikace Java spuštěná ve škálovací sadě, v Azure.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Automatické škálování škálovací sady virtuálních počítačů v Azure pomocí Ansible](./ansible-auto-scale-vmss.md)