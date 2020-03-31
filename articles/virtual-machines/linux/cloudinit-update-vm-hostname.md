---
title: Nastavení názvu hostitele pro virtuální počítač s Linuxem pomocí cloud-initu
description: Jak používat cloud-init k přizpůsobení virtuálního počítače s Linuxem během vytváření pomocí azure CLI
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 631b8ef83d5fbf10ec401df7432b23238f2ae2e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969170"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Nastavení názvu hostitele pro virtuální počítač s Linuxem v Azure pomocí cloud-initu
Tento článek ukazuje, jak pomocí [cloud-init](https://cloudinit.readthedocs.io) ukonfigurovat konkrétní název hostitele na virtuálním počítači (VM) nebo škálovací sady virtuálních strojů (VMSS) v době zřizování v Azure. Tyto skripty cloud-init se spustí při prvním spuštění, jakmile jsou prostředky zřízeny Azure. Další informace o tom, jak cloud-init funguje nativně v Azure a podporované distribuce Linuxu, najdete [v tématu cloud-init přehled](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Nastavení názvu hostitele pomocí cloud-init
Ve výchozím nastavení je název hostitele stejný jako název virtuálního počítače při vytváření nového virtuálního počítače v Azure.  Chcete-li spustit skript cloud-init a změnit tento výchozí název hostitele při vytváření virtuálního počítače v Azure `--custom-data` s [vytvořením az vm](/cli/azure/vm), zadejte soubor cloud-init pomocí přepínače.  

Chcete-li zobrazit proces upgradu v akci, vytvořte soubor v aktuálním prostředí s názvem *cloud_init_hostname.txt* a vložte následující konfiguraci. V tomto příkladu vytvořte soubor v prostředí Cloud Shell není v místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud_init_hostname.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Zvolte #1, abyste použili **editor nano.** Ujistěte se, že celý soubor cloud-init je zkopírován správně, zejména první řádek.  

```yaml
#cloud-config
hostname: myhostname
```

Před nasazením této bitové kopie je třeba vytvořit skupinu prostředků pomocí příkazu [az group create.](/cli/azure/group) Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teď vytvořte virtuální hosti s [vytvořením virtuálního virtuálního virtuálního připojení a](/cli/azure/vm) zadejte soubor `--custom-data cloud_init_hostname.txt` cloud-init takto:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Po vytvoření Azure CLI zobrazí informace o virtuálním počítači. Použijte `publicIpAddress` SSH k virtuálnímu počítači. Zadejte svou vlastní adresu takto:

```bash
ssh <publicIpAddress>
```

Chcete-li zobrazit název `hostname` virtuálního_

```bash
hostname
```

Virtuální ho virtuálního zařízení by měl hlásit název hostitele jako tuto hodnotu nastavenou v souboru cloud-init, jak je znázorněno v následujícím příkladu výstupu:

```bash
myhostname
```

## <a name="next-steps"></a>Další kroky
Další příklady změn konfigurace cloud-init najdete v následujících tématech:
 
- [Přidání dalšího uživatele Linuxu do virtuálního počítače](cloudinit-add-user.md)
- [Spuštění správce balíčků pro aktualizaci existujících balíčků při prvním spuštění](cloudinit-update-vm.md)
- [Změna názvu místního hostitele virtuálního_kmontíva](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfiguračních souborů a vložení klíčů](tutorial-automate-vm-deployment.md)
