---
title: Řešení potíží s chybami přidělení virtuálních počítačů Azure v klasickém modelu nasazení| Dokumenty společnosti Microsoft
description: Poradce při potížích s chybami přidělení při vytváření, restartování nebo změně velikosti klasického virtuálního počítače v Azure
services: azure-service-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 20e64e5225987a8045e406a0e8fcae098c580c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913374"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Postup řešení potíží pro scénáře selhání přidělení v modelu nasazení Classic

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Následují běžné scénáře přidělení, které způsobují připnutí požadavku na přidělení. Ponoříme se do každého scénáře dále v tomto článku.

- Změna velikosti virtuálního počítače nebo přidání virtuálních počítače nebo instancí rolí do existující cloudové služby
- Restartování částečně zastavených (uvolněných) virtuálních počítačů
- Restartování úplně zastavených (uvolněných) virtuálních počítačů
- Pracovní a produkční nasazení (pouze platforma jako služba)
- Skupina spřažení (blízkost virtuálního zařízení nebo služby)
- Virtuální síť založená na skupině spřažení

Když se zobrazí chyba přidělení, zkontrolujte, zda některý z uvedených scénářů použít pro vaši chybu. Použijte chybu přidělení, která je vrácena platformou Azure k identifikaci odpovídající scénář. Pokud je váš požadavek připnutý, odeberte některá omezení připnutí, abyste požadavek otevřeli více clusterům, čímž se zvýší pravděpodobnost úspěchu přidělení.
Obecně platí, že pokud chyba neuvádí, že "požadovaná velikost virtuálního počítače není podporována", můžete to vždy zopakovat později. Důvodem je, že v clusteru mohlo být uvolněno dostatečné množství prostředků, aby bylo možné vyhovět vašemu požadavku. Pokud je problém, že požadovaná velikost virtuálního počítače není podporována, zkuste jinou velikost virtuálního počítače. V opačném případě je jedinou možností odebrání omezení připnutí.

Dva běžné scénáře selhání se vztahují ke skupinám spřažení. V minulosti byla skupina spřažení použita k zajištění těsné blízkosti virtuálních počítačů a instancí služeb nebo byla použita k povolení vytvoření virtuální sítě. Se zavedením regionálních virtuálních sítí se skupiny spřažení již nepotřebují k vytvoření virtuální sítě. Se snížením latence sítě v infrastruktuře Azure se změnilo doporučení použít skupiny spřažení pro virtuální počítače nebo blízkost služby.

Následující diagram představuje taxonomii (připnutý) přidělení scénáře. 

