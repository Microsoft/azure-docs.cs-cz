---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e08e6ca4f6413b475bd8a0e5ff30b74921c2f0e6
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227238"
---
Rozhraní příkazového řádku Azure umožňuje vytvářet a spravovat prostředky Azure v systémech macOS, Linux a Windows. Tento článek podrobně popisuje některé z nejčastěji používané příkazy k vytvoření a správa virtuálních počítačů (VM).

Tento článek vyžaduje použití Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete upgrade, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli). Můžete také použít [Cloud Shell](/azure/cloud-shell/quickstart) z prohlížeče.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Základní příkazy Azure Resource Manageru v Azure CLI
Podrobnější nápovědu přepínače konkrétní příkazového řádku a možnosti, můžete použít možnosti online nápovědy k příkazům a zadáním `az <command> <subcommand> --help`.

### <a name="create-vms"></a>Vytvoření virtuálních počítačů
| Úkol | Příkazy Azure CLI |
| --- | --- |
| Vytvoření skupiny prostředků | `az group create --name myResourceGroup --location eastus` |
| Vytvoření virtuálního počítače s Linuxem | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Vytvoření virtuálního počítače s Windows | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>Správa stavu virtuálního počítače
| Úkol | Příkazy Azure CLI |
| --- | --- |
| Spuštění virtuálního počítače | `az vm start --resource-group myResourceGroup --name myVM` |
| Zastavení virtuálního počítače | `az vm stop --resource-group myResourceGroup --name myVM` |
| Uvolnění virtuálního počítače | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Restartování virtuálního počítače | `az vm restart --resource-group myResourceGroup --name myVM` |
| Znovunasazení virtuálního počítače | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Odstranění virtuálního počítače | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>Získání informací o virtuálním počítači
| Úkol | Příkazy Azure CLI |
| --- | --- |
| Výpis virtuálních počítačů | `az vm list` |
| Získání informací o virtuálním počítači | `az vm show --resource-group myResourceGroup --name myVM` |
| Získání využití prostředků virtuálního počítače | `az vm list-usage --location eastus` |
| Získání všech dostupných velikostí virtuálních počítačů | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>Disky a Image
| Úkol | Příkazy Azure CLI |
| --- | --- |
| Přidání datového disku k virtuálnímu počítači | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new ` |
| Odebrání datového disku z virtuálního počítače | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| Změna velikosti disku | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Pořízení snímku disku | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Vytvoření image virtuálního počítače | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Vytvoření virtuálního počítače z image | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>Další postup
Další příklady příkazů rozhraní příkazového řádku, najdete v článku [vytvořit a spravovat virtuální počítače s Linuxem pomocí Azure CLI](../articles/virtual-machines/linux/tutorial-manage-vm.md) kurzu.

