---
title: Aktualizace a nainstalovat balíčky v virtuálního počítače s Linuxem v Azure pomocí cloudu init | Microsoft Docs
description: Postup použití cloudu init pro aktualizace a nainstalovat balíčky během vytváření pomocí Azure CLI 2.0 ve virtuální počítač s Linuxem
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
ms.date: 04/20/2018
ms.author: rclaus
ms.openlocfilehash: 8d5835b5d1b0c2f77bdf5e1a2b808478b8f4de22
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186150"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Aktualizace a nainstalovat balíčky v virtuálního počítače s Linuxem v Azure pomocí init cloudu
V tomto článku se dozvíte, jak používat [cloudu init](https://cloudinit.readthedocs.io) aktualizovat balíčky na systémem Linux nastaví virtuální počítač (VM) nebo škálování virtuálních počítačů (VMSS) při zřizování čas v Azure. Tyto skripty cloudu init spustit při prvním spuštění počítače po prostředky se zřizují Azure. Další informace o cloudu init fungování nativně ve službě Azure a podporovaných distribucích systému Linux najdete v tématu [init cloudu – přehled](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Aktualizace virtuálního počítače s inicializací cloudu
Z bezpečnostních důvodů můžete nakonfigurovat virtuální počítač na nejnovější aktualizace na při prvním spuštění. Jak cloud init funguje napříč různých distribucích systému Linux, je nutné specifikovat `apt` nebo `yum` pro správce balíčků. Místo toho můžete definovat `package_upgrade` a umožněte proces cloudu init určit příslušné mechanismus pro distro používán. Tento pracovní postup můžete použít stejné cloudové init skripty ve distribucích.

Informace o procesu upgradu v akci, vytvořte soubor ve své aktuální prostředí s názvem *cloud_init_upgrade.txt* a vložte následující konfigurace. V tomto příkladu vytvoření souboru v prostředí cloudu není na místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud_init_upgrade.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Zvolte #1 používat **nano** editor. Ujistěte se, že je soubor celou cloudu init zkopírován správně, obzvláště první řádek.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Než nasadíte tuto bitovou kopii, je nutné vytvořit skupinu prostředků s [vytvořit skupinu az](/cli/azure/group#az_group_create) příkaz. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teď vytvořte virtuální počítač s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) a určete soubor init cloudu s `--custom-data cloud_init_upgrade.txt` následujícím způsobem:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH na veřejnou IP adresu vašeho virtuálního počítače zobrazené ve výstupu z předchozí příkaz. Zadejte vlastní **publicIpAddress** následujícím způsobem:

```bash
ssh <publicIpAddress>
```

Spusťte nástroj pro správu balíček a kontrola aktualizací.

```bash
sudo yum update
```

Init cloudu – zkontrolovat a nainstalovat aktualizace na spouštěcí, měla by existovat žádné další aktualizace použít.  Zobrazí proces aktualizace, počet balíčků, změnit, jakož i instalaci `httpd` spuštěním `yum history` a zkontrolujte podobná té následující výstup.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Další postup
Mezi další cloudu init změny konfigurace naleznete v následujících tématech:
 
- [Přidání další uživatele Linux do virtuálního počítače](cloudinit-add-user.md)
- [Spusťte Správce balíčků aktualizovat existující balíčky na při prvním spuštění](cloudinit-update-vm.md)
- [Změňte název místního hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfigurační soubory a vložit klíče](tutorial-automate-vm-deployment.md)