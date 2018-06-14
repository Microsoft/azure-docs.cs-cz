---
title: Přidání uživatele do virtuálního počítače s Linuxem v Azure pomocí cloudu init | Microsoft Docs
description: Jak používat cloudové init k přidání uživatele do virtuálního počítače s Linuxem během vytváření pomocí Azure CLI 2.0
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
ms.openlocfilehash: ce4421fc8276f215564cb7a171a215cc166c8517
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29123459"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Přidání uživatele do virtuálního počítače s Linuxem v Azure pomocí init cloudu
V tomto článku se dozvíte, jak používat [cloudu init](https://cloudinit.readthedocs.io) přidat uživatele na virtuální počítač (VM) nebo virtuální počítač sadách škálování (VMSS) na zřizování čas v Azure. Tento skript cloudu init spouští při prvním spuštění počítače po prostředky se zřizují pomocí Azure. Další informace o cloudu init fungování nativně ve službě Azure a podporovaných distribucích systému Linux najdete v tématu [cloudu init přehled](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Přidání uživatele do virtuálního počítače s inicializací cloudu
Jedním z první úlohy na žádné nové virtuální počítače Linux je potřeba přidat další uživatelské sami nepoužívejte *kořenové*. Klíče SSH jsou vhodné pro zabezpečení a použitelnost. Klíče jsou přidány do *~/.ssh/authorized_keys* souboru pomocí tohoto skriptu init cloudu.

Chcete-li přidat uživatele do virtuálního počítače s Linuxem, vytvořte soubor ve své aktuální prostředí s názvem *cloud_init_add_user.txt* a vložte následující konfigurace. V tomto příkladu vytvoření souboru v prostředí cloudu není na místním počítači. Můžete použít libovolný editor, které chcete. Zadáním příkazu `sensible-editor cloud_init_add_user.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Zvolte #1 používat **nano** editor. Ujistěte se, že je soubor celou cloudu init zkopírován správně, obzvláště první řádek.  Je třeba zadat veřejný klíč (například obsah *~/.ssh/id_rsa.pub*) na hodnotu `ssh-authorized-keys:` -má byla zkrácena sem pro zjednodušení příkladu.

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
> Zahrnuje #cloudu konfiguračním souboru `- default` parametr zahrnuty. Uživatel, to se připojí ke stávající správce vytvořené při zřizování. Pokud vytvoříte uživatele bez `- default` parametr - uživatele automaticky generovaného správce vytvořit platformou Azure by být přepsána. 

Než nasadíte tuto bitovou kopii, je nutné vytvořit skupinu prostředků s [vytvořit skupinu az](/cli/azure/group#az_group_create) příkaz. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teď vytvořte virtuální počítač s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) a určete soubor init cloudu s `--custom-data cloud_init_add_user.txt` následujícím způsobem:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH na veřejnou IP adresu vašeho virtuálního počítače zobrazené ve výstupu z předchozí příkaz. Zadejte vlastní **publicIpAddress** následujícím způsobem:

```bash
ssh <publicIpAddress>
```

Potvrďte uživatelů byla přidána do virtuálního počítače a zadaných skupin zobrazit obsah */etc/skupiny* následujícím způsobem:

```bash
cat /etc/group
```

Následující příklad výstupu zobrazuje uživateli *cloud_init_add_user.txt* soubor byl přidán do virtuálního počítače a do příslušné skupiny:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Další postup
Mezi další cloudu init změny konfigurace naleznete v následujících tématech:
 
- [Přidání další uživatele Linux do virtuálního počítače](cloudinit-add-user.md)
- [Spusťte Správce balíčků aktualizovat existující balíčky na při prvním spuštění](cloudinit-update-vm.md)
- [Změňte název místního hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfigurační soubory a vložit klíče](tutorial-automate-vm-deployment.md)