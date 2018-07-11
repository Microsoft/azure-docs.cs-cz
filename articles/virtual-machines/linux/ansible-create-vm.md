---
title: Použít Ansible k vytvoření základní virtuální počítač s Linuxem v Azure | Dokumentace Microsoftu
description: Zjistěte, jak použít Ansible k vytváření a správě základní virtuální počítač s Linuxem v Azure
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
ms.openlocfilehash: 35dfe8348718e0edf8683f7eeddf286831697d89
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931425"
---
# <a name="create-a-basic-virtual-machine-in-azure-with-ansible"></a>Vytvoření základního virtuálního počítače v Azure pomocí Ansible
Ansible umožňuje automatizovat nasazení a konfiguraci prostředků ve vašem prostředí. Ansible můžete použít ke správě virtuálních počítačů (VM) v Azure, stejně jako byste to udělali jiný prostředek. V tomto článku se dozvíte, jak vytvořit základní virtuální počítač pomocí Ansible. Můžete také zjistíte, jak [vytvoření kompletního prostředí virtuálních počítačů pomocí Ansible](ansible-create-complete-vm.md).


## <a name="prerequisites"></a>Požadavky
Ke správě prostředků Azure pomocí Ansible, budete potřebovat následující:

- Ansible a sady Azure Python SDK moduly nainstalované ve vašem systému hostitele.
    - Nainstalovat Ansible [CentOS 7.4](ansible-install-configure.md#centos-74), [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), a [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2)
- Přihlašovací údaje Azure a Ansible nakonfigurovaný tak, aby jejich použití.
    - [Vytvořit přihlašovací údaje Azure a konfigurace Ansible](ansible-install-configure.md#create-azure-credentials)
- Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. 
    - Pokud potřebujete upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). Můžete také použít [Azure Cloud Shell](/azure/cloud-shell/quickstart) z webového prohlížeče.


## <a name="create-supporting-azure-resources"></a>Vytvořte Podpůrné prostředky Azure
V tomto příkladu vytvoříte sadu runbook, která nasadí virtuální počítač do existující infrastruktury. Nejprve vytvořte skupinu prostředků s [vytvořit skupiny az](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Vytvoření virtuální sítě pro virtuální počítač s [az network vnet vytvořit](/cli/azure/network/vnet#az-network-vnet-create). Následující příklad vytvoří virtuální síť s názvem *myVnet* a podsíť s názvem *mySubnet*:

```azurecli
az network vnet create \
  --resource-group myResourceGroup \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnet \
  --subnet-prefix 10.0.1.0/24
```


## <a name="create-and-run-ansible-playbook"></a>Vytvořte a spusťte Ansible playbook
Vytvořit playbook Ansible s názvem *azure_create_vm.yml* a vložte následující obsah. Tento příklad vytvoří jeden virtuální počítač a nakonfiguruje přihlašovací údaje SSH. Zadejte svoje kompletní veřejného klíče data v *key_data* spárovat následujícím způsobem:

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
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
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

Vytvoření virtuálního počítače pomocí Ansible, spusťte playbook následujícím způsobem:

```bash
ansible-playbook azure_create_vm.yml
```

Výstup vypadá podobně jako v následujícím příkladu, který ukazuje, že virtuální počítač se úspěšně vytvořil:

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```


## <a name="next-steps"></a>Další postup
Tento příklad vytvoří virtuální počítač v existující skupinu prostředků a virtuální síť už nasazené. Podrobnější příklad o tom, jak použít Ansible k vytvoření Podpůrné prostředky, jako jsou virtuální sítě a pravidel skupin zabezpečení sítě, naleznete v tématu [vytvoření kompletního prostředí virtuálních počítačů pomocí Ansible](ansible-create-complete-vm.md).
