---
title: Použití cloud-init k přidání uživatele do virtuálního počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Použití cloud-init k přidání uživatele do virtuálního počítače s Linuxem během vytváření pomocí Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 905d701437b1b580c019c800d13b18f725580fdd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972942"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Použití cloud-init k přidání uživatele do virtuálního počítače s Linuxem v Azure
V tomto článku se dozvíte, jak používat [cloud-init](https://cloudinit.readthedocs.io) přidání uživatele do virtuálního počítače (VM) nebo virtuálního počítače škálovací sady (VMSS) při zřizování času v Azure. Tento cloud-init skript se spustí při prvním spuštění po Azure se zřizují prostředky. Další informace o tom, jak funguje cloud-init nativně v Azure a podporovaných distribucích systému Linux, najdete v části [přehled cloud-init](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Přidání uživatele do virtuálního počítače s nástrojem cloud-init
Jedním z prvních úkolů na všechny nové virtuální počítač s Linuxem je přidat další uživatele sami se vyhněte použití *kořenové*. Klíče SSH jsou nejvhodnější pro zabezpečení a použitelnost. Klíče jsou přidány do *~/.ssh/authorized_keys* soubor pomocí tohoto skriptu cloud-init.

K přidání uživatele do virtuálního počítače s Linuxem, vytvořit soubor v aktuálním prostředí *cloud_init_add_user.txt* a vložte do něj následující konfiguraci. V tomto příkladu vytvoření souboru ve službě Cloud Shell není na místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud_init_add_user.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Zvolte #1 použít **nano** editoru. Ujistěte se, že se soubor celý cloud-init zkopíroval správně, zejména první řádek.  Budete muset zadat veřejný klíč (například obsah *~/.ssh/id_rsa.pub*) pro hodnotu vlastnosti `ssh-authorized-keys:` -má byla zkrácena zde pro zjednodušení příkladu.

```yaml
#cloud-config
users:
  - default
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```
> [!NOTE] 
> #Cloudu – konfigurační soubor obsahuje `- default` parametr zahrnuté. Uživatel, to se připojí ke stávající správce vytvoří během zřizování. Pokud vytvoříte uživatele bez `- default` parametr - uživatele s rolí správce vygenerovaný automaticky vytvoří Platforma Azure bude přepsán. 

Před nasazením této bitové kopie, je potřeba vytvořit skupinu prostředků pomocí [vytvořit skupiny az](/cli/azure/group#az_group_create) příkazu. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teď vytvořte virtuální počítač s [az vm vytvořit](/cli/azure/vm#az_vm_create) a zadejte soubor cloud-init s `--custom-data cloud_init_add_user.txt` následujícím způsobem:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

Připojte přes SSH k veřejné IP adresu vašeho virtuálního počítače uvedené ve výstupu předchozího příkazu. Zadejte vlastní **publicIpAddress** následujícím způsobem:

```bash
ssh <publicIpAddress>
```

Pokud chcete potvrdit, váš uživatel byl přidán do virtuálního počítače a zadaných skupin, zobrazit obsah */etc/skupiny* to následujícím způsobem:

```bash
cat /etc/group
```

Následující příklad výstupu ukazuje uživateli *cloud_init_add_user.txt* soubor byl přidán do virtuálního počítače a příslušné skupiny:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Další postup
Příklady cloud-init další změny konfigurace najdete tady:
 
- [Přidání dalších uživatelů Linuxu na virtuální počítač](cloudinit-add-user.md)
- [Spusťte Správce balíčků aktualizovat existující balíčky při prvním spuštění](cloudinit-update-vm.md)
- [Změnit místní název hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizovat konfigurační soubory a klíče pro vložení](tutorial-automate-vm-deployment.md)