---
title: Vytváření škálovacích sad virtuálních počítačů v Azure pomocí Ansible
description: Zjistěte, jak pomocí Ansible vytvořit a nakonfigurovat škálovací sadu virtuálních počítačů v Azure.
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/24/2018
ms.openlocfilehash: f3b08c41d3bf083c7cca5897cee11a1a4b9c9092
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918571"
---
# <a name="create-virtual-machine-scale-sets-in-azure-using-ansible"></a>Vytváření škálovacích sad virtuálních počítačů v Azure pomocí Ansible
Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. Pomocí Ansible můžete spravovat škálovací sadu virtuálních počítačů v Azure stejně jako jakékoli jiné prostředky Azure. V tomto článku se dozvíte, jak pomocí Ansible vytvořit škálovací sadu virtuálních počítačů a horizontálně navýšit její kapacitu. 

## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure** – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ke spuštění následujících ukázkových playbooků v tomto kurzu se vyžaduje Ansible 2.6. 

## <a name="create-a-vmss"></a>Vytvoření škálovací sady virtuálních počítačů
Tato část představuje ukázkový playbook Ansible, který definuje následující prostředky:
- **Skupina prostředků**, do které se nasadí všechny vaše prostředky
- **Virtuální síť** v adresním prostoru 10.0.0.0/16.
- **Podsíť** v této virtuální síti.
- **Veřejná IP adresa**, která vám umožní přistupovat k prostředkům přes internet.
- **Skupina zabezpečení sítě**, která řídí tok síťového provozu do a ze škálovací sady virtuálních počítačů.
- **Nástroj pro vyrovnávání zatížení**, který s využitím pravidel nástroje pro vyrovnávání zatížení distribuuje provoz mezi sadu definovaných virtuálních počítačů.
- **Škálovací sada virtuálních počítačů**, která všechny vytvořené prostředky využívá.

Místo hodnoty *admin_password* zadejte vlastní heslo správce.

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

Pokud chcete vytvořit prostředí škálovací sady virtuálních počítačů pomocí Ansible, uložte předchozí playbook jako `vmss-create.yml` nebo si [stáhněte ukázkový playbook Ansible](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml).

Playbook Ansible spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:

  ```bash
  ansible-playbook vmss-create.yml
  ```

Po spuštění playbooku se zobrazí podobný výstup jako v následujícím příkladu, který ukazuje úspěšné vytvoření škálovací sady virtuálních počítačů:

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

## <a name="scale-out-a-vmss"></a>Horizontální navýšení kapacity škálovací sady virtuálních počítačů
Vytvořená škálovací sada virtuálních počítačů obsahuje dvě instance. Pokud přejdete ke škálovací sadě virtuálních počítačů na webu Azure Portal, zobrazí se **Standard_DS1_v2 (2 instance)**. Můžete použít také [Azure Cloud Shell](https://shell.azure.com/), a to spuštěním následujícího příkazu ve službě Cloud Shell:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Zobrazí se podobný výsledek jako v následujícím výstupu:

  ```bash
  {
    "capacity": 2,
  }
  ```

Teď provedeme škálování ze dvou na tři instance. Následující kód playbooku Ansible načte informace o škálovací sadě virtuálních počítačů a změní její kapacitu ze dvou na tři instance. 

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

Pokud chcete horizontálně navýšit kapacitu škálovací sady virtuálních počítačů, kterou jste vytvořili, uložte předchozí playbook jako `vmss-scale-out.yml` nebo si [stáhněte ukázkový playbook Ansible](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml). 

Následující příkaz spustí playbook:

  ```bash
  ansible-playbook vmss-scale-out.yml
  ```

Výstup spuštění playbooku Ansible ukazuje úspěšné horizontální navýšení kapacity škálovací sady virtuálních počítačů:

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

Pokud přejdete ke škálovací sadě virtuálních počítačů, kterou jste nakonfigurovali, na webu Azure Portal, zobrazí se **Standard_DS1_v2 (3 instance)**. Změnu můžete ověřit také pomocí služby [Azure Cloud Shell](https://shell.azure.com/), a to spuštěním následujícího příkazu:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Výsledek spuštění příkazu ve službě Cloud Shell ukazuje, že teď existují tři instance. 

  ```bash
  {
    "capacity": 3,
  }
  ```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"] 
> [Ukázkový playbook Ansible pro škálovací sadu virtuálních počítačů](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)