![Připnutá taxonomie přidělení](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Změna velikosti virtuálního počítače nebo přidání virtuálních počítače nebo instancí rolí do existující cloudové služby
**Chyba**

Upgrade_VMSizeNotSupported nebo GeneralError

**Příčina připnutí clusteru**

Požadavek na změnit velikost virtuálního počítače nebo přidat virtuální ho virtuálního počítače nebo instance role do existující cloudové služby musí být pokus v původním clusteru, který je hostitelem existující cloudové služby. Vytvoření nové cloudové služby umožňuje platformě Azure najít jiný cluster, který má volné prostředky nebo podporuje velikost virtuálního počítače, kterou jste požadovali.

**Řešení**

Pokud je chyba Upgrade_VMSizeNotSupported*, zkuste jinou velikost virtuálního počítače. Pokud použití jiné velikosti virtuálního počítače není možnost, ale pokud je přijatelné použít jinou virtuální IP adresu (VIP), vytvořte novou cloudovou službu pro hostování nového virtuálního počítače a přidejte novou cloudovou službu do místní virtuální sítě, kde jsou spuštěné stávající virtuální počítače. Pokud vaše stávající cloudová služba nepoužívá místní virtuální síť, můžete pro novou cloudovou službu vytvořit novou virtuální síť a potom připojit [stávající virtuální síť k nové virtuální síti](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Další informace o [regionálních virtuálních sítích](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Pokud je chyba GeneralError*, je pravděpodobné, že typ prostředku (například konkrétní velikost virtuálního počítače) je podporován clusterem, ale cluster nemá volné prostředky v tuto chvíli. Podobně jako ve výše uvedeném scénáři přidejte požadovaný výpočetní prostředek vytvořením nové cloudové služby (všimněte si, že nová cloudová služba musí používat jinou virtuální síť) a k připojení cloudových služeb použijte regionální virtuální síť.

## <a name="restart-partially-stopped-deallocated-vms"></a>Restartování částečně zastavených (uvolněných) virtuálních počítačů
**Chyba**

Chyba GeneralError*

**Příčina připnutí clusteru**

Částečné deallocation znamená, že jste zastavili (saze) jeden nebo více, ale ne všechny virtuální chody v cloudové službě. Když zastavíte (navrátit) virtuální hod, přidružené prostředky jsou uvolněny. Restartování tohoto zastaveného (sabaného) virtuálního virtuálního zařízení je tedy nový požadavek na přidělení. Restartování virtuálních připojení v částečně úbytné cloudové službě je ekvivalentní přidání virtuálních připojení k existující cloudové službě. Požadavek na přidělení musí být pokus v původním clusteru, který je hostitelem existující cloudové služby. Vytvoření jiné cloudové služby umožňuje platformě Azure najít jiný cluster, který má volný prostředek nebo podporuje velikost virtuálního počítače, kterou jste požadovali.

**Řešení**

Pokud je přijatelné použít jinou virtuální ip adresu, odstraňte zastavené (nabyté) virtuální počítače (ale zachovejte přidružené disky) a přidejte virtuální počítače zpět prostřednictvím jiné cloudové služby. K připojení cloudových služeb použijte místní virtuální síť:

* Pokud vaše stávající cloudová služba používá místní virtuální síť, jednoduše přidejte novou cloudovou službu do stejné virtuální sítě.
* Pokud vaše stávající cloudová služba nepoužívá místní virtuální síť, vytvořte novou virtuální síť pro novou cloudovou službu a potom [připojte stávající virtuální síť k nové virtuální síti](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Další informace o [regionálních virtuálních sítích](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Restartování úplně zastavených (uvolněných) virtuálních počítačů
**Chyba**

Chyba GeneralError*

**Příčina připnutí clusteru**

Úplné deallocation znamená, že jste zastavili (salokované) všechny virtuální chody z cloudové služby. Požadavky na přidělení restartování těchto virtuálních počítačů musí být pokus v původním clusteru, který je hostitelem cloudové služby. Vytvoření nové cloudové služby umožňuje platformě Azure najít jiný cluster, který má volné prostředky nebo podporuje velikost virtuálního počítače, kterou jste požadovali.

**Řešení**

Pokud je přijatelné použít jinou virtuální ip adresu, odstraňte původní zastavené (uvolněné) virtuální počítače (ale zachovat přidružené disky) a odstraňte odpovídající cloudovou službu (přidružené výpočetní prostředky už byly uvolněny, když jste zastavili (uvolnili) virtuální počítače). Vytvořte novou cloudovou službu a přidejte virtuální připojení zpět.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Pracovní/produkční nasazení (pouze platforma jako služba)
**Chyba**

New_General* nebo New_VMSizeNotSupported*

**Příčina připnutí clusteru**

Testovací nasazení a produkční nasazení cloudové služby jsou hostovány ve stejném clusteru. Když přidáte druhé nasazení, dojde k pokusu o odpovídající požadavek na přidělení ve stejném clusteru, který je hostitelem prvního nasazení.

**Řešení**

Odstraňte první nasazení a původní cloudovou službu a znovu nasaďte cloudovou službu. Tato akce může přistát první nasazení v clusteru, který má dostatek volných prostředků, aby se vešly obě nasazení nebo v clusteru, který podporuje velikosti virtuálních počítače, které jste požadovali.

## <a name="affinity-group-vmservice-proximity"></a>Skupina spřažení (blízkost virtuálního zařízení/služby)
**Chyba**

New_General* nebo New_VMSizeNotSupported*

**Příčina připnutí clusteru**

Všechny výpočetní prostředky přiřazené ke skupině spřažení jsou vázány na jeden cluster. Nové požadavky na výpočetní prostředky v této skupině spřažení jsou pokusy ve stejném clusteru, kde jsou hostovány existující prostředky. To platí bez ohledu na to, zda jsou nové prostředky vytvořeny prostřednictvím nové cloudové služby nebo prostřednictvím existující cloudové služby.

**Řešení**

Pokud skupina spřažení není nutná, nepoužívejte skupinu spřažení ani neseskupte výpočetní prostředky do více skupin spřažení.

## <a name="affinity-group-based-virtual-network"></a>Virtuální síť založená na skupině spřažení
**Chyba**

New_General* nebo New_VMSizeNotSupported*

**Příčina připnutí clusteru**

Před zavedením místních virtuálních sítí bylo nutné přidružit virtuální síť ke skupině spřažení. V důsledku toho výpočetní prostředky umístěné do skupiny spřažení jsou vázány stejnými omezeními, jak je popsáno v části "Scénář přidělení: Skupina spřažení (Blízkost virtuálního počítače/služby)" výše. Výpočetní prostředky jsou vázané na jeden cluster.

**Řešení**

Pokud skupinu spřažení nepotřebujete, vytvořte novou místní virtuální síť pro nové prostředky, které přidáváte, a pak [připojte stávající virtuální síť k nové virtuální síti](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Další informace o [regionálních virtuálních sítích](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Případně můžete [migrovat virtuální síť založenou na skupině spřažení do místní virtuální sítě](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)a pak znovu přidat požadované prostředky.


