---
title: Vyhledejte a odstraňte Odpojit síťové adaptéry s funkcí Azure | Microsoft Docs
description: Postup nalezení a odstranění Azure síťové adaptéry, které nejsou připojeny k virtuálním počítačům s 2.0 rozhraní příkazového řádku Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: c730866fe73305a37b37038699a7f729085a16aa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Postup nalezení a odstranění Odpojit síťové karty pro virtuální počítače Azure
Při odstranění virtuálního počítače (VM) v Azure, nebudou odstraněny karty síťového rozhraní (NIC) ve výchozím nastavení. Je-li vytvářet a odstraňovat víc virtuálních počítačů, nepoužívané síťové adaptéry nadále používat interní zapůjčení IP adres. Při vytváření další síťové adaptéry virtuálních počítačů, mohou nelze získat zapůjčení IP do adresního prostoru podsítě. Tento článek ukazuje, jak vyhledat a odstranit Odpojit síťové adaptéry.

## <a name="find-and-delete-unattached-nics"></a>Vyhledejte a odstraňte Odpojit síťové karty

*VirtualMachine* vlastnosti pro síťový adaptér se ukládají skupině ID a prostředky virtuálního počítače na síťový adaptér je připojen k. Následující skript projde všechny síťové adaptéry v předplatném a ověří, zda *virtualMachine* vlastnost má hodnotu null. Pokud je tato vlastnost hodnotu null, síťový adaptér není připojen k virtuálnímu počítači.

Chcete-li zobrazit všechny odpojit síťové adaptéry, ho má důrazně doporučujeme pro první spuštění skriptu pomocí *deleteUnattachedNics* proměnnou *0*. Chcete-li odstranit Odpojit síťové adaptéry, poté, co si výstup seznamu, spusťte skript s *deleteUnattachedNics* k *1*.

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

Další informace o tom, jak vytvořit a spravovat virtuální sítě v Azure najdete v tématu [vytvoření a správě sítí virtuálních počítačů](tutorial-virtual-network.md).
