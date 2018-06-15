---
title: Použít cloudové init ke konfiguraci swapfile na virtuální počítač s Linuxem | Microsoft Docs
description: Jak používat cloudové init ke konfiguraci swapfile v virtuálního počítače s Linuxem během vytváření pomocí Azure CLI 2.0
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
ms.openlocfilehash: 88a141922f113caf7ad67c89de48f84a821f7ba3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
ms.locfileid: "29952594"
---
# <a name="use-cloud-init-to-configure-a-swapfile-on-a-linux-vm"></a>Ke konfiguraci swapfile na virtuální počítač s Linuxem použít init cloudu
V tomto článku se dozvíte, jak používat [cloudu init](https://cloudinit.readthedocs.io) ke konfiguraci swapfile na různých distribucí Linux. Swapfile tradičně byla nakonfigurována pomocí Linux agenta (WALA) podle které distribuce vyžaduje jeden.  Tento dokument se popisují proces tvorby swapfile na vyžádání při zřizování dobu používání cloudové init.  Další informace o cloudu init fungování nativně ve službě Azure a podporovaných distribucích systému Linux najdete v tématu [init cloudu – přehled](using-cloud-init.md)

## <a name="create-swapfile-for-ubuntu-based-images"></a>Vytvoření swapfile pro Ubuntu na základě bitové kopie
Ve výchozím nastavení v Azure nevytvářejte Ubuntu Galerie obrázků stránkovacích souborů. Povolit odkládací soubor konfigurace během zřizování dobu používání init cloudu virtuálních počítačů-najdete [AzureSwapPartitions dokumentu](https://wiki.ubuntu.com/AzureSwapPartitions) na stránkách wiki Ubuntu.

## <a name="create-swapfile-for-redhat-and-centos-based-images"></a>Vytvoření swapfile pro RedHat a CentOS na základě bitové kopie

Vytvořte soubor ve své aktuální prostředí s názvem *cloud_init_swapfile.txt* a vložte následující konfigurace. V tomto příkladu vytvoření souboru v prostředí cloudu není na místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud_init_swapfile.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Zvolte #1 používat **nano** editor. Ujistěte se, že je soubor celou cloudu init zkopírován správně, obzvláště první řádek.  

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

Než nasadíte tuto bitovou kopii, je nutné vytvořit skupinu prostředků s [vytvořit skupinu az](/cli/azure/group#az_group_create) příkaz. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teď vytvořte virtuální počítač s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) a určete soubor init cloudu s `--custom-data cloud_init_swapfile.txt` následujícím způsobem:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swapfile.txt \
  --generate-ssh-keys 
```

## <a name="verify-swapfile-was-created"></a>Ověřte, zda byl vytvořen swapfile
SSH na veřejnou IP adresu vašeho virtuálního počítače zobrazené ve výstupu z předchozí příkaz. Zadejte vlastní **publicIpAddress** následujícím způsobem:

```bash
ssh <publicIpAddress>
```

Až budete mít SSH'ed do virtuálního počítače, zkontrolujte, zda byla vytvořena swapfile

```bash
swapon -s
```

Výstup tohoto příkazu by měl vypadat takto:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Pokud máte stávající image Azure, který má odkládací soubor nakonfigurovaný a chcete změnit konfiguraci odkládací soubor pro nové bitové kopie, byste měli odebrat existující odkládací soubor. Najdete v dokumentu 'Přizpůsobit Image zřídit podle cloudu init' Další podrobnosti.

## <a name="next-steps"></a>Další kroky
Mezi další cloudu init změny konfigurace naleznete v následujících tématech:
 
- [Přidání další uživatele Linux do virtuálního počítače](cloudinit-add-user.md)
- [Spusťte Správce balíčků aktualizovat existující balíčky na při prvním spuštění](cloudinit-update-vm.md)
- [Změňte název místního hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfigurační soubory a vložit klíče](tutorial-automate-vm-deployment.md)
