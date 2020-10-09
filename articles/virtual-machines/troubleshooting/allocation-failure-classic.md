---
title: Řešení potíží s přidělováním virtuálních počítačů Azure v modelu nasazení Classic | Microsoft Docs
description: Řešení chyb přidělení při vytváření, restartování nebo změně velikosti klasického virtuálního počítače v Azure
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77913374"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Postup řešení potíží pro scénáře selhání přidělení v modelu nasazení Classic

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Níže jsou uvedené běžné scénáře přidělování, které způsobují připnutí žádosti o přidělení. V tomto článku se budeme podrobně k jednotlivým scénářům dále.

- Změna velikosti virtuálního počítače nebo přidání instancí virtuálních počítačů nebo rolí do existující cloudové služby
- Restartování částečně zastavených (uvolněných) virtuálních počítačů
- Restartování úplně zastavených (uvolněných) virtuálních počítačů
- Pracovní a provozní nasazení (jenom platforma jako služba)
- Skupina vztahů (virtuální počítač nebo blízkost služby)
- Spřažení – virtuální síť založená na skupinách

Když obdržíte chybu přidělení, ověřte, jestli se na vaši chybu nevztahují některé z uvedených scénářů. Použijte chybu přidělení, kterou vrátí platforma Azure, a Identifikujte odpovídající scénář. Pokud je váš požadavek připnutý, odeberte některá omezení připnutí a otevřete tak vaši žádost více clusterům, čímž zvýšíte pravděpodobnost úspěchu přidělení.
Obecně platí, že pokud chyba neuvádí, že "požadovaná velikost virtuálního počítače není podporována", můžete se kdykoli pokusit opakovat později. Důvodem je to, že v clusteru je pravděpodobně uvolněn dostatek prostředků pro přizpůsobení vaší žádosti. Pokud se jedná o problém, že požadovaná velikost virtuálního počítače není podporovaná, zkuste jinou velikost virtuálního počítače. V opačném případě jedinou možností je odebrat omezení připnutí.

Dva běžné scénáře selhání souvisejí se skupinami vztahů. V minulosti se skupina vztahů použila k zajištění těsné blízkosti virtuálních počítačů a instancí služby nebo se použila k povolení vytvoření virtuální sítě. Po zavedení regionálních virtuálních sítí už skupiny vztahů nemusíte vytvářet virtuální sítě. Při snižování latence sítě v infrastruktuře Azure se změnilo doporučení pro používání skupin vztahů pro virtuální počítače nebo blízkosti služby.

Následující diagram znázorňuje taxonomii (připnutých) scénářů přidělení. 

![Připnuté taxonomie přidělení](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Změna velikosti virtuálního počítače nebo přidání instancí virtuálních počítačů nebo rolí do existující cloudové služby
**Chyba**

Upgrade_VMSizeNotSupported nebo GeneralError

**Příčina připnutí clusteru**

Žádost o změnu velikosti virtuálního počítače nebo přidání virtuálního počítače nebo instance role do existující cloudové služby se musí vyžádat v původním clusteru, který hostuje existující cloudovou službu. Vytvořením nové cloudové služby umožníte platformě Azure najít další cluster, který má bezplatné prostředky, nebo podporuje požadovanou velikost virtuálního počítače.

**Alternativní řešení**

Pokud je chyba Upgrade_VMSizeNotSupported *, zkuste jinou velikost virtuálního počítače. Pokud použití jiné velikosti virtuálního počítače není možnost, ale pokud je přijatelné použít jinou virtuální IP adresu (VIP), vytvořte novou cloudovou službu pro hostování nového virtuálního počítače a přidejte novou cloudovou službu do místní virtuální sítě, ve které jsou spuštěné stávající virtuální počítače. Pokud vaše existující cloudová služba nepoužívá regionální virtuální síť, můžete pro novou cloudovou službu vytvořit novou virtuální síť a pak připojit [stávající virtuální síť k nové virtuální síti](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Přečtěte si další informace o [regionálních virtuálních sítích](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Pokud je chyba GeneralError *, je pravděpodobnější, že cluster podporuje typ prostředku (například velikost konkrétního virtuálního počítače), ale cluster nemá v současnosti volné prostředky. Podobně jako u výše uvedeného scénáře přidejte požadovaný výpočetní prostředek pomocí vytvoření nové cloudové služby (Všimněte si, že nová cloudová služba musí používat jinou virtuální IP adresu) a k připojení svých cloudových služeb používá regionální virtuální síť.

## <a name="restart-partially-stopped-deallocated-vms"></a>Restartování částečně zastavených (uvolněných) virtuálních počítačů
**Chyba**

GeneralError*

**Příčina připnutí clusteru**

Částečné navracení znamená, že jste zastavili (oddělili) jednu nebo víc virtuálních počítačů v cloudové službě, ale ne všechny. Když zastavíte (narušíte přidělení) virtuálního počítače, budou se tyto přidružené prostředky uvolnit. Restartování virtuálního počítače, který se zastavil (přidělení), je tedy nová žádost o přidělení. Restartování virtuálních počítačů v částečně navrácené cloudové službě se podobá přidání virtuálních počítačů do existující cloudové služby. Požadavek na přidělení se musí provést v původním clusteru, který hostuje stávající cloudovou službu. Vytvořením jiné cloudové služby umožníte platformě Azure najít další cluster, který má bezplatný prostředek, nebo podporuje požadovanou velikost virtuálního počítače.

**Alternativní řešení**

Pokud je přijatelné použít jinou VIP, odstraňte zastavené (navrácené) virtuální počítače (ale zachovejte přidružené disky) a přidejte virtuální počítače zpátky přes jinou cloudovou službu. Pro připojení ke cloudovým službám použijte místní virtuální síť:

* Pokud vaše stávající cloudová služba používá oblastní virtuální síť, stačí přidat novou cloudovou službu ke stejné virtuální síti.
* Pokud vaše existující cloudová služba nepoužívá regionální virtuální síť, vytvořte novou virtuální síť pro novou cloudovou službu a pak [Připojte stávající virtuální síť k nové virtuální síti](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Přečtěte si další informace o [regionálních virtuálních sítích](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Restartování úplně zastavených (uvolněných) virtuálních počítačů
**Chyba**

GeneralError*

**Příčina připnutí clusteru**

Úplné navracení znamená, že jste zastavili (oddělili) všechny virtuální počítače z cloudové služby. Žádosti o přidělení k restartování těchto virtuálních počítačů je nutné provést v původním clusteru, který hostuje cloudovou službu. Vytvořením nové cloudové služby umožníte platformě Azure najít další cluster, který má bezplatné prostředky, nebo podporuje požadovanou velikost virtuálního počítače.

**Alternativní řešení**

Pokud je přijatelné použít jinou virtuální IP adresu, odstraňte původní zastavené (navrácené) virtuální počítače (ale zachovejte přidružené disky) a odstraňte odpovídající cloudovou službu (přidružené výpočetní prostředky už byly po zastavení (přidělení) virtuálních počítačů vydané. Vytvořte novou cloudovou službu pro přidání virtuálních počítačů zpátky.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Pracovní/provozní nasazení (jenom platforma jako služba)
**Chyba**

New_General * nebo New_VMSizeNotSupported *

**Příčina připnutí clusteru**

Pracovní nasazení a provozní nasazení cloudové služby se hostují ve stejném clusteru. Když přidáte druhé nasazení, bude proveden pokus o odpovídající žádost o přidělení ve stejném clusteru, který je hostitelem prvního nasazení.

**Alternativní řešení**

Odstraňte první nasazení a původní cloudovou službu a znovu nasaďte cloudovou službu. Tato akce může nakládat s prvním nasazením v clusteru, který má dostatek volných prostředků pro přizpůsobení nasazení nebo v clusteru, který podporuje požadované velikosti virtuálních počítačů.

## <a name="affinity-group-vmservice-proximity"></a>Skupina vztahů (virtuální počítač/služba)
**Chyba**

New_General * nebo New_VMSizeNotSupported *

**Příčina připnutí clusteru**

Libovolný výpočetní prostředek přiřazený ke skupině vztahů je vázaný na jeden cluster. Nové požadavky na výpočetní prostředky v této skupině vztahů se pokoušejí ve stejném clusteru, ve kterém jsou hostované stávající prostředky. To platí bez ohledu na to, jestli se nové prostředky vytvoří prostřednictvím nové cloudové služby nebo existující cloudové služby.

**Alternativní řešení**

Pokud není skupina vztahů potřebná, nepoužívejte skupinu vztahů ani seskupte výpočetní prostředky do více skupin vztahů.

## <a name="affinity-group-based-virtual-network"></a>Spřažení – virtuální síť založená na skupinách
**Chyba**

New_General * nebo New_VMSizeNotSupported *

**Příčina připnutí clusteru**

Před tím, než byly zavedeny regionální virtuální sítě, jste museli přidružit virtuální síť ke skupině vztahů. Výsledkem je, že výpočetní prostředky umístěné do skupiny vztahů jsou vázány stejnými omezeními, jak je popsáno v části scénář přidělení: spřažení (virtuální počítač/služba) výše. Výpočetní prostředky jsou vázané na jeden cluster.

**Alternativní řešení**

Pokud nepotřebujete skupinu vztahů, vytvořte novou oblastní virtuální síť pro nové prostředky, které přidáváte, a pak [Připojte stávající virtuální síť k nové virtuální síti](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Přečtěte si další informace o [regionálních virtuálních sítích](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Alternativně můžete [migrovat virtuální síť založenou na skupinách na síť do oblastní virtuální sítě](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)a pak znovu přidat požadované prostředky.


