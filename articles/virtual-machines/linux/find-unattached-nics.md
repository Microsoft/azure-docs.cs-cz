---
title: Vyhledání a odstranění nepřipojených síťových karet Azure
description: Jak najít a odstranit síťové adaptéry Azure, které nejsou připojené k virtuálním počítačům pomocí Azure CLI
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 5b28226cfe6bc51a2619c4dd63e666ddd51dad2a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016195"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Jak vyhledat a odstranit nepřipojené síťové karty (nic) pro virtuální počítače Azure
Když odstraníte virtuální počítač v Azure, karty síťového rozhraní (nic) se ve výchozím nastavení neodstraní. Pokud vytvoříte a odstraníte víc virtuálních počítačů, nepoužívané síťové karty budou dál používat zapůjčení interních IP adres. Při vytváření jiných síťových adaptérů virtuálních počítačů nemusí být možné získat zapůjčení IP adres v adresním prostoru podsítě. V tomto článku se dozvíte, jak najít a odstranit nepřipojené síťové adaptéry.

## <a name="find-and-delete-unattached-nics"></a>Vyhledání a odstranění nepřipojených síťových adaptérů

Vlastnost *VirtualMachine* pro síťovou kartu ukládá ID a skupinu prostředků virtuálního počítače, ke kterému je připojená síťová karta. Následující skript projde všemi síťovými kartami v rámci předplatného a zkontroluje, jestli má vlastnost *VirtualMachine* hodnotu null. Pokud má tato vlastnost hodnotu null, síťové rozhraní není připojené k virtuálnímu počítači.

Pokud chcete zobrazit všechny nepřipojené síťové karty, důrazně doporučujeme spustit skript s proměnnou *deleteUnattachedNics* na *hodnotu 0*. Pokud chcete po kontrole výstupu seznamu odstranit všechny nepřipojené síťové rozhraní, spusťte skript s *deleteUnattachedNics* na *1*.

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

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak vytvářet a spravovat virtuální sítě v Azure, najdete v tématu [Vytvoření a správa sítí virtuálních počítačů](tutorial-virtual-network.md).
