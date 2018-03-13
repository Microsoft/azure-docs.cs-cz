---
title: "K vytvoření kompletní virtuální počítač s Linuxem v Azure použijte Ansible | Microsoft Docs"
description: "Další informace o použití Ansible k vytváření a správě dokončení prostředí Linux virtuálního počítače v Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: 5efb666652bf124160df836b5d12305903d907ba
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>Vytvořte dokončení prostředí Linux virtuálního počítače v Azure s Ansible
Ansible umožňuje automatizovat nasazení a konfigurace prostředků ve vašem prostředí. Ansible můžete použít ke správě virtuálních počítačů (VM) v Azure, stejně jako jiný prostředek. Tento článek ukazuje, jak vytvořit úplný prostředí Linux a podpůrné prostředky s Ansible. Můžete si také přečíst postup [vytvořit základní virtuální počítač s Ansible](ansible-create-vm.md).


## <a name="prerequisites"></a>Požadavky
Ke správě prostředků Azure s Ansible, budete potřebovat následující:

- Ansible a moduly Azure Python SDK v systému hostitele.
    - Nainstalujte Ansible [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), [CentOS 7.3](ansible-install-configure.md#centos-73), a [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2)
- Přihlašovací údaje Azure a Ansible nakonfigurovat jejich použití.
    - [Vytvořit přihlašovací údaje Azure a nakonfigurovat Ansible](ansible-install-configure.md#create-azure-credentials)
- Azure CLI verze verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. 
    - Pokud potřebujete upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). Můžete také použít [cloudové prostředí](/azure/cloud-shell/quickstart) z prohlížeče.


## <a name="create-virtual-network"></a>Vytvoření virtuální sítě
V následující části v playbook Ansible vytvoří virtuální síť s názvem *myVnet* v *10.0.0.0/16* adresní prostor:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Chcete-li přidat podsíť, v následující části vytvoří podsíť s názvem *mySubnet* v *myVnet* virtuální sítě:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>Vytvoření veřejné IP adresy
Pro přístup k prostředkům přes Internet, vytvořte a přiřaďte veřejnou IP adresu pro virtuální počítač. V následující části v playbook Ansible vytvoří veřejnou IP adresu s názvem *myPublicIP*:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>Vytvořit skupinu zabezpečení sítě
Skupiny zabezpečení sítě řídí tok síťový provoz do/z virtuálního počítače. V následující části v playbook Ansible vytvoří skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup* a definuje pravidlo umožňující přenos SSH na TCP port 22:

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


## <a name="create-virtual-network-interface-card"></a>Vytvořit virtuální síťová karta
Virtuální síťová karta (NIC) připojí k dané virtuální síti, veřejnou IP adresu a skupinu zabezpečení sítě virtuálního počítače. V následující části v playbook Ansible vytvoří virtuální síťový adaptér s názvem *myNIC* připojené k virtuální síťové prostředky, které jste vytvořili:

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


## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače
Posledním krokem je vytvoření virtuálního počítače a použít všechny prostředky, které jsou vytvořené. V následující části v playbook Ansible vytvoří virtuální počítač s názvem *Můjvp* a připojí virtuální síťový adaptér s názvem *myNIC*. Zadejte svoje vlastní dokončení veřejného klíče data v *key_data* spárujte následujícím způsobem:

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
        key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.3'
      version: latest
```

## <a name="complete-ansible-playbook"></a>Dokončení Ansible playbook
Tyto části sdružujícího vytvořit Ansible playbook s názvem *azure_create_complete_vm.yml* a vložte následující obsah. Zadejte svoje vlastní dokončení veřejného klíče data v *key_data* pár:

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
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
          key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Ansible musí nasadit všechny prostředky do skupiny prostředků. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/vm#az_vm_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Pokud chcete vytvořit úplný prostředí virtuálních počítačů s Ansible, spusťte playbook následujícím způsobem:

```bash
ansible-playbook azure_create_complete_vm.yml
```

Výstup bude vypadat podobně jako v následujícím příkladu, který ukazuje, že virtuální počítač se úspěšně vytvořil:

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create virtual network] *********************************************
changed: [localhost]

TASK [Add subnet] *********************************************************
changed: [localhost]

TASK [Create public IP address] *******************************************
changed: [localhost]

TASK [Create Network Security Group that allows SSH] **********************
changed: [localhost]

TASK [Create virtual network inteface card] *******************************
changed: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=7    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Další postup
Tento příklad vytvoří kompletní prostředí virtuálních počítačů, včetně požadované prostředky virtuální sítě. Více přímé příklad k vytvoření virtuálního počítače do stávající síťové prostředky s výchozími možnostmi najdete v tématu [vytvoření virtuálního počítače](ansible-create-vm.md).
