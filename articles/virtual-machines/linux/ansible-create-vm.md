---
title: Vytvořte základní virtuální počítač s Linuxem v Azure pomocí Ansible | Microsoft Docs
description: Další informace o použití Ansible k vytváření a správě základní virtuální počítač s Linuxem v Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: e36bdbf84b275fb8a6a4e42496b3080bebf1b193
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716631"
---
# <a name="create-a-basic-virtual-machine-in-azure-with-ansible"></a>Vytvoření základního virtuálního počítače v Azure pomocí Ansible
Ansible umožňuje automatizovat nasazení a konfigurace prostředků ve vašem prostředí. Ansible můžete použít ke správě virtuálních počítačů (VM) v Azure, stejně jako jiný prostředek. Tento článek ukazuje, jak vytvořit základní virtuální počítač s Ansible. Můžete si také přečíst postup [vytvořit úplný prostředí virtuálních počítačů s Ansible](ansible-create-complete-vm.md).


## <a name="prerequisites"></a>Požadavky
Ke správě prostředků Azure s Ansible, budete potřebovat následující:

- Ansible a moduly Azure Python SDK v systému hostitele.
    - Nainstalujte Ansible [CentOS 7.4](ansible-install-configure.md#centos-74), [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), a [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2)
- Přihlašovací údaje Azure a Ansible nakonfigurovat jejich použití.
    - [Vytvořit přihlašovací údaje Azure a nakonfigurovat Ansible](ansible-install-configure.md#create-azure-credentials)
- Azure CLI verze verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. 
    - Pokud potřebujete upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). Můžete také [prostředí cloudu Azure](/azure/cloud-shell/quickstart) z webového prohlížeče.


## <a name="create-supporting-azure-resources"></a>Vytvoření Podpora prostředků Azure
V tomto příkladu vytvoříte sadu runbook, která nasadí virtuální počítač do existující infrastruktury. Nejprve vytvořte skupinu prostředků s [vytvořit skupinu az](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Vytvoření virtuální sítě pro virtuální počítač s [vytvoření sítě vnet az](/cli/azure/network/vnet#az-network-vnet-create). Následující příklad vytvoří virtuální síť s názvem *myVnet* a podsíť s názvem *mySubnet*:

```azurecli
az network vnet create \
  --resource-group myResourceGroup \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnet \
  --subnet-prefix 10.0.1.0/24
```


## <a name="create-and-run-ansible-playbook"></a>Vytvoření a spuštění Ansible playbook
Vytvoření Ansible playbook s názvem *azure_create_vm.yml* a vložte následující obsah. Tento příklad vytvoří jeden virtuální počítač a nakonfiguruje pověření SSH. Zadejte svoje vlastní dokončení veřejného klíče data v *key_data* spárujte následujícím způsobem:

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

Pokud chcete vytvořit virtuální počítač s Ansible, spusťte playbook následujícím způsobem:

```bash
ansible-playbook azure_create_vm.yml
```

Výstup bude vypadat podobně jako v následujícím příkladu, který ukazuje, že virtuální počítač se úspěšně vytvořil:

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
Tento příklad vytvoří virtuální počítač v existující skupinu prostředků a virtuální síť už nasazená. Podrobnější příklad o tom, jak použít Ansible k vytvoření doprovodné materiály, jako je například virtuální sítě a pravidel skupin zabezpečení sítě najdete v tématu [vytvořit úplný prostředí virtuálních počítačů s Ansible](ansible-create-complete-vm.md).
