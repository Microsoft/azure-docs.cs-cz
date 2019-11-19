---
title: Kurz – nasazení aplikací do služby Virtual Machine Scale Sets v Azure pomocí Ansible
description: Naučte se používat Ansible ke konfiguraci služby Azure Virtual Machine Scale Sets a nasazení aplikace v sadě škálování.
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 7c54790a3d988341caa65bdf0ce33c240f0580fb
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156386"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Kurz: nasazení aplikací do služby Virtual Machine Scale Sets v Azure pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Načíst informace o hostiteli pro skupinu virtuálních počítačů Azure
> * Klonování a sestavení ukázkové aplikace
> * Instalace JRE (Java Runtime Environment) do sady škálování
> * Nasazení aplikace Java do sady škálování

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **git** - [git](https://git-scm.com) se použije ke stažení ukázky v Javě použité v tomto kurzu.
- **Java SE Development Kit (JDK)** – Sada [JDK](https://aka.ms/azure-jdks) se použije k sestavení ukázkového projektu v Javě.
- **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) se používá k vytvoření ukázkového projektu Java.

## <a name="get-host-information"></a>Získání informací o hostiteli

Kód PlayBook v této části načte informace o hostiteli pro skupinu virtuálních počítačů. Kód získá veřejné IP adresy a nástroj pro vyrovnávání zatížení v rámci zadané skupiny prostředků a vytvoří skupinu hostitelů s názvem `scalesethosts` v inventáři.

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

Kód PlayBook v této části používá `git` ke klonování ukázkového projektu Java z GitHubu a sestavení projektu. 

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

Po spuštění PlayBook se zobrazí výstup podobný následujícímu výsledku:

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

## <a name="deploy-the-application-to-a-scale-set"></a>Nasazení aplikace do sady škálování

PlayBook kód v této části se používá pro:

* Nainstalujte JRE do skupiny hostitelů s názvem `saclesethosts`
* Nasaďte aplikaci Java do skupiny hostitelů s názvem `saclesethosts`

Existují dva způsoby, jak získat ukázkovou PlayBook:

* [Stáhněte si PlayBook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) a uložte ho do `vmss-setup-deploy.yml`.
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

Před spuštěním PlayBook se podívejte na následující poznámky:

* V části `vars` nahraďte zástupný text `{{ admin_password }}` vlastním heslem.
* Chcete-li použít typ připojení SSH s hesly, nainstalujte program sshpass:

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    CentOS

    ```bash
    yum install sshpass
    ```

* V některých prostředích se může při použití hesla SSH místo klíče zobrazit chyba. Pokud se zobrazí tato chyba, můžete zakázat kontrolu klíčů hostitele přidáním následujícího řádku do `/etc/ansible/ansible.cfg` nebo `~/.ansible.cfg`:

    ```bash
    [defaults]
    host_key_checking = False
    ```

Pomocí následujícího příkazu spusťte playbook:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Výstup spuštění příkazu Ansible-PlayBook indikuje, že ukázková aplikace Java je nainstalovaná do skupiny hostitelů sady škálování:

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

## <a name="verify-the-results"></a>Ověřit výsledky

Pomocí přechodu na adresu URL nástroje pro vyrovnávání zatížení pro vaši sadu škálování Ověřte výsledky práce:

![Aplikace Java spuštěná v sadě škálování v Azure.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: automatické škálování virtuálních počítačů ve službě Virtual Machine Scale Sets v Azure pomocí Ansible](./ansible-auto-scale-vmss.md)