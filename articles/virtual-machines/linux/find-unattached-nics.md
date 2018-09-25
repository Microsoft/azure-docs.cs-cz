---
title: Vyhledání a odstranění nepřipojených síťových adaptérů Azure | Dokumentace Microsoftu
description: Tom, jak vyhledat a odstranit síťové adaptéry Azure, které nejsou připojené k virtuálním počítačům pomocí Azure CLI
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 9d8345aacb603a6411fdc693ac9f808778d27333
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954326"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Jak najít a odstranit nepřipojené síťové rozhraní (NIC) karty pro virtuální počítače Azure
Když odstraníte virtuální počítač (VM) v Azure, nebudou ve výchozím nastavení odstraněny karty síťového rozhraní (NIC). Je-li vytvořit a odstranit více virtuálních počítačů, nepoužívané síťové adaptéry dál používat interní zapůjčení IP adres. Při vytváření dalších síťových adaptérů virtuálního počítače, mohou nepovedlo se získat zapůjčení IP v adresním prostoru podsítě. V tomto článku se dozvíte, jak najít a odstranění nepřipojených síťových adaptérů.

## <a name="find-and-delete-unattached-nics"></a>Vyhledání a odstranění nepřipojených síťových adaptérů

*VirtualMachine* vlastnosti pro síťové rozhraní se ukládají ID a skupinu prostředků virtuálního počítače síťového adaptéru je připojen k. Tento skript projde všechny síťové adaptéry v rámci předplatného a ověří, zda *virtualMachine* vlastnost má hodnotu null. Pokud je tato vlastnost hodnotu null, síťového adaptéru není připojen k virtuálnímu počítači.

Chcete-li zobrazit všechny nepřipojených síťových adaptérů, ji má důrazně doporučujeme pro první spuštění skriptu pomocí *deleteUnattachedNics* proměnnou *0*. Odstranění nepřipojených síťových adaptérů, poté, co si výstup seznamu, spusťte skript s *deleteUnattachedNics* k *1*.

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>Další postup

Další informace o tom, jak vytvářet a spravovat virtuální sítě v Azure najdete v tématu [vytváření a správě sítí virtuálních počítačů](tutorial-virtual-network.md).
