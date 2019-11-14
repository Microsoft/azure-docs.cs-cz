---
title: Rychlý Start – konfigurace virtuálních počítačů se systémem Linux v Azure pomocí Ansible
description: V tomto rychlém startu se dozvíte, jak vytvořit virtuální počítač se systémem Linux v Azure pomocí Ansible.
keywords: ansible, azure, devops, virtual machine
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 325b581910bc343f57a2da00ab3ed6e447c1e9e3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037080"
---
# <a name="quickstart-configure-linux-virtual-machines-in-azure-using-ansible"></a>Rychlý Start: Konfigurace virtuálních počítačů se systémem Linux v Azure pomocí Ansible

Ansible umožňuje pomocí deklarativního jazyka automatizovat vytváření, konfiguraci a nasazování prostředků Azure prostřednictvím *playbooků* Ansible. Tento článek představuje ukázkovou Ansible PlayBook pro konfiguraci virtuálních počítačů se systémem Linux. [Kompletní playbook Ansible](#complete-sample-ansible-playbook) je uvedený na konci tohoto článku.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)]

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





[Veřejné IP adresy](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) umožňují internetovým prostředkům příchozí komunikaci s prostředky Azure. Veřejné IP adresy taky umožňují prostředkům Azure komunikovat odchozím službám Azure, které jsou veřejně přístupné. V obou případech IP adresa přiřazená k prostředku, ke kterému se přistupoval. Adresa je vyhrazená pro prostředek, dokud ho neodstraníte. Pokud není k prostředku přiřazená veřejná IP adresa, může prostředek dál komunikovat odchozí připojení k Internetu. Připojení provede Azure dynamicky přiřazující dostupnou IP adresu. Dynamicky přiřazená adresa není pro prostředek vyhrazená.

Následující část ukázkového playbooku Ansible vytvoří veřejnou IP adresu `myPublicIP`:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

[Skupiny zabezpečení sítě](/azure/virtual-network/security-overview) filtrují síťový provoz mezi prostředky Azure ve virtuální síti. Jsou definovaná pravidla zabezpečení, která řídí příchozí a odchozí provoz do a z prostředků Azure. Další informace o prostředcích Azure a skupinách zabezpečení sítě najdete v tématu [Integrace virtuální sítě pro služby Azure](/azure/virtual-network/virtual-network-for-azure-services) .

Následující PlayBook vytvoří skupinu zabezpečení sítě s názvem `myNetworkSecurityGroup`. Skupina zabezpečení sítě obsahuje pravidlo, které povoluje provoz SSH na portu TCP 22.

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

V následující části ukázkového oddílu Ansible PlayBook se vytvoří virtuální síťová karta s názvem `myNIC` připojená k prostředkům virtuální sítě, kterou jste vytvořili:

```yaml
- name: Create virtual network interface card
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
  - name: Create virtual network interface card
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

1. Přihlásit se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

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

   TASK [Create virtual network interface card] *******************************
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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Rychlý Start: Správa virtuálního počítače se systémem Linux v Azure pomocí Ansible](./ansible-manage-linux-vm.md)
