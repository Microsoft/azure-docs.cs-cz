---
title: Vyhledání a odstranění nepřipojených síťové karty Azure
description: Jak najít a odstranit síťové karty Azure, které nejsou připojené k virtuálním počítačům pomocí příkazového příkazu k onomu Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 8142b95ee666e205a8328eafd5930f1f386e49af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945124"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Jak najít a odstranit nepřipojené karty síťového rozhraní (NIC) pro virtuální počítače Azure
Když odstraníte virtuální počítač (VM) v Azure, karty síťového rozhraní (NIC) nejsou odstraněny ve výchozím nastavení. Pokud vytvoříte a odstraníte více virtuálních počítačů, nepoužívané síťové karty nadále používají interní zapůjčení IP adres. Při vytváření dalších síťových rozhraní virtuálních počítačů nemusí být možné získat zapůjčení protokolu IP v adresním prostoru podsítě. Tento článek ukazuje, jak najít a odstranit nepřipojené síťové karty.

## <a name="find-and-delete-unattached-nics"></a>Vyhledání a odstranění nepřipojených síťových adaptérů

Vlastnost *virtualMachine* pro nic ukládá ID a skupinu prostředků virtuálního počítače, ke kterému je připojena nic. Následující skript smyčky přes všechny síťové karty v předplatném a zkontroluje, zda *virtualMachine* vlastnost je null. Pokud tato vlastnost je null, nic není připojen k virtuálnímu virtuálnímu soudu.

Chcete-li zobrazit všechny nepřipojené síťové karty, důrazně doporučujeme nejprve spustit skript s proměnnou *deleteUnattachedNics* na *0*. Chcete-li po kontrole výstupu seznamu odstranit všechny nepřipojené síťové karty, spusťte skript s *příkazem deleteUnattachedNics* na *1*.

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

Další informace o tom, jak vytvářet a spravovat virtuální sítě v Azure, najdete v tématu [vytváření a správa sítí virtuálních počítače](tutorial-virtual-network.md).
