---
title: Příkazy rozhraní příkazového řádku Azure
description: Seznamte se s některými běžnými příkazy Azure CLI, které vám pomohou začít spravovat vaše virtuální počítače v režimu Azure Resource Manager.
author: RicksterCDN
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 05/12/2017
ms.author: rclaus
ms.openlocfilehash: 5a9dd8aaeed0642461e4244a72a3dab5c96a77b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87372242"
---
# <a name="common-azure-cli-commands-for-managing-azure-resources"></a>Běžné příkazy Azure CLI pro správu prostředků

Rozhraní příkazového řádku Azure umožňuje vytvářet a spravovat prostředky Azure v systémech macOS, Linux a Windows. Tento článek podrobně popisuje některé nejběžnější příkazy pro vytváření a správu virtuálních počítačů (VM).

Tento článek vyžaduje Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete upgrade, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli). [Cloud Shell](../../cloud-shell/quickstart.md) můžete použít také v prohlížeči.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Základní příkazy Azure Resource Manageru v Azure CLI
Podrobnější nápovědu ke konkrétním přepínačům a možnostem příkazového řádku najdete v online nápovědě k příkazům a možnostech zadáním `az <command> <subcommand> --help` .

### <a name="create-vms"></a>Vytvoření virtuálních počítačů
| Úloha | Příkazy Azure CLI |
| --- | --- |
| Vytvoření skupiny prostředků | `az group create --name myResourceGroup --location eastus` |
| Vytvoření virtuálního počítače s Linuxem | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Vytvoření virtuálního počítače s Windows | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>Správa stavu virtuálního počítače
| Úloha | Příkazy Azure CLI |
| --- | --- |
| Spuštění virtuálního počítače | `az vm start --resource-group myResourceGroup --name myVM` |
| Zastavení virtuálního počítače | `az vm stop --resource-group myResourceGroup --name myVM` |
| Uvolnění virtuálního počítače | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Restartování virtuálního počítače | `az vm restart --resource-group myResourceGroup --name myVM` |
| Znovunasazení virtuálního počítače | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Odstranění virtuálního počítače | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>Získat informace o virtuálním počítači
| Úloha | Příkazy Azure CLI |
| --- | --- |
| Výpis virtuálních počítačů | `az vm list` |
| Získání informací o virtuálním počítači | `az vm show --resource-group myResourceGroup --name myVM` |
| Získání využití prostředků virtuálního počítače | `az vm list-usage --location eastus` |
| Získání všech dostupných velikostí virtuálních počítačů | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>Disky a image
| Úloha | Příkazy Azure CLI |
| --- | --- |
| Přidání datového disku k virtuálnímu počítači | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| Odebrání datového disku z virtuálního počítače | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| Změna velikosti disku | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Pořízení snímku disku | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Vytvoření image virtuálního počítače | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Vytvoření virtuálního počítače z Image | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>Další kroky
Další příklady příkazů rozhraní příkazového řádku najdete v kurzu [vytváření a správa virtuálních počítačů se systémem Linux pomocí kurzu Azure CLI](tutorial-manage-vm.md) .
