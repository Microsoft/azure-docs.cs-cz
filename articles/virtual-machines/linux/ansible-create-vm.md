---
title: Rychlý start – konfigurace virtuálních počítačů s Linuxem v Azure pomocí Ansible | Dokumentace Microsoftu
description: V tomto rychlém startu zjistěte, jak vytvořit virtuální počítač s Linuxem v Azure pomocí Ansible
keywords: ansible, azure, devops, virtual machine
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 4bf7d43b682a2a42d9909f9cd33aa9542a1a9330
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64711636"
---
# <a name="quickstart-configure-linux-virtual-machines-in-azure-using-ansible"></a>Rychlý start: Konfigurace virtuálních počítačů s Linuxem v Azure pomocí Ansible

Ansible umožňuje pomocí deklarativního jazyka automatizovat vytváření, konfiguraci a nasazování prostředků Azure prostřednictvím *playbooků* Ansible. Tento článek představuje playbook Ansible ukázky pro konfiguraci virtuálních počítačů s Linuxem. [Kompletní playbook Ansible](#complete-sample-ansible-playbook) je uvedený na konci tohoto článku.

## <a name="prerequisites"></a>Požadavky

- [!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Ansible potřebuje skupinu prostředků, ve které jsou nasazené vaše prostředky. Následující část ukázkového playbooku Ansible vytvoří skupinu prostředků `myResourceGroup` v umístění `eastus`:

```yaml
- name: Create resource group
  azure_rm_resourcegroup:
    name: myResourceGroup
    location: eastus
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Když vytváříte virtuální počítač Azure, musíte vytvořit [virtuální síť](/azure/virtual-network/virtual-networks-overview) nebo použít existující virtuální síť. Také musíte rozhodnout, jak budou vaše virtuální počítače ve virtuální síti dostupné. Následující část ukázkového playbooku Ansible vytvoří virtuální síť `myVnet` v adresním prostoru `10.0.0.0/16`:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Všechny prostředky Azure nasazené do virtuální sítě se nasazují do [podsítě](/azure/virtual-network/virtual-network-manage-subnet) ve virtuální síti. 

Následující část ukázkového playbooku Ansible vytvoří podsíť `mySubnet` ve virtuální síti `myVnet`:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```

## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy





[Veřejné IP adresy](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) umožňují internetovým prostředkům příchozí komunikaci s prostředky Azure. Veřejné IP adresy taky umožňují prostředkům Azure odchozí komunikaci s veřejně přístupných služeb Azure. V obou případech IP adresu přiřazenou k prostředku, ke kterému přistupujete. Adresa je pro prostředek vyhrazená, dokud je její přiřazení zrušit. Pokud k prostředku není přiřazena veřejná IP adresa, prostředek můžete stále odchozí komunikaci k Internetu. Navázání připojení Azure dynamicky přiřazuje dostupnou IP adresu. Dynamicky přidělovanou adresu není pro prostředek vyhrazená.

Následující část ukázkového playbooku Ansible vytvoří veřejnou IP adresu `myPublicIP`:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

[Skupiny zabezpečení sítě](/azure/virtual-network/security-overview) filtrování síťového provozu mezi prostředky ve virtuální síti Azure. Pravidla zabezpečení jsou definovány, kterými se řídí příchozí a odchozí provoz do a z prostředků Azure. Další informace o prostředcích Azure a skupiny zabezpečení sítě najdete v tématu [integrace virtuální sítě pro služby Azure](/azure/virtual-network/virtual-network-for-azure-services)

Následující playbook vytvoří skupinu zabezpečení sítě s názvem `myNetworkSecurityGroup`. Skupina zabezpečení sítě obsahuje pravidla, které umožní provoz SSH na portu TCP 22.

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: Tcp
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```

## <a name="create-a-virtual-network-interface-card"></a>Vytvoření virtuální síťové karty

Virtuální síťová karta propojí váš virtuální počítač s danou virtuální sítí, veřejnou IP adresou a skupinou zabezpečení sítě. 

Následující části sekce playbook Ansible ukázka vytvoří virtuální síťové karty s názvem `myNIC` připojené k virtuální síťové prostředky, které jste vytvořili:

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Posledním krokem je vytvoření virtuálního počítače, který používá všechny prostředky, které jste vytvořili v předchozích částech tohoto článku. 

Část ukázkového playbooku Ansible uvedená v této části vytvoří virtuální počítač `myVM` a připojí k němu virtuální síťovou kartu `myNIC`. Zástupnou hodnotu &lt;your-key-data> nahraďte úplnými daty vlastního veřejného klíče.

```yaml
- name: Create VM
  azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_DS1_v2
    admin_username: azureuser
    ssh_password_enabled: false
    ssh_public_keys:
      - path: /home/azureuser/.ssh/authorized_keys
        key_data: <your-key-data>
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.5'
      version: latest
```

## <a name="complete-sample-ansible-playbook"></a>Kompletní ukázkový playbook Ansible

Tato část obsahuje úplný ukázkový playbook Ansible, který jste vytvořili v průběhu tohoto článku. 

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
    register: output_ip_address
  - name: Dump public IP for VM which will be created
    debug:
      msg: "The public IP is {{ output_ip_address.state.ip_address }}."
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys:
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: <your-key-data>
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

## <a name="run-the-sample-ansible-playbook"></a>Spuštění ukázkového playbooku Ansible

Tato část vás provede spuštěním ukázkového playbooku Ansible z tohoto článku.

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otevřete [Cloud Shell](/azure/cloud-shell/overview).

1. Následujícím způsobem vytvořte soubor `azure_create_complete_vm.yml`, který bude obsahovat váš playbook, a otevřete ho v editoru VI:

   ```azurecli-interactive
   vi azure_create_complete_vm.yml
   ```

1. Stisknutím klávesy **I** přejděte do režimu vkládání.

1. Vložte do editoru [kompletní ukázkový playbook Ansible](#complete-sample-ansible-playbook).

1. Stisknutím klávesy **Esc** ukončete režim vkládání.

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

    ```bash
    :wq
    ```

1. Spusťte ukázkový playbook Ansible.

   ```bash
   ansible-playbook azure_create_complete_vm.yml
   ```

1. Výstup bude vypadat podobně jako v následujícím příkladu, kde vidíte, že se úspěšně vytvořil virtuální počítač:

   ```bash
   PLAY [Create Azure VM] ****************************************************

   TASK [Gathering Facts] ****************************************************
   ok: [localhost]

   TASK [Create resource group] *********************************************
   changed: [localhost]

   TASK [Create virtual network] *********************************************
   changed: [localhost]

   TASK [Add subnet] *********************************************************
   changed: [localhost]

   TASK [Create public IP address] *******************************************
   changed: [localhost]

   TASK [Dump public IP for VM which will be created] ********************************************************************
   ok: [localhost] => {
      "msg": "The public IP is <ip-address>."
   }

   TASK [Create Network Security Group that allows SSH] **********************
   changed: [localhost]

   TASK [Create virtual network inteface card] *******************************
   changed: [localhost]

   TASK [Create VM] **********************************************************
   changed: [localhost]

   PLAY RECAP ****************************************************************
   localhost                  : ok=8    changed=7    unreachable=0    failed=0
   ```

1. K přístupu k virtuálnímu počítači s Linuxem slouží příkaz SSH. Zástupnou hodnotu &lt;ip-address> nahraďte IP adresou z předchozího kroku.

    ```bash
    ssh azureuser@<ip-address>
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [Rychlé zprovoznění: Spravovat virtuální počítač s Linuxem v Azure pomocí Ansible](./ansible-manage-linux-vm.md)