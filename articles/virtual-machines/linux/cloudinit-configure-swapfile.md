---
title: Použití cloud-init pro konfigurace stránkovacího souboru na virtuálním počítači s Linuxem | Dokumentace Microsoftu
description: Použití cloud-init k konfigurace stránkovacího souboru v virtuálního počítače s Linuxem během vytváření pomocí Azure CLI
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
ms.openlocfilehash: 626fd4739daf2506854c42f16ac986a361ebab38
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55769908"
---
# <a name="use-cloud-init-to-configure-a-swapfile-on-a-linux-vm"></a>Konfigurace stránkovacího souboru na virtuálním počítači s Linuxem pomocí cloud-init
V tomto článku se dozvíte, jak používat [cloud-init](https://cloudinit.readthedocs.io) konfigurace stránkovacího souboru v různých distribucích systému Linux. Stránkovacího souboru byla nakonfigurována tradičně podle Linuxu agenta (WALA) podle distribuce, kterou vyžaduje jeden.  Tento dokument se popisují proces sestavení stránkovacího souboru na vyžádání během zřizování doby použití cloud-init.  Další informace o tom, jak funguje cloud-init nativně v Azure a podporovaných distribucích systému Linux, najdete v části [přehled cloud-init](using-cloud-init.md)

## <a name="create-swapfile-for-ubuntu-based-images"></a>Vytvořit stránkovacího souboru pro Image založenou na Ubuntu
Ve výchozím nastavení v Azure nevytvářejte Image z Galerie Ubuntu stránkovací soubory. Povolit konfiguraci odkládací soubor během zřizování čas použití cloud-init virtuálního počítače – podrobnosti najdete [AzureSwapPartitions dokumentu](https://wiki.ubuntu.com/AzureSwapPartitions) na stránkách wiki Ubuntu.

## <a name="create-swapfile-for-red-hat-and-centos-based-images"></a>Vytvořit stránkovacího souboru pro Image na základě CentOS a Red Hat

V aktuálním prostředí vytvořte soubor *cloud_init_swapfile.txt* a vložte do něj následující konfiguraci. V tomto příkladu vytvoření souboru ve službě Cloud Shell není na místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud_init_swapfile.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Zvolte #1 použít **nano** editoru. Ujistěte se, že se soubor celý cloud-init zkopíroval správně, zejména první řádek.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Před nasazením této bitové kopie, je potřeba vytvořit skupinu prostředků pomocí [vytvořit skupiny az](/cli/azure/group) příkazu. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teď vytvořte virtuální počítač s [az vm vytvořit](/cli/azure/vm) a zadejte soubor cloud-init s `--custom-data cloud_init_swapfile.txt` následujícím způsobem:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swapfile.txt \
  --generate-ssh-keys 
```

## <a name="verify-swapfile-was-created"></a>Ověřte, zda byl vytvořen stránkovacího souboru
Připojte přes SSH k veřejné IP adresu vašeho virtuálního počítače uvedené ve výstupu předchozího příkazu. Zadejte vlastní **publicIpAddress** následujícím způsobem:

```bash
ssh <publicIpAddress>
```

Jakmile budete mít SSH'ed k virtuálnímu počítači, zkontrolujte, zda byla vytvořena stránkovacího souboru

```bash
swapon -s
```

Výstup tohoto příkazu by měl vypadat takto:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Pokud máte stávající image Azure, která má nakonfigurované odkládacího souboru a chcete změnit konfiguraci odkládací soubor pro nové bitové kopie, byste měli odebrat existující odkládacího souboru. Najdete v dokumentu 'Vlastní Image k poskytování pomocí cloud-init' Další podrobnosti.

## <a name="next-steps"></a>Další postup
Příklady cloud-init další změny konfigurace najdete tady:
 
- [Přidání dalších uživatelů Linuxu na virtuální počítač](cloudinit-add-user.md)
- [Spusťte Správce balíčků aktualizovat existující balíčky při prvním spuštění](cloudinit-update-vm.md)
- [Změnit místní název hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizovat konfigurační soubory a klíče pro vložení](tutorial-automate-vm-deployment.md)
