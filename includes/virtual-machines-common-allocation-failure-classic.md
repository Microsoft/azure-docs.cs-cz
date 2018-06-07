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
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "31531051"
---
Tady uvádíme běžné scénáře přidělení, které způsobí požadavku na přidělení být připojena. Můžeme vám podrobně každý scénář dále v tomto článku.

- Změnit velikost virtuálního počítače nebo přidat virtuální počítače nebo instance rolí do existující cloudové služby
- Restartování částečně zastavena (deallocated) virtuálních počítačů
- Restartujte plně zastaveném (deallocated) virtuálních počítačů
- Pracovní a provozní nasazení (platforma jako služba pouze)
- Skupina vztahů (virtuálního počítače nebo služby blízkosti)
- Spřažení – na základě skupin virtuální sítě

Pokud obdržíte chybu přidělení, zkontrolujte, zda kterýkoli z uvedených scénářů platí na vaše chybu. Použijte Chyba přidělení vrácená platformou Azure k identifikaci odpovídající scénář. Pokud je připnutý váš požadavek, odeberte některé z Připnutí omezení otevřít žádost o více clusterů, a tím zvýšit riziko přidělení úspěch.
Obecně platí Pokud se chyba není stavu, že "požadovaná velikost virtuálního počítače není podporováno", můžete vždy opakovat později. To je proto dostatek prostředků, může být uvolněna v clusteru, aby dokázala pojmout vaši žádost. Pokud problém je, že požadovaná velikost virtuálního počítače nepodporuje, zkuste jinou velikost virtuálního počítače. Jedinou možností, jinak je odebrání Připnutí omezení.

Dvě běžné scénáře selhání souvisí s skupiny vztahů. V minulosti skupiny vztahů byl použit k poskytování těsné blízkosti virtuální počítače a instance služby nebo byl použit a umožňují tak vytváření virtuální sítě. Se zavedením regionálních virtuálních sítí skupiny vztahů již nejsou potřeba pro vytvoření virtuální sítě. S snížení latence sítě v infrastrukturu Azure se změnila doporučení pro použití skupiny vztahů pro virtuální počítače a služby blízkosti.

Následující Diagram představuje taxonomii scénářů (připojené) přidělení. 

