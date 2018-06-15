---
title: Spuštění skriptu bash ve virtuální počítač s Linuxem v Azure pomocí cloudu init | Microsoft Docs
description: Postup použití cloudu init pro spuštění skriptu bash ve virtuální počítač s Linuxem během vytváření pomocí Azure CLI 2.0
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
ms.openlocfilehash: 471749563fae5b5de6e98e22ebf2ec5cc9365368
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29123714"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Spuštění skriptu bash ve virtuální počítač s Linuxem v Azure pomocí init cloudu
V tomto článku se dozvíte, jak používat [cloudu init](https://cloudinit.readthedocs.io) existující bash ke spuštění skriptu ve virtuálním počítači (VM) Linux nebo sadách škálování virtuálních počítačů (VMSS) na zřizování čas v Azure. Tyto skripty cloudu init spustit při prvním spuštění počítače po prostředky se zřizují Azure. Další informace o cloudu init fungování nativně ve službě Azure a podporovaných distribucích systému Linux najdete v tématu [init cloudu – přehled](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Spusťte skript bash s inicializací cloudu
S cloudu inicializací není potřeba převést stávající skripty cloudu config přijme cloudu init více vstupních typů, z nichž jeden je skript bash.

Pokud používáte rozšíření Azure Linux vlastních skriptů spouštět skripty, můžete migrovat, aby uživatelé používali init cloudu. Ale rozšíření Azure mají integrované zprávy upozornění na selhání skriptu, nasazení bitové kopie init cloudu se není nezdaří, pokud skript selže.

Pokud chcete zobrazit tuto funkci v akci, vytvořte skript jednoduché bash pro testování. Init – cloudu, jako `#cloud-config` souboru skriptu musí být místní vůči kde bude spuštěna AzureCLI příkazy ke zřízení virtuálního počítače.  V tomto příkladu vytvoření souboru v prostředí cloudu není na místním počítači. Můžete použít libovolný editor, které chcete. Zadáním příkazu `sensible-editor simple_bash.sh` soubor vytvořte a zobrazte seznam editorů k dispozici. Zvolte #1 používat **nano** editor. Ujistěte se, že je soubor celou cloudu init zkopírován správně, obzvláště první řádek.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Než nasadíte tuto bitovou kopii, je nutné vytvořit skupinu prostředků s [vytvořit skupinu az](/cli/azure/group#az_group_create) příkaz. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teď vytvořte virtuální počítač s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) a určete soubor skriptu bash s `--custom-data simple_bash.sh` následujícím způsobem:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Ověření skriptu bash spustila
SSH na veřejnou IP adresu vašeho virtuálního počítače zobrazené ve výstupu z předchozí příkaz. Zadejte vlastní **publicIpAddress** následujícím způsobem:

```bash
ssh <publicIpAddress>
```

Změnit na **tmp** adresář a ověřte, že myScript.txt soubor existuje a má odpovídající text uvnitř této.  Pokud není, můžete zkontrolovat **/var/log/cloud-init.log** další podrobnosti.  Vyhledejte následující položky:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Další postup
Mezi další cloudu init změny konfigurace naleznete v následujících tématech:
 
- [Přidání další uživatele Linux do virtuálního počítače](cloudinit-add-user.md)
- [Spusťte Správce balíčků aktualizovat existující balíčky na při prvním spuštění](cloudinit-update-vm.md)
- [Změňte název místního hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfigurační soubory a vložit klíče](tutorial-automate-vm-deployment.md)