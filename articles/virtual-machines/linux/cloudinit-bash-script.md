---
title: Použití cloud-init pro spuštění skriptu bash v virtuálního počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Jak používat cloud-init pro spuštění skriptu bash v virtuálního počítače s Linuxem během vytváření pomocí Azure CLI
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
ms.openlocfilehash: 4f65ebfd2e1ce508c5cf9b224871102a35b55fe0
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770313"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Použití cloud-init pro spuštění skriptu bash v virtuálního počítače s Linuxem v Azure
V tomto článku se dozvíte, jak používat [cloud-init](https://cloudinit.readthedocs.io) ke spuštění existujícího skriptu bash na virtuální počítač s Linuxem (VM) nebo škálovacích sad virtuálních počítačů (VMSS) při zřizování času v Azure. Tyto skripty cloud-init spustit při prvním spuštění, jakmile se zřizují prostředky Azure. Další informace o tom, jak funguje cloud-init nativně v Azure a podporovaných distribucích systému Linux, najdete v části [přehled cloud-init](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Spuštění skriptu bash pomocí cloud-init
Pomocí cloud-init, není potřeba převést stávající skripty konfigurace cloudu cloud-init akceptuje více vstupních typů, z nichž jeden je skriptu bash.

Pokud používáte rozšíření Linux vlastních skriptů Azure ke spouštění vašich skriptů, můžete je používat cloud-init migrovat. Ale rozšíření Azure jste integrovali odesílajících sestavy do upozornění na selhání skriptu, nasazení bitové kopie cloud-init nejsou při selhání Pokud skript selže.

Pokud chcete zobrazit tuto funkci v akci, vytvořte skript jednoduché bash pro testování. Cloud-init, jako jsou `#cloud-config` soubor, tento skript musí být místní vůči kde bude běžet AzureCLI příkazy ke zřízení virtuálního počítače.  V tomto příkladu vytvoření souboru ve službě Cloud Shell není na místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor simple_bash.sh` soubor vytvořte a zobrazte seznam editorů k dispozici. Zvolte #1 použít **nano** editoru. Ujistěte se, že se soubor celý cloud-init zkopíroval správně, zejména první řádek.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Před nasazením této bitové kopie, je potřeba vytvořit skupinu prostředků pomocí [vytvořit skupiny az](/cli/azure/group) příkazu. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teď vytvořte virtuální počítač s [az vm vytvořit](/cli/azure/vm) a zadat soubor skriptu bash s `--custom-data simple_bash.sh` následujícím způsobem:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Ověřte spuštění skriptu bash
Připojte přes SSH k veřejné IP adresu vašeho virtuálního počítače uvedené ve výstupu předchozího příkazu. Zadejte vlastní **publicIpAddress** následujícím způsobem:

```bash
ssh <publicIpAddress>
```

Přejděte **tmp** adresář a ověřte, že myScript.txt soubor existuje a má odpovídající text uvnitř této.  Pokud tomu tak není, můžete zkontrolovat **/var/log/cloud-init.log** další podrobnosti.  Vyhledejte následující položky:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Další postup
Příklady cloud-init další změny konfigurace najdete tady:
 
- [Přidání dalších uživatelů Linuxu na virtuální počítač](cloudinit-add-user.md)
- [Spusťte Správce balíčků aktualizovat existující balíčky při prvním spuštění](cloudinit-update-vm.md)
- [Změnit místní název hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizovat konfigurační soubory a klíče pro vložení](tutorial-automate-vm-deployment.md)