![Taxonomii připojené přidělení](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Změnit velikost virtuálního počítače nebo přidat virtuální počítače nebo instance rolí do existující cloudové služby
**Chyba**

Upgrade_VMSizeNotSupported nebo GeneralError

**Příčinu Připnutí clusteru**

Požadavek na změnit velikost virtuálního počítače nebo virtuálního počítače nebo role instance přidat do existující cloudové služby musí být pokus v původní cluster, který hostuje stávající cloudovou službu. Vytváření nové cloudové služby umožňuje platformy Azure do jiného clusteru, která má volných prostředků nebo podporuje velikost virtuálního počítače, který jste požadovali, najít.

**Alternativní řešení**

Pokud je chyba Upgrade_VMSizeNotSupported *, zkuste jinou velikost virtuálního počítače. Pokud pomocí jinou velikost virtuálního počítače není možné, ale pokud se dá použít jinou virtuální adresu IP (VIP), vytvořte novou cloudovou službu pro hostování nový virtuální počítač a přidat novou cloudovou službu do regionální virtuální síť, kde běží existujících virtuálních počítačů. Pokud vaše stávající cloudovou službu nepoužívá regionální virtuální síť, můžete přesto vytvořit novou virtuální síť pro novou cloudovou službu a potom se připojte vaše [existující virtuální sítě do nové virtuální sítě](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Další informace [regionálních virtuálních sítí](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Pokud je chyba GeneralError *, je pravděpodobné, že typ prostředku (například konkrétní velikost virtuálního počítače) se podporuje v clusteru, ale clusteru nemá volné prostředky v tuto chvíli. Podobá se výše uvedené scénáře, přidejte požadované výpočetních prostředků procesem vytvoření nové cloudové služby (Všimněte si, že má novou cloudovou službu používat jiný virtuální IP adresy) a připojení ke cloudovým službám pomocí regionální virtuální síť.

## <a name="restart-partially-stopped-deallocated-vms"></a>Restartování částečně zastavena (deallocated) virtuálních počítačů
**Chyba**

GeneralError *

**Příčinu Připnutí clusteru**

Částečné navrácení znamená (deallocated) jeden nebo více, ale ne všechny virtuální počítače v cloudové službě byla zastavena. Při zastavení (zrušit přidělení) a virtuální počítač, jsou vydávány přidružené prostředky. Restartování tohoto zastaveném (deallocated) virtuálního počítače je proto novou žádost o přidělení. Restartování virtuálních počítačů v částečně deallocated Cloudová služba je ekvivalentní při přidávání virtuálních počítačů do stávající cloudovou službu. Požadavek na přidělení musí být pokus v původní cluster, který hostuje stávající cloudovou službu. Vytváření jiné cloudové služby umožňuje platformy Azure do jiného clusteru, který má volný prostředek nebo podporuje velikost virtuálního počítače, který jste požadovali, najít.

**Alternativní řešení**

Pokud se dá použít jiný virtuální IP adresy, odstranit zastaveném (deallocated) virtuálních počítačů (ale zachovat přidružené disků) a přidejte virtuální počítače zpět pomocí jiné cloudové služby. Pro připojení vašich cloudových službách použijte regionální virtuální síť:

* Pokud vaše stávající cloudovou službu používá regionální virtuální síť, stačí přidáte novou cloudovou službu do stejné virtuální síti.
* Pokud vaše stávající cloudovou službu nepoužívá regionální virtuální síť, vytvořit novou virtuální síť pro novou cloudovou službu a potom [připojit existující virtuální síť k nové virtuální sítě](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Další informace [regionálních virtuálních sítí](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Restartujte plně zastaveném (deallocated) virtuálních počítačů
**Chyba**

GeneralError *

**Příčinu Připnutí clusteru**

Úplné navrácení znamená, že jste zastavili (nepřiřazeném) všech virtuálních počítačů z cloudové služby. Přidělení žádosti, které chcete restartovat tyto virtuální počítače musí být pokus v původním clusteru, který je hostitelem cloudové služby. Vytváření nové cloudové služby umožňuje platformy Azure do jiného clusteru, která má volných prostředků nebo podporuje velikost virtuálního počítače, který jste požadovali, najít.

**Alternativní řešení**

Pokud se dá použít jiný virtuální IP adresy, odstranit původní zastaveném (deallocated) virtuálních počítačů (ale zachovat přidružené disky) a odstranit odpovídající cloudové služby (přidružené výpočetní prostředky uvolnila již při jeho zastavení (deallocated) virtuálních počítačů). Vytvořte novou cloudovou službu, chcete-li přidat virtuální počítače zpět.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Pracovní nebo produkční nasazení (platforma jako služba pouze)
**Chyba**

New_General * nebo New_VMSizeNotSupported *

**Příčinu Připnutí clusteru**

Nasazení pracovní a provozní nasazení cloudové služby jsou hostované ve stejném clusteru. Když přidáte druhé nasazení, požadavek na odpovídající přidělení se pokusí ve stejném clusteru, který je hostitelem prvním nasazení.

**Alternativní řešení**

Odstraňte prvním nasazení a původní Cloudová služba a znovu nasaďte cloudové služby. Tato akce může být umístěn prvním nasazení v clusteru, který má dostatek volných prostředků, aby vyhovovaly obou nasazení nebo v clusteru, který podporuje velikosti virtuálních počítačů, které jste požádali.

## <a name="affinity-group-vmservice-proximity"></a>Skupina vztahů (blízkosti virtuálního počítače nebo služby)
**Chyba**

New_General * nebo New_VMSizeNotSupported *

**Příčinu Připnutí clusteru**

Všechny výpočetní zdroj přiřazený do skupiny vztahů je vázaný na jednom clusteru. Nový prostředek výpočetní požadavků v tom, že jsou skupiny vztahů pokus ve stejném clusteru, kde jsou hostované existující prostředky. To platí, jestli jsou vytvořeny nové prostředky prostřednictvím novou cloudovou službu nebo stávající cloudovou službu.

**Alternativní řešení**

Pokud skupiny vztahů je nezbytné, není pomocí skupiny vztahů nebo seskupení výpočetní prostředky do více skupin vztahů.

## <a name="affinity-group-based-virtual-network"></a>Na základě vztahů skupiny virtuální sítě
**Chyba**

New_General * nebo New_VMSizeNotSupported *

**Příčinu Připnutí clusteru**

Předtím, než byly zavedeny regionálních virtuálních sítí, bylo potřeba virtuální síť přidružit skupiny vztahů. V důsledku toho výpočetní prostředky, které jsou umístěny do skupiny vztahů je vázána ve stejné omezení, jak je popsáno v "přidělení scénář: Skupina vztahů (virtuálního počítače nebo služby blízkosti)" část výše. Výpočetní prostředky jsou svázané s jeden cluster.

**Alternativní řešení**

Pokud není nutné skupinu vztahů, vytvořte nový regionální virtuální síť pro nové prostředky, které přidáváte, a potom [připojit existující virtuální síť k nové virtuální sítě](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Další informace [regionálních virtuálních sítí](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Alternativně můžete [migrace na základě vztahů skupiny virtuální sítě na regionální virtuální síť](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)a poté znovu přidejte požadované prostředky.
