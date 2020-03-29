---
title: Konfigurace odkládacího oddílu na virtuálním počítači s Linuxem pomocí cloud-initu
description: Jak používat cloud-init ke konfiguraci odkládacího oddílu ve virtuálním počítači SB Linux během vytváření pomocí rozhraní příkazového příkazu Azure
author: rickstercdn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1247652e536042ee249054d86aed3c3f8e7aa7bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969208"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>Konfigurace odkládacího oddílu na virtuálním počítači s Linuxem pomocí cloud-initu
Tento článek ukazuje, jak pomocí [cloud-init](https://cloudinit.readthedocs.io) nakonfigurovat odkládací oddíl na různých distribucích Linuxu. Odkládací oddíl byl tradičně konfigurován agentem Linuxu (WALA) na základě toho, které distribuce ji vyžadovaly.  Tento dokument bude nastínit proces pro vytváření odkládacího oddílu na vyžádání během zřizování čas pomocí cloud-init.  Další informace o tom, jak cloud-init funguje nativně v Azure a podporované distribuce Linuxu, najdete [v tématu cloud-init přehled](using-cloud-init.md)

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Vytvořte odkládací oddíl pro obrázky založené na Ubuntu
Ve výchozím nastavení v Azure nevytvářejí obrázky galerie Ubuntu odkládací oddíly. Chcete-li povolit konfiguraci odkládacího oddílu během doby zřizování virtuálních počítačů pomocí cloud-initu – přečtěte si [dokument AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) na wiki Ubuntu.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Vytvoření odkládacího oddílu pro obrázky založené na Red Hatu a CentOS

Vytvořte soubor v aktuálním prostředí s názvem *cloud_init_swappart.txt* a vložte následující konfiguraci. V tomto příkladu vytvořte soubor v prostředí Cloud Shell není v místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud_init_swappart.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Zvolte #1, abyste použili **editor nano.** Ujistěte se, že celý soubor cloud-init je zkopírován správně, zejména první řádek.  

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

Před nasazením této bitové kopie je třeba vytvořit skupinu prostředků pomocí příkazu [az group create.](/cli/azure/group) Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teď vytvořte virtuální hosti s [vytvořením virtuálního virtuálního virtuálního připojení a](/cli/azure/vm) zadejte soubor `--custom-data cloud_init_swappart.txt` cloud-init takto:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>Ověření vytvoření odkládacího oddílu
SSH na veřejnou IP adresu vašeho virtuálního počítače zobrazené ve výstupu z předchozího příkazu. Zadejte svůj vlastní **publicIpAddress** takto:

```bash
ssh <publicIpAddress>
```

Jakmile máte SSH'ed do virtuálního virtuálního disku, zkontrolujte, zda byl vytvořen odkládací oddíl

```bash
swapon -s
```

Výstup z tohoto příkazu by měl vypadat takto:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Pokud máte existující image Azure, která má nakonfigurovaný odkládací oddíl a chcete změnit konfiguraci odkládacího oddílu pro nové bitové kopie, měli byste odebrat existující odkládací oddíl. Další podrobnosti naleznete v dokumentu "Přizpůsobení obrázků pro poskytování pomocí cloud-init".

## <a name="next-steps"></a>Další kroky
Další příklady změn konfigurace cloud-init najdete v následujících tématech:
 
- [Přidání dalšího uživatele Linuxu do virtuálního počítače](cloudinit-add-user.md)
- [Spuštění správce balíčků pro aktualizaci existujících balíčků při prvním spuštění](cloudinit-update-vm.md)
- [Změna názvu místního hostitele virtuálního_kmontíva](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfiguračních souborů a vložení klíčů](tutorial-automate-vm-deployment.md)
