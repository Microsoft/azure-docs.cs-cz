---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows, azure-resource-manager
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/14/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: f403e060859df6d1de96a3c0d478d57df2677eee
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "31531051"
---
Následují obvyklé scénáře přidělení, které způsobují požadavek na přidělení na připnout. Každý scénář dále v tomto článku začneme budete zabývat.

- Změna velikosti virtuálního počítače nebo přidat virtuální počítače nebo instance rolí do existující cloudové služby
- Restartování virtuálních počítačů částečně zastaveno (přidělení zrušeno)
- Restartování virtuálních počítačů plně zastaveno (přidělení zrušeno)
- Nasazení pracovního a produkčního prostředí (platforma jako služba jenom)
- Skupina vztahů (virtuální počítač nebo služba blízkosti)
- Spřažení – na základě skupin virtuální sítě

Pokud obdržíte chybu přidělování, zkontrolujte, zda některý z uvedených scénářů platí pro chyby. Chyba přidělení, která je vrácena platformou Azure k identifikaci odpovídající scénář použití. Pokud vaše žádost je připnutá, odeberte některé Připnutí omezení otevřete požadavek do více clusterů, čímž roste pravděpodobnost úspěchu přidělení.
Obecně platí Pokud se chyba, že "požadovanou velikost virtuálního počítače se nepodporuje" neuvádí, můžete vždy opakovat později. Je to proto dostatek prostředků byl pravděpodobně uvolněn v clusteru tak, aby vyhovovaly vaší žádosti. Pokud tento problém je, že požadovaná velikost virtuálního počítače nepodporuje, zkuste jinou velikost virtuálního počítače. V opačném případě je jediná možnost odebrat Připnutí omezení.

Dvě běžné scénáře selhání souvisí se skupiny vztahů. V minulosti skupinu vztahů byl použit k poskytování těsné blízkosti virtuální počítače a instance služby nebo byl použit a tím umožňují vytváření virtuální sítě. Se zavedením regionálních služeb virtual Network skupiny vztahů už nejsou potřeba k vytvoření virtuální sítě. Snížení latence sítě v Azure infrastruktury byl změněn doporučení pro skupiny vztahů používat pro virtuální počítače nebo služby blízkosti.

V níže uvedeném diagramu taxonomie scénáře (připojené) přidělení. 

