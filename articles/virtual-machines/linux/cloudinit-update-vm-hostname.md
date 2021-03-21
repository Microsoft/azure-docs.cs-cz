---
title: Použití Cloud-init k nastavení názvu hostitele pro virtuální počítač Linux
description: Použití Cloud-init k přizpůsobení virtuálního počítače se systémem Linux během vytváření pomocí Azure CLI
author: rickstercdn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 5c3195fd338d17e2dae3ff2aadfb90790519715b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559388"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Použití Cloud-init k nastavení názvu hostitele pro virtuální počítač Linux v Azure
V tomto článku se dozvíte, jak pomocí [Cloud-init](https://cloudinit.readthedocs.io) nakonfigurovat konkrétní název hostitele na virtuálním počítači (VM) nebo Virtual Machine Scale Sets (VMSS) v době zřizování v Azure. Tyto skripty Cloud-init se spouštějí při prvním spuštění, jakmile se prostředky zřídí v Azure. Další informace o tom, jak nativně funguje Cloud-init v Azure a podporované distribuce Linux, najdete v článku [Přehled Cloud-init](using-cloud-init.md) .

## <a name="set-the-hostname-with-cloud-init"></a>Nastavení názvu hostitele pomocí Cloud-init
Ve výchozím nastavení je název hostitele stejný jako název virtuálního počítače při vytváření nového virtuálního počítače v Azure.  Pokud chcete spustit skript Cloud-init pro změnu tohoto výchozího názvu hostitele při vytváření virtuálního počítače v Azure pomocí [AZ VM Create](/cli/azure/vm), zadejte soubor Cloud-init s `--custom-data` přepínačem.  

Pokud chcete zobrazit proces upgradu v akci, vytvořte v aktuálním prostředí soubor s názvem *cloud_init_hostname.txt* a vložte následující konfiguraci. V tomto příkladu vytvořte soubor v Cloud Shell ne na vašem místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud_init_hostname.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Vyberte #1 pro použití editoru **nano** . Přesvědčte se, zda je celý soubor Cloud-init zkopírován správně, zejména první řádek.  

```yaml
#cloud-config
hostname: myhostname
```

Před nasazením této image je potřeba vytvořit skupinu prostředků pomocí příkazu [AZ Group Create](/cli/azure/group) . Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nyní vytvořte virtuální počítač pomocí příkazu [AZ VM Create](/cli/azure/vm) a zadejte soubor Cloud-init následujícím `--custom-data cloud_init_hostname.txt` způsobem:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Po vytvoření Azure CLI zobrazí informace o virtuálním počítači. Použijte `publicIpAddress` k vašemu virtuálnímu počítači protokol SSH. Následujícím způsobem zadejte vlastní adresu:

```bash
ssh <publicIpAddress>
```

Chcete-li zobrazit název virtuálního počítače, použijte `hostname` příkaz následujícím způsobem:

```bash
hostname
```

Virtuální počítač by měl nahlásit název hostitele jako hodnotu nastavenou v souboru Cloud-init, jak je znázorněno v následujícím příkladu výstupu:

```bash
myhostname
```

## <a name="next-steps"></a>Další kroky
Další příklady cloudových inicializací změn konfigurace najdete v následujících tématech:
 
- [Přidání dalšího uživatele se systémem Linux k virtuálnímu počítači](cloudinit-add-user.md)
- [Spusťte Správce balíčků, aby při prvním spuštění aktualizoval existující balíčky.](cloudinit-update-vm.md)
- [Změnit místní název hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfiguračních souborů a vkládání klíčů](tutorial-automate-vm-deployment.md)
