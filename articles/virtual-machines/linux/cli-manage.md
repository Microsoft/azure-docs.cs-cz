---
title: Příkazy rozhraní příkazového řádku Azure
description: Naučte se některé běžné příkazy Azure CLI, abyste mohli začít spravovat virtuální počítače v režimu Azure Resource Manageru.
author: RicksterCDN
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 05/12/2017
ms.author: rclaus
ms.openlocfilehash: 253f2ab1b192d22f43e4082766adf4ec4f86fe71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969254"
---
# <a name="common-azure-cli-commands-for-managing-azure-resources"></a>Běžné příkazy Azure CLI pro správu prostředků

Azure CLI umožňuje vytvářet a spravovat prostředky Azure na macOS, Linux a Windows. Tento článek podrobně popisuje některé nejběžnější příkazy pro vytváření a správu virtuálních počítačů.

Tento článek vyžaduje Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete upgradovat, [přečtěte si informace o instalaci příkazového příkazového příkazu k webu Azure](/cli/azure/install-azure-cli). [Cloud Shell](/azure/cloud-shell/quickstart) můžete také používat z prohlížeče.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Základní příkazy Azure Resource Manageru v Azure CLI
Podrobnější nápovědu s konkrétními přepínači a možnostmi příkazového řádku získáte `az <command> <subcommand> --help`zadáním příkazu online .

### <a name="create-vms"></a>Vytvoření virtuálních počítačů
| Úkol | Příkazy Azure CLI |
| --- | --- |
| Vytvoření skupiny prostředků | `az group create --name myResourceGroup --location eastus` |
| Vytvoření virtuálního počítače s Linuxem | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Vytvoření virtuálního počítače s Windows | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>Správa stavu virtuálního mísy
| Úkol | Příkazy Azure CLI |
| --- | --- |
| Spuštění virtuálního počítače | `az vm start --resource-group myResourceGroup --name myVM` |
| Zastavení virtuálního počítače | `az vm stop --resource-group myResourceGroup --name myVM` |
| Uvolnění virtuálního počítače | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Restartování virtuálního počítače | `az vm restart --resource-group myResourceGroup --name myVM` |
| Znovunasazení virtuálního počítače | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Odstranění virtuálního počítače | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>Získání informací o virtuálním virtuálním účtu
| Úkol | Příkazy Azure CLI |
| --- | --- |
| Výpis virtuálních počítačů | `az vm list` |
| Získání informací o virtuálním počítači | `az vm show --resource-group myResourceGroup --name myVM` |
| Získání využití prostředků virtuálního počítače | `az vm list-usage --location eastus` |
| Získání všech dostupných velikostí virtuálních počítačů | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>Disky a obrázky
| Úkol | Příkazy Azure CLI |
| --- | --- |
| Přidání datového disku k virtuálnímu počítači | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| Odebrání datového disku z virtuálního počítače | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| Změna velikosti disku | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Pořízení snímku disku | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Vytvoření image virtuálního virtuálního mísa | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Vytvoření virtuálního virtuálního montovazy z bitové kopie | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>Další kroky
Další příklady příkazů příkazového příkazu příkazu cli najdete v tématu [vytváření a správa virtuálních počítačích SILinux pomocí kurzu Azure CLI.](tutorial-manage-vm.md)



