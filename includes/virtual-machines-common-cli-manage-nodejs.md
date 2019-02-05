---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 713068b940532331ee4a6c5f5a92816d328e41ec
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736099"
---
Než budete moct použít Azure CLI s příkazy a šablonami Resource Manageru k nasazení prostředků a úloh Azure pomocí skupin prostředků, budete potřebovat účet s Azure. Pokud účet nemáte, [tady můžete získat bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/).

Pokud jste ještě nenainstalovali Azure CLI a nepřipojili se ke svým předplatným, přečtěte si článek [Instalace Azure CLI](../articles/cli-install-nodejs.md), pomocí příkazu `azure config mode arm` nastavte režim na `arm` a pomocí příkazu `azure login` se připojte k Azure.

## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- Azure CLI classic – naše rozhraní příkazového řádku pro classic a resource management modelech nasazení (Tento článek)
- [Azure CLI](../articles/virtual-machines/linux/cli-manage.md) – naše nová generace rozhraní příkazového řádku pro model nasazení správy prostředků

## <a name="basic-azure-resource-manager-commands-in-azure-classic-cli"></a>Základní příkazy Azure Resource Manageru v klasickém rozhraní příkazového řádku Azure

Tento článek popisuje základní příkazy, které můžete použít ke správě a interakci s prostředky (hlavně virtuálními počítači) ve vašem předplatném Azure pomocí Azure classic CLI.  Podrobnější nápovědu ke konkrétním přepínačům a možnostem příkazového řádku můžete získat pomocí online nápovědy k příkazům a jejich možnostem zadáním `azure <command> <subcommand> --help` nebo `azure help <command> <subcommand>`.

> [!NOTE]
> Tyto příklady nezahrnují operace založené na šablonách, které se obecně doporučují pro nasazení virtuálních počítačů v Resource Manageru. Informace najdete ve článcích [Použití Azure CLI s Azure Resource Managerem](../articles/xplat-cli-azure-resource-manager.md) a [Nasazení a správa virtuálních počítačů pomocí šablon Azure Resource Manageru a Azure CLI](../articles/virtual-machines/linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

| Úkol | Resource Manager |
| --- | --- | --- |
| Vytvoření nejzákladnějšího virtuálního počítače |`azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Získejte hodnotu `image-urn` z příkazu `azure vm image list`. Příklady najdete v [tomto článku](../articles/virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).) |
| Vytvoření virtuálního počítače s Linuxem |`azure  vm create [options] <resource-group> <name> <location> -y "Linux"` |
| Vytvoření virtuálního počítače s Windows |`azure  vm create [options] <resource-group> <name> <location> -y "Windows"` |
| Výpis virtuálních počítačů |`azure  vm list [options]` |
| Získání informací o virtuálním počítači |`azure  vm show [options] <resource_group> <name>` |
| Spuštění virtuálního počítače |`azure vm start [options] <resource_group> <name>` |
| Zastavení virtuálního počítače |`azure vm stop [options] <resource_group> <name>` |
| Uvolnění virtuálního počítače |`azure vm deallocate [options] <resource-group> <name>` |
| Restartování virtuálního počítače |`azure vm restart [options] <resource_group> <name>` |
| Odstranění virtuálního počítače |`azure vm delete [options] <resource_group> <name>` |
| Zachycení virtuálního počítače |`azure vm capture [options] <resource_group> <name>` |
| Vytvoření virtuálního počítače z uživatelské image |`azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>` |
| Vytvoření virtuálního počítače ze specializovaného disku |`azure  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>` |
| Přidání datového disku k virtuálnímu počítači |`azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]` |
| Odebrání datového disku z virtuálního počítače |`azure  vm disk detach [options] <resource-group> <vm-name> <lun>` |
| Přidání obecného rozšíření do virtuálního počítače |`azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Přidání rozšíření přístupu virtuálních počítačů do virtuálního počítače |`azure vm reset-access [options] <resource-group> <name>` |
| Přidání rozšíření Dockeru do virtuálního počítače |`azure  vm docker create [options] <resource-group> <name> <location> <os-type>` |
| Odebrání rozšíření virtuálního počítače |`azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Získání využití prostředků virtuálního počítače |`azure vm list-usage [options] <location>` |
| Získání všech dostupných velikostí virtuálních počítačů |`azure vm sizes [options]` |

## <a name="next-steps"></a>Další postup
* Další příklady příkazů rozhraní příkazového řádku, které přesahují základní správu virtuálních počítačů, najdete v článku [Použití Azure CLI s Azure Resource Managerem](../articles/virtual-machines/azure-cli-arm-commands.md).