![Taxonomie připojené přidělení](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Změna velikosti virtuálního počítače nebo přidat virtuální počítače nebo instance rolí do existující cloudové služby
**Chyba**

Upgrade_VMSizeNotSupported nebo GeneralError

**Příčinu Připnutí clusteru**

Žádost o změně velikosti virtuálního počítače nebo přidání virtuálního počítače nebo role instance do existující cloudové služby, musí se pokusit v původním clusteru, který je hostitelem existující cloudovou službu. Vytváří se nová Cloudová služba umožňuje platformě Azure najít jiného clusteru, který má volné prostředky nebo podporuje velikost virtuálního počítače, kterou jste si vyžádali.

**Alternativní řešení**

Pokud je chyba Upgrade_VMSizeNotSupported *, zkuste jinou velikost virtuálního počítače. Pokud pomocí jinou velikost virtuálního počítače není možné zvolit, ale je nepřijatelné využívat jiné virtuální adresa IP (VIP), vytvořte novou cloudovou službu k hostování nového virtuálního počítače a přidejte novou cloudovou službu na regionální virtuální síť, ve kterém jsou existující virtuální počítače spuštěné. Pokud váš existující cloudovou službu nepoužívá regionální virtuální síť, můžete stále vytvořit novou virtuální síť pro novou cloudovou službu a připojte vaše [existující virtuální sítě pro nové virtuální sítě](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Přečtěte si více o [regionálních virtuálních sítích](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Pokud je chyba GeneralError *, je pravděpodobné, že typ prostředku (třeba konkrétní velikosti virtuálního počítače) se podporuje v clusteru, ale clusteru nemá žádné volné prostředky v tuto chvíli. Podobně jako výše popsaném scénáři, přidejte požadované výpočetní prostředek procesem vytvoření nové cloudové služby (Všimněte si, že má novou cloudovou službu k použití různých virtuálních IP adres) a regionální virtuální síť používat pro připojení cloudových služeb.

## <a name="restart-partially-stopped-deallocated-vms"></a>Restartování virtuálních počítačů částečně zastaveno (přidělení zrušeno)
**Chyba**

GeneralError *

**Příčinu Připnutí clusteru**

Částečné dealokace znamená, že zastaveno (přidělení zrušeno) jednu nebo víc, ale ne všechny virtuální počítače v cloudové službě. Když zastavíte (uvolníte) virtuálního počítače, se vydávají přidružené prostředky. Zastaveno (přidělení zrušeno) virtuální počítač se restartuje, tedy novou žádost o přidělení. Restartování virtuálních počítačů v částečně uvolněno cloudové službě je ekvivalentní k přidávání virtuálních počítačů do existující cloudové služby. Požadavek na přidělení má se pokusit v původním clusteru, který je hostitelem existující cloudovou službu. Vytváření jinou cloudovou službu umožňuje platformě Azure najít jiného clusteru, který je bezplatný zdroj nebo podporuje požadovaná velikost virtuálního počítače.

**Alternativní řešení**

Pokud je akceptovatelná podle použití různých virtuálních IP adres, odstraňte zastavené (přidělení zrušeno) virtuální počítače (ale ponechat přidružené disky) a přidejte virtuální počítače zpět prostřednictvím jinou cloudovou službu. Regionální virtuální síť použijte pro připojení cloudových služeb:

* Pokud váš existující cloudovou službu používá regionální virtuální síť, jednoduše přidejte novou cloudovou službu do stejné virtuální síti.
* Pokud vaše existující cloudovou službu nepoužívá regionální virtuální síť, vytvořte novou virtuální síť pro novou cloudovou službu a potom [připojte existující virtuální síti novou virtuální síť](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Přečtěte si více o [regionálních virtuálních sítích](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Restartování virtuálních počítačů plně zastaveno (přidělení zrušeno)
**Chyba**

GeneralError *

**Příčinu Připnutí clusteru**

Úplné zrušení přidělení znamená, že jste zastavili (přidělení zrušeno) všech virtuálních počítačů z cloudové služby. Žádosti o přidělení k restartování těchto virtuálních počítačů, třeba se pokusit v původním clusteru, který je hostitelem cloudovou službu. Vytváří se nová Cloudová služba umožňuje platformě Azure najít jiného clusteru, který má volné prostředky nebo podporuje velikost virtuálního počítače, kterou jste si vyžádali.

**Alternativní řešení**

Pokud je akceptovatelná podle použití různých virtuálních IP adres, odstraňte původní zastaveno (přidělení zrušeno) virtuálních počítačů (ale ponechat přidružené disky) a odstranění odpovídající cloudové služby (přidružené výpočetní prostředky, které byly již uvolní, když je zastaveno (přidělení zrušeno) virtuálních počítačů). Vytvořte novou cloudovou službu, chcete-li přidat virtuální počítače zpět.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Pracovní nebo produkční nasazení (platforma jako služba jenom)
**Chyba**

New_General * nebo New_VMSizeNotSupported *

**Příčinu Připnutí clusteru**

Nasazení pracovního a produkčního nasazení cloudové služby jsou hostované ve stejném clusteru. Při přidání druhé nasazení odpovídající požadavek na přidělení se pokusí vytvořit ve stejném clusteru, který je hostitelem prvním nasazení.

**Alternativní řešení**

Odstraňte první nasazení a původní cloudovou službu a znovu nasadit cloudovou službu. Tato akce může být umístěn prvním nasazení v clusteru, který má dost volných prostředků podle obě nasazení nebo v clusteru, který podporuje velikosti virtuálních počítačů, které jste požádali.

## <a name="affinity-group-vmservice-proximity"></a>Skupina vztahů (blízkosti virtuálního počítače nebo služby)
**Chyba**

New_General * nebo New_VMSizeNotSupported *

**Příčinu Připnutí clusteru**

Všechny výpočetní zdroj přiřazený do skupiny vztahů je vázán na jednom clusteru. V této skupině vztahů nedochází k pokusům o ve stejném clusteru, kde se hostují stávající prostředky požádá o nové výpočetní prostředek. To platí, ať už se vytvoří nové prostředky prostřednictvím novou cloudovou službu nebo existující cloudovou službu.

**Alternativní řešení**

Pokud skupinu vztahů, není nutné, použijte skupinu vztahů, ani seskupení výpočetní prostředky do více skupin vztahů.

## <a name="affinity-group-based-virtual-network"></a>Na základě přidružení skupin virtuální sítě
**Chyba**

New_General * nebo New_VMSizeNotSupported *

**Příčinu Připnutí clusteru**

Před zavedením regionálních virtuálních sítích, bylo potřeba virtuální síť přidružit k skupinu vztahů. V důsledku toho výpočetní prostředky, které umístí do skupiny vztahů jsou vázány stejná omezení, jak je popsáno v "přidělení scénář: Skupina vztahů (virtuální počítač nebo službu do blízkosti)" výše uvedené části. Výpočetní prostředky, které jsou vázané na jednom clusteru.

**Alternativní řešení**

Pokud nepotřebujete skupinu vztahů, vytvořte novou regionální virtuální síť pro nové prostředky, které přidáváte, a potom [připojte existující virtuální síti novou virtuální síť](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Přečtěte si více o [regionálních virtuálních sítích](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Alternativně můžete [migraci virtuální sítě se na základě přidružení skupin k regionální virtuální síť](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)a poté znovu přidejte požadované prostředky.
