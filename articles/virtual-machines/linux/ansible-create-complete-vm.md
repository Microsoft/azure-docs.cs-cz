---
title: Použít Ansible k vytvoření kompletní virtuální počítač s Linuxem v Azure | Dokumentace Microsoftu
description: Zjistěte, jak použít Ansible k vytvoření a správě kompletního prostředí pro virtuální počítač Linux v Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 63228f8bf8729f1bf3796a77516490ae7088d5ed
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930840"
---
# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>Vytvoření kompletního linuxového prostředí virtuálních počítačů v Azure pomocí Ansible
Ansible umožňuje automatizovat nasazení a konfiguraci prostředků ve vašem prostředí. Ansible můžete použít ke správě virtuálních počítačů (VM) v Azure, stejně jako byste to udělali jiný prostředek. Tento článek popisuje, jak k vytvoření kompletního linuxového prostředí a podpůrné prostředky pomocí Ansible. Můžete také zjistíte, jak [vytvoření základního virtuálního počítače pomocí Ansible](ansible-create-vm.md).


## <a name="prerequisites"></a>Požadavky
Ke správě prostředků Azure pomocí Ansible, budete potřebovat následující:

- Ansible a sady Azure Python SDK moduly nainstalované ve vašem systému hostitele.
    - Nainstalovat Ansible [CentOS 7.4](ansible-install-configure.md#centos-74), [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), a [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2)
- Přihlašovací údaje Azure a Ansible nakonfigurovaný tak, aby jejich použití.
    - [Vytvořit přihlašovací údaje Azure a konfigurace Ansible](ansible-install-configure.md#create-azure-credentials)
- Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. 
    - Pokud potřebujete upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). Můžete také použít [Cloud Shell](/azure/cloud-shell/quickstart) z prohlížeče.


## <a name="create-virtual-network"></a>Vytvoření virtuální sítě
Pojďme podívat se na každý oddíl Ansible playbook a vytvoření jednotlivých prostředků Azure. Kompletní playbooku, naleznete v tématu [této části tohoto článku](#complete-ansible-playbook).

V následující části v Ansible playbook vytvoří virtuální síť s názvem *myVnet* v *10.0.0.0/16* adresní prostor:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Chcete-li přidat podsíť, následující části se vytvoří podsíť s názvem *mySubnet* v *myVnet* virtuální sítě:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>Vytvoření veřejné IP adresy
Pro přístup k prostředkům v síti Internet, vytvořit a přiřadit veřejnou IP adresu vašeho virtuálního počítače. V následující části v Ansible playbook vytvoří veřejnou IP adresu s názvem *myPublicIP*:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>Vytvořit skupinu zabezpečení sítě
Skupiny zabezpečení sítě řízení toku síťového provozu do a z virtuálního počítače. V následující části v Ansible playbook vytvoří skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup* a definuje pravidlo, které umožní provoz SSH na portu TCP 22:

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


## <a name="create-virtual-network-interface-card"></a>Vytvořit virtuální síťové karty
Virtuální síťové karty (NIC) připojí k dané virtuální sítě, veřejná IP adresa a skupiny zabezpečení sítě virtuálního počítače. V následující části v Ansible playbook vytvoří virtuální síťovou kartu s názvem *myNIC* připojené k virtuální síťové prostředky, které jste vytvořili:

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
Posledním krokem je vytvoření virtuálního počítače a použít všechny prostředky vytvořené. V následující části v Ansible playbook vytvoří virtuální počítač s názvem *myVM* a připojí virtuální síťovou kartu s názvem *myNIC*. Zadejte svoje kompletní veřejného klíče data v *key_data* spárovat následujícím způsobem:

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
      sku: '7.5'
      version: latest
```

## <a name="complete-ansible-playbook"></a>Dokončení Ansible playbook
Chcete-li tyto části pohromadě, vytvořit playbook Ansible s názvem *azure_create_complete_vm.yml* a vložte následující obsah. Zadejte svoje kompletní veřejného klíče data v *key_data* pár:

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
        sku: '7.5'
        version: latest
```

Ansible potřebuje k nasazení všech vašich prostředků do skupiny prostředků. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Vytvoření kompletního prostředí virtuálních počítačů pomocí Ansible, spusťte playbook následujícím způsobem:

```bash
ansible-playbook azure_create_complete_vm.yml
```

Výstup vypadá podobně jako v následujícím příkladu, který ukazuje, že virtuální počítač se úspěšně vytvořil:

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
Tento příklad vytvoří kompletní prostředí virtuálních počítačů, včetně požadované prostředky virtuální sítě. Přímější příklad k vytvoření virtuálního počítače do stávajících síťových prostředků pomocí výchozích možností najdete v tématu [vytvoření virtuálního počítače](ansible-create-vm.md).
