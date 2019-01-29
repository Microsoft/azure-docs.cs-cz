---
title: Pomocí cloud-init můžete nastavit název hostitele pro virtuální počítač s Linuxem v Azure | Dokumentace Microsoftu
description: Použití cloud-init k přizpůsobení virtuálního počítače s Linuxem během vytváření pomocí Azure CLI
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
ms.openlocfilehash: da20c4b30e2708bf7754d025cfbd2c269c3b5c7c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164185"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Pomocí cloud-init můžete nastavit název hostitele pro virtuální počítač s Linuxem v Azure
V tomto článku se dozvíte, jak používat [cloud-init](https://cloudinit.readthedocs.io) nakonfigurovat konkrétní název hostitele na virtuální počítač (VM) nebo virtuální počítače škálovacích sad (VMSS) zřizování času v Azure. Tyto skripty cloud-init spustit při prvním spuštění, jakmile se zřizují prostředky Azure. Další informace o tom, jak funguje cloud-init nativně v Azure a podporovaných distribucích systému Linux, najdete v části [přehled cloud-init](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Nastavit název hostitele pomocí cloud-init
Ve výchozím nastavení, název hostitele je stejný jako název virtuálního počítače při vytváření nového virtuálního počítače v Azure.  Pro spuštění skriptu cloud-init, chcete-li změnit toto výchozí název hostitele při vytváření virtuálního počítače v Azure pomocí [az vm vytvořit](/cli/azure/vm), zadejte soubor cloud-init s `--custom-data` přepnout.  

Pokud chcete zobrazit procesu upgradu v akci, vytvořte soubor v aktuálním prostředí *cloud_init_hostname.txt* a vložte do něj následující konfiguraci. V tomto příkladu vytvoření souboru ve službě Cloud Shell není na místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud_init_hostname.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Zvolte #1 použít **nano** editoru. Ujistěte se, že se soubor celý cloud-init zkopíroval správně, zejména první řádek.  

```yaml
#cloud-config
hostname: myhostname
```

Před nasazením této bitové kopie, je potřeba vytvořit skupinu prostředků pomocí [vytvořit skupiny az](/cli/azure/group) příkazu. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teď vytvořte virtuální počítač s [az vm vytvořit](/cli/azure/vm) a zadejte soubor cloud-init s `--custom-data cloud_init_hostname.txt` následujícím způsobem:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Po vytvoření v Azure CLI zobrazí informace o virtuálním počítači. Použití `publicIpAddress` pro připojení SSH k virtuálnímu počítači. Zadejte vlastní adresu následujícím způsobem:

```bash
ssh <publicIpAddress>
```

Chcete-li zobrazit název virtuálního počítače, použijte `hostname` takto:

```bash
hostname
```

Virtuální počítač by měl vykazovat název hostitele jak tuto hodnotu nastavenou v souboru cloud-init, jak je znázorněno v následujícím příkladu výstupu:

```bash
myhostname
```

## <a name="next-steps"></a>Další postup
Příklady cloud-init další změny konfigurace najdete tady:
 
- [Přidání dalších uživatelů Linuxu na virtuální počítač](cloudinit-add-user.md)
- [Spusťte Správce balíčků aktualizovat existující balíčky při prvním spuštění](cloudinit-update-vm.md)
- [Změnit místní název hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizovat konfigurační soubory a klíče pro vložení](tutorial-automate-vm-deployment.md)
