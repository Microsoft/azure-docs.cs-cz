---
title: Nasazení aplikací do škálovací sady virtuálních počítačů v Azure pomocí Ansible
description: Zjistěte, jak použít Ansible k konfigurace škálovací sady virtuálních počítačů a nasazení aplikace na virtuální počítač škálovací sady v Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, playbooku, virtuálních počítačů, škálovací sadu virtuálních počítačů, vmss
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: b9c8058606e13c0db4908530e98cddb69d2caf50
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008850"
---
# <a name="deploy-applications-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Nasazení aplikací do škálovací sady virtuálních počítačů v Azure pomocí Ansible
Ansible umožňuje automatizovat nasazení a konfiguraci prostředků ve vašem prostředí. Ansible můžete použít k nasazení aplikací do Azure. V tomto článku se dozvíte, jak nasadit aplikace v Javě do škálovací sady virtuálních počítačů Azure (VMSS).  

## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure** – Pokud nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) předtím, než začnete.
- **Konfigurace Ansible** - [Azure vytvořit přihlašovací údaje a konfigurace Ansible](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Ansible a moduly sady Azure Python SDK** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)
- **Škálovací sada virtuálních počítačů** – Pokud ještě nemáte škálovací sadu virtuálních počítačů nastavena, můžete [vytvoření virtuálního počítače škálovací sady pomocí Ansible](ansible-create-configure-vmss.md). 
- **Git** - [git](https://git-scm.com) se používá ke stahování ukázku Java použité v tomto kurzu.
- **Java SE Development Kit (JDK)** – The JDK slouží k vytvoření ukázkového projektu jazyka Java.
- **Nástroje Apache Maven sestavení** – [nástroje Apache Maven sestavení](https://maven.apache.org/download.cgi) slouží k vytvoření ukázkového projektu jazyka Java.

> [!Note]
> Ansible 2.6 je potřeba spustit následující playbooky ukázka v tomto kurzu. 

## <a name="get-host-information"></a>Získání informací o hostiteli

Tato část ukazuje, jak použít Ansible k načtení informací o hostiteli pro skupinu virtuálních počítačů Azure. Níže je playbook Ansible vzorku. Kód získá veřejné IP adresy a nástroje pro vyrovnávání zatížení v rámci zadané skupině prostředků a vytvoří skupinu hostitelů s názvem **saclesethosts** v inventáři. 

Uložte následující ukázkové scénáře jako `get-hosts-tasks.yml`: 

  ```yaml
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

## <a name="prepare-an-application-for-deployment"></a>Příprava aplikace pro nasazení  

V této části pomocí gitu naklonujte ukázkový projekt v Javě z Githubu a sestavte projekt. Uložte následující playbook jako `app.yml`:

  ```yaml
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

Pomocí následujícího příkazu spusťte playbook Ansible vzorku:

  ```bash
  ansible-playbook app.yml
  ```

Ve výstupu z příkazu ansible playbook nezobrazí si podobná následující, kde uvidíte, že vytvořeno ukázkovou aplikaci naklonovali z Githubu:

  ```bash
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

## <a name="deploy-the-application-to-vmss"></a>Nasaďte aplikaci na VMSS

V následující části v Ansible playbook nainstaluje prostředí JRE (Java Runtime Environment) pro skupinu hostitelů s názvem **saclesethosts**a nasadí aplikaci v Javě do skupiny hostitelů s názvem **saclesethosts**: 

(Změnit `admin_password` vlastní heslo.)

  ```yaml
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

Můžete uložit předchozí playbook Ansible ukázka jako `vmss-setup-deploy.yml`, nebo [Stáhnout příručku celé ukázkové](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss). 

Použít ssh typ připojení s hesly, je nutné nainstalovat sshpass program. 
  - Ubunto 16.04, spusťte příkaz `apt-get install sshpass`.
  - CentOS 7.4, spusťte příkaz `yum install sshpass`.

Může se zobrazit chyba typu **pomocí hesla SSH místo klíče není možné, protože je povolená kontrola klíč hostitele a sshpass to nepodporuje.  Otisk prstu tohoto hostitele přidáte do vašeho souboru known_hosts ke správě tohoto hostitele.** Pokud se zobrazí tato chyba, můžete zakázat kontrolu tak, že přidáte následující řádek buď klíč hostitele `/etc/ansible/ansible.cfg` souboru nebo `~/.ansible.cfg` souboru:
  ```bash
  [defaults]
  host_key_checking = False
  ```

Spuštění playbooku pomocí následujícího příkazu:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Výstup z příkazu ansible playbook označuje, že byla nainstalována ukázkové aplikace v Javě do skupiny hostitelů škálovací sadu virtuálních počítačů:

  ```bash
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

Blahopřání! Aplikace je nyní spuštěna v Azure. Teď můžete přejít na adresu URL z nástroje pro vyrovnávání zatížení pro škálovací sadu virtuálních počítačů:

![Aplikace Java spuštěná ve virtuálním počítači škálovací sady v Azure.](media/ansible-deploy-app-vmss/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"] 
> [Playbook Ansible vzorku pro VMSS](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)