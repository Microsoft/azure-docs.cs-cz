---
title: Nasazování aplikací do škálovacích sad virtuálních počítačů v Azure pomocí Ansible
description: Zjistěte, jak pomocí Ansible nakonfigurovat škálovací sadu virtuálních počítačů v Azure a nasadit do ní aplikace.
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/11/2018
ms.openlocfilehash: fc6b1484d43d0798f9ff70049017c8d360f027d4
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050400"
---
# <a name="deploy-applications-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Nasazování aplikací do škálovacích sad virtuálních počítačů v Azure pomocí Ansible
Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. Pomocí Ansible můžete nasadit své aplikace do Azure. V tomto článku se dozvíte, jak do škálovací sady virtuálních počítačů (VMSS) Azure nasadit aplikaci v Javě.  

## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure** – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **Škálovací sada virtuálních počítačů** – Pokud ještě škálovací sadu virtuálních počítačů nemáte, můžete si [vytvořit škálovací sadu virtuálních počítačů pomocí Ansible](ansible-create-configure-vmss.md). 
- **git** - [git](https://git-scm.com) se použije ke stažení ukázky v Javě použité v tomto kurzu.
- **Java SE Development Kit (JDK)** – Sada [JDK](https://aka.ms/azure-jdks) se použije k sestavení ukázkového projektu v Javě.
- **Nástroje pro sestavování Apache Maven** – [Nástroje pro sestavování Apache Maven](https://maven.apache.org/download.cgi) se použijí k sestavení ukázkového projektu v Javě.

> [!Note]
> Ke spuštění následujících ukázkových playbooků v tomto kurzu se vyžaduje Ansible 2.6. 

## <a name="get-host-information"></a>Získání informací o hostiteli

Tato část ukazuje, jak pomocí Ansible načíst informace o hostiteli skupiny virtuálních počítačů Azure. Níže je ukázkový playbook Ansible. Tento kód získá veřejné IP adresy a nástroj pro vyrovnávání zatížení v zadané skupině prostředků a v inventáři vytvoří skupinu hostitelů **scalesethosts**. 

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

V této části pomocí gitu naklonujete z GitHubu ukázkový projekt v Javě a sestavíte ho. Uložte následující ukázkový playbook jako `app.yml`:

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

Zobrazí se podobný výstup příkazu ansible-playbook jako v následujícím příkladu, kde vidíte, že příkaz sestavil ukázkovou aplikaci naklonovanou z GitHubu:

  ```Output
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Git Clone sample app] ***************************************************************************
  changed: [localhost]

  TASK [Build sample app] ***************************************************
  changed: [localhost]

  PLAY RECAP ***************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-vmss"></a>Nasazení aplikace do VMSS

Playbook Ansible v následující části nainstaluje Java Runtime Environment ve skupině hostitelů **scalesethosts** a nasadí aplikaci v Javě do skupiny hostitelů **scalesethosts**: 

(Hodnotu `admin_password` změňte na své heslo.)

  ```yml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      scaleset_name: myVMSS
      loadbalancer_name: myVMSSlb
      admin_username: azureuser
      admin_password: "your_password"
    tasks:   
    - include: get-hosts-tasks.yml

  - name: Install JRE on VMSS
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

Předchozí ukázkový playbook Ansible můžete uložit jako `vmss-setup-deploy.yml` nebo si můžete [stáhnout úplný ukázkový playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss). 

Pokud chcete použít typ připojení SSH s hesly, musíte nainstalovat program sshpass. 
  - V případě Ubuntu 16.04 spusťte příkaz `apt-get install sshpass`.
  - V případě CentOS 7.4 spusťte příkaz `yum install sshpass`.

Možná se zobrazí chyba jako **Není možné použít heslo SSH místo klíče, protože je povolená kontrola klíče hostitele a sshpass tuto funkci nepodporuje. Pokud chcete tohoto hostitele spravovat, přidejte otisk tohoto hostitele do souboru known_hosts.** Pokud se zobrazí tato chyba, můžete kontrolu klíče hostitele zakázat přidáním následujícího řádku do souboru `/etc/ansible/ansible.cfg` nebo `~/.ansible.cfg`:
  ```bash
  [defaults]
  host_key_checking = False
  ```

Pomocí následujícího příkazu spusťte playbook:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Výstup spuštění příkazu ansible-playbook značí, že se ukázková aplikace v Javě nainstalovala do skupiny hostitelů škálovací sady virtuálních počítačů:

  ```Output
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups] **********************************************
  ok: [localhost]

  TASK [Get loadbalancer info] ****************************************************************************
  ok: [localhost]

  TASK [Add all hosts] *****************************************************************************
  changed: [localhost] ...

  PLAY [Install JRE on VMSS] *****************************************************************************

  TASK [Gathering Facts] *****************************************************************************
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM] *****************************************************************************
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application] ********************************************************************
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP ************************************************************************************************
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

Blahopřejeme! Vaše aplikace je teď spuštěná v Azure. Teď můžete přejít na adresu URL nástroje pro vyrovnávání zatížení vaší škálovací sady virtuálních počítačů:

![Aplikace v Javě spuštěná ve škálovací sadě virtuálních počítačů v Azure](media/ansible-deploy-app-vmss/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"] 
> [Automaticky škálujte škálovací sadu virtuálních počítačů pomocí Ansible](https://docs.microsoft.com/azure/ansible/ansible-auto-scale-vmss)