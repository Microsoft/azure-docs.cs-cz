---
title: Kurz – nasazení aplikací do škálovacích sad virtuálních strojů v Azure pomocí Ansible
description: Naučte se používat Ansible ke konfiguraci škálovacích sad virtuálních strojů Azure a nasazovat aplikace na škálovací sadě.
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.date: 01/13/2020
ms.openlocfilehash: d638ae3f0c33734b42ef5456772fcd2bc62e35a4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75940857"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Kurz: Nasazení aplikací do škálovacích sad virtuálních strojů v Azure pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Načtení informací o hostiteli pro skupinu virtuálních počítačů Azure
> * Klonování a sestavení ukázkové aplikace
> * Instalace jre (Java Runtime Environment) na škálovací sadu
> * Nasazení aplikace Java do škálovací sady

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **git** - [git](https://git-scm.com) se používá ke stažení ukázky Java použité v tomto kurzu.
- **Java SE Development Kit (JDK)** – Sada [JDK](https://aka.ms/azure-jdks) se použije k sestavení ukázkového projektu v Javě.
- **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) se používá k sestavení ukázkového projektu Java.

## <a name="get-host-information"></a>Získání informací o hostiteli

Kód playbooku v této části načte informace o hostiteli pro skupinu virtuálních počítačů. Kód získá veřejné IP adresy a vyrovnávání zatížení v rámci zadané `scalesethosts` skupiny prostředků a vytvoří skupinu hostitelů pojmenovanou ve skladu.

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
      hostname: "{{ output_ip_address.publicipaddresses[0].ip_address }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.publicipaddresses[0].ip_address }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_info.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Příprava aplikace na nasazení

Playbook kód v této `git` části používá klonovat ukázkový projekt Java z GitHubu a vytvoří projekt. 

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

Po spuštění playbooku se zobrazí výstup podobný následujícím výsledkům:

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

Kód playbooku v této části slouží k:

* Instalace jre na hostitelskou skupinu s názvem`saclesethosts`
* Nasazení aplikace Java do skupiny hostitelů s názvem`saclesethosts`

Ukázkový playbook lze získat dvěma způsoby:

* [Stáhněte si playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) `vmss-setup-deploy.yml`a uložte jej do .
* Vytvořte nový `vmss-setup-deploy.yml` soubor s názvem a zkopírujte do něj následující obsah:

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

Před spuštěním playbooku se podívejte na následující poznámky:

* V `vars` části nahraďte `{{ admin_password }}` zástupný symbol vlastním heslem.
* Chcete-li použít typ připojení ssh s hesly, nainstalujte program sshpass:

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    Centos:

    ```bash
    yum install sshpass
    ```

* V některých prostředích se může zobrazit chyba o použití hesla SSH namísto klíče. Pokud se tato chyba zobrazí, můžete zakázat kontrolu klíčů `/etc/ansible/ansible.cfg` hostitele `~/.ansible.cfg`přidáním následujícího řádku do nebo :

    ```bash
    [defaults]
    host_key_checking = False
    ```

Pomocí následujícího příkazu spusťte playbook:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Výstup ze spuštění příkazu ansible-playbook označuje, že ukázková aplikace Java byla nainstalována do hostitelské skupiny škálovací sady:

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

## <a name="verify-the-results"></a>Ověření výsledků

Ověřte výsledky své práce tak, že přejdete na adresu URL provykladač zatížení pro váhovou sadu:

![Java aplikace spuštěná ve škálovací sadě v Azure.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Škálovací sady virtuálních strojů v Azure s automatickým škálování pomocí ansible](./ansible-auto-scale-vmss.md)