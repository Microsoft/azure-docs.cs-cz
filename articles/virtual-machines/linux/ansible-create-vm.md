---
title: Použití Ansible k vytvoření virtuálního počítače s Linuxem v Azure
description: Zjistěte, jak pomocí Ansible vytvořit virtuální počítač s Linuxem v Azure.
ms.service: ansible
keywords: ansible, azure, devops, virtual machine
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/22/2018
ms.openlocfilehash: 1f7f4809e064de15bb0a18c404f0df81307b1b9a
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073981"
---
# <a name="use-ansible-to-create-a-linux-virtual-machine-in-azure"></a>Použití Ansible k vytvoření virtuálního počítače s Linuxem v Azure
Ansible umožňuje pomocí deklarativního jazyka automatizovat vytváření, konfiguraci a nasazování prostředků Azure prostřednictvím *playbooků* Ansible. Jednotlivé části tohoto článku ukazují, jak můžou vypadat jednotlivé části playbooku Ansible pro vytváření a konfiguraci různých aspektů virtuálního počítače s Linuxem. [Kompletní playbook Ansible](#complete-sample-ansible-playbook) je uvedený na konci tohoto článku.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure** – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)]

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
[Veřejné IP adresy](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) umožňují internetovým prostředkům příchozí komunikaci s prostředky Azure. Veřejné IP adresy taky umožňují prostředkům Azure odchozí komunikaci s internetovými službami a veřejně přístupnými službami Azure prostřednictvím IP adresy přiřazené prostředku. Adresa je pro prostředek vyhrazená, dokud její přiřazení nezrušíte. Pokud není k prostředku přiřazená veřejná IP adresa, prostředek stále může využívat odchozí komunikaci s internetem, ale Azure dynamicky přiřadí dostupnou IP adresu, která není pro prostředek vyhrazená. 

Následující část ukázkového playbooku Ansible vytvoří veřejnou IP adresu `myPublicIP`:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě
[Skupina zabezpečení sítě](/azure/virtual-network/security-overview) umožňuje filtrovat síťový provoz do a z prostředků Azure ve virtuální síti Azure. Skupina zabezpečení sítě obsahuje pravidla zabezpečení umožňující povolit nebo odepřít příchozí nebo odchozí síťový provoz několika typů prostředků Azure. 

Následující část ukázkového playbooku Ansible vytvoří skupinu zabezpečení sítě `myNetworkSecurityGroup` a definuje pravidlo, které povolí provoz SSH na portu TCP 22:

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

Následující část ukázkového playbooku Ansible vytvoří virtuální síťovou kartu `myNIC` připojenou k prostředkům virtuální sítě, které jste vytvořili:

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
> [Použití Ansible ke správě virtuálního počítače s Linuxem v Azure](./ansible-manage-linux-vm.md)
