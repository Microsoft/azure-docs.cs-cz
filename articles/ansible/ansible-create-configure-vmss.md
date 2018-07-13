---
title: Vytvoření škálovací sady virtuálních počítačů v Azure pomocí Ansible
description: Zjistěte, jak použít Ansible k vytvoření a konfigurace virtuálního počítače škálovací sady v Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, playbooku, virtuálních počítačů, škálovací sadu virtuálních počítačů, vmss
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: 5f915f7b1b425a3bd6e5d62eb70bb3f633b7eda8
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009004"
---
# <a name="create-virtual-machine-scale-sets-in-azure-using-ansible"></a>Vytvoření škálovací sady virtuálních počítačů v Azure pomocí Ansible
Ansible umožňuje automatizovat nasazení a konfiguraci prostředků ve vašem prostředí. Ansible můžete použít ke správě škálovací sady virtuálních počítačů (VMSS) v Azure, stejně jako spravujete libovolné prostředky Azure. Tento článek ukazuje, jak použít Ansible k vytvoření a horizontální navýšení kapacity škálovací sady virtuálních počítačů. 

## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure** – Pokud nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) předtím, než začnete.
- **Konfigurace Ansible** - [Azure vytvořit přihlašovací údaje a konfigurace Ansible](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Ansible a moduly sady Azure Python SDK** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)

> [!Note]
> Ansible 2.6 je potřeba spustit následující playbooky ukázka v tomto kurzu. 

## <a name="create-a-vmss"></a>Vytvoření VMSS
Tato část představuje ukázkové playbook Ansible, který definuje následující prostředky:
- **Skupina prostředků** do kterého všechny vaše prostředky se nasadí
- **Virtuální síť** v adresní prostor 10.0.0.0/16
- **Podsíť** v rámci virtuální sítě
- **Veřejná IP adresa** tohoto wllows můžete získat přístup k prostředkům v síti Internet
- **Skupina zabezpečení sítě** , která řídí tok síťový provoz a škálovací sady virtuálních počítačů
- **Nástroj pro vyrovnávání zatížení** , který distribuuje provoz mezi sadu definovaných virtuálních počítačů pomocí pravidla nástroje pro vyrovnávání zatížení
- **Škálovací sada virtuálních počítačů** , který používá všechny vytvořené prostředky

Zadejte vlastní heslo *admin_password* hodnotu.

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
      vmss_lb_name: myVMSSlb
      location: eastus
      admin_username: azureuser
      admin_password: "your_password"
    tasks:
      - name: Create a resource group
        azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
      - name: Create virtual network
        azure_rm_virtualnetwork:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefixes: "10.0.0.0/16"
      - name: Add subnet
        azure_rm_subnet:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefix: "10.0.1.0/24"
          virtual_network: "{{ vmss_name }}"
      - name: Create public IP address
        azure_rm_publicipaddress:
          resource_group: "{{ resource_group }}"
          allocation_method: Static
          name: "{{ vmss_name }}"
      - name: Create Network Security Group that allows SSH
        azure_rm_securitygroup:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          rules:
            - name: SSH
              protocol: Tcp
              destination_port_range: 22
              access: Allow
              priority: 1001
              direction: Inbound

      - name: Create a load balancer
        azure_rm_loadbalancer:
          name: "{{ vmss_lb_name }}"
          location: "{{ location }}"
          resource_group: "{{ resource_group }}"
          public_ip: "{{ vmss_name }}"
          probe_protocol: Tcp
          probe_port: 8080
          probe_interval: 10
          probe_fail_count: 3
          protocol: Tcp
          load_distribution: Default
          frontend_port: 80
          backend_port: 8080
          idle_timeout: 4
          natpool_frontend_port_start: 50000
          natpool_frontend_port_end: 50040
          natpool_backend_port: 22
          natpool_protocol: Tcp

      - name: Create VMSS
        azure_rm_virtualmachine_scaleset:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          vm_size: Standard_DS1_v2
          admin_username: "{{ admin_username }}"
          admin_password: "{{ admin_password }}"
          ssh_password_enabled: true
          capacity: 2
          virtual_network_name: "{{ vmss_name }}"
          subnet_name: "{{ vmss_name }}"
          upgrade_policy: Manual
          tier: Standard
          managed_disk_type: Standard_LRS
          os_disk_caching: ReadWrite
          image:
            offer: UbuntuServer
            publisher: Canonical
            sku: 16.04-LTS
            version: latest
          load_balancer: "{{ vmss_lb_name }}"
          data_disks:
            - lun: 0
              disk_size_gb: 20
              managed_disk_type: Standard_LRS
              caching: ReadOnly
            - lun: 1
              disk_size_gb: 30
              managed_disk_type: Standard_LRS
              caching: ReadOnly
  ```

Chcete-li vytvořit prostředí virtuálního počítače škálovací sady pomocí Ansible, uložit předchozí playbook jako `vmss-create.yml`, nebo [stáhnout ukázkový playbook Ansible](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml).

Chcete-li spustit Ansible playbook, použijte **ansible playbook** takto:

  ```bash
  ansible-playbook vmss-create.yml
  ```

Po spuštění playbooku, výstup podobný následující příklad ukazuje, že virtuální počítač škálovací sada se úspěšně vytvořil:

  ```bash
  PLAY [localhost] ***********************************************************

  TASK [Gathering Facts] *****************************************************
  ok: [localhost]

  TASK [Create a resource group] ****************************************************************************
  changed: [localhost]

  TASK [Create virtual network] ****************************************************************************
  changed: [localhost]

  TASK [Add subnet] **********************************************************
  changed: [localhost]

  TASK [Create public IP address] ****************************************************************************
  changed: [localhost]

  TASK [Create Network Security Group that allows SSH] ****************************************************************************
  changed: [localhost]

  TASK [Create a load balancer] ****************************************************************************
  changed: [localhost]

  TASK [Create VMSS] *********************************************************
  changed: [localhost]

  PLAY RECAP *****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0

  ```

## <a name="scale-out-a-vmss"></a>Horizontální navýšení kapacity VMSS
Škálovací sady vytvořené virtuální počítač má dvě instance. Když přejdete do virtuálního počítače škálovací sady na webu Azure Portal, uvidíte **Standard_DS1_v2 (2 instance)**. Můžete také použít [Azure Cloud Shell](https://shell.azure.com/) spuštěním následujícího příkazu ve službě Cloud Shell:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Výstup by měl vypadat přibližně takto:

  ```bash
  {
    "capacity": 2,
  }
  ```

Teď můžeme škálovat z dvě instance na tři instance. Následující kód playbook Ansible načte informace o škálování virtuálního počítače a změní jeho kapacity ze dvou na tři. 

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
    tasks: 
      - name: Get scaleset info
        azure_rm_virtualmachine_scaleset_facts:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          format: curated
        register: output_scaleset

      - name: Dump scaleset info
        debug:
          var: output_scaleset

      - name: Modify scaleset (change the capacity to 3)
        set_fact:
          body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

      - name: Update something in that VMSS
        azure_rm_virtualmachine_scaleset: "{{ body }}"
  ```

Chcete-li horizontální navýšení kapacity škálovací sady virtuálních počítačů, které jste vytvořili, uložit předchozí playbook jako `vmss-scale-out.yml` nebo [stáhnout ukázkový playbook Ansible](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml)). 

Následující příkaz spustí playbook:

  ```bash
  ansible-playbook vmss-scale-out.yml
  ```

Výstup spuštění Ansible playbook ukazuje, že škálovací sadu virtuálních počítačů úspěšně horizontálním navýšení kapacity:

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get scaleset info] ***************************************************************************
  ok: [localhost]

  TASK [Dump scaleset info] ***************************************************************************
  ok: [localhost] => {
      "output_scaleset": {
          "ansible_facts": {
              "azure_vmss": [
                  {
                      ......
                  }
              ]
          },
          "changed": false,
          "failed": false
      }
  }

  TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] ***************************************************************************
  ok: [localhost]

  TASK [Update something in that VMSS] ***************************************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=5    changed=1    unreachable=0    failed=0
  ```

Pokud přejděte do škálovací sady virtuálních počítačů, které jste nakonfigurovali na webu Azure Portal, uvidíte **Standard_DS1_v2 (3 instancí)**. Můžete si taky ověřit změny s [Azure Cloud Shell](https://shell.azure.com/) spuštěním následujícího příkazu:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Výsledky spuštění příkazu ve službě Cloud Shell ukazuje, že nyní existují tři instance. 

  ```bash
  {
    "capacity": 3,
  }
  ```

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"] 
> [Playbook Ansible vzorku pro VMSS](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)