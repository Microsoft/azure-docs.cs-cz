---
title: Úvod do mezipaměti Azure Redis na úrovni Premium | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet a spravovat Redis trvalost, Redis, clustering a podpora virtuální sítě pro svou úroveň Premium mezipaměti Azure pro instance Redis
services: azure-cache-for-redis
documentationcenter: ''
author: yegu-ms
manager: cfowler
editor: ''
ms.assetid: 30f46f9f-e6ec-4c38-a8cc-f9d4444856e5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 35ecb5156a8b81fa9e10123f7700cf91af91aa0a
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56115840"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Úvod do mezipaměti Azure Redis na úrovni Premium
Mezipaměť Azure pro Redis je distribuovaná spravovaná mezipaměť, která pomáhá díky mimořádně rychlému přístupu k datům vytvářet vysoce škálovatelné a rychle reagující aplikace. 

Nová úroveň Premium je úroveň připravená pro podnikové, které obsahuje všechny funkce úrovně Standard a další, jako třeba vyšší výkon, větší úlohy, zotavení po havárii, importu/exportu a zvýšené zabezpečení. Pokračujte ve čtení získat další informace o další funkce úrovně Premium mezipaměti.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Lepší výkon ve srovnání s úrovní Standard nebo Basic
**Vyšší výkon přes standardní nebo základní úroveň.** Mezipaměť na úrovni Premium jsou nasazené na hardwaru, který má rychlejšími procesory a poskytuje lepší výkon ve srovnání na základní nebo standardní úroveň. Mezipaměti úrovně Premium má vyšší propustnost a nižší latenci. 

**Propustnost pro stejné velikosti mezipaměti je na úrovni Premium vyšší než úroveň Standard.** Například propustnost 53 GB P4 (Premium) mezipaměť je 250 tisíc požadavků za sekundu v porovnání s 150 kontrolovat C6 (Standard).

Další informace o velikosti, propustnosti a šířky pásma u prémiových mezipamětí najdete v tématu [mezipaměti Azure redis Cache – nejčastější dotazy](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Trvalost dat Redis
Úroveň Premium umožňuje zachovat data v mezipaměti v účtu služby Azure Storage. V mezipaměti Basic nebo Standard všechna data uložena pouze v paměti. V případě základní infrastruktury existuje problémy můžou být potenciální ztráty dat. Doporučujeme použití funkce trvalosti dat Redis na úrovni Premium ke zvýšení odolnosti proti ztrátě dat. Mezipaměti Redis Azure nabízí RDB a AOF (už brzo) možnosti v [trvalost Redis](https://redis.io/topics/persistence). 

Pokyny týkající se konfigurace trvalosti najdete v tématu [konfigurace trvalosti pro mezipaměť Azure Premium pro Redis](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Cluster Redis
Pokud chcete vytvořit větší než 53 GB mezipaměti nebo chcete sdílení dat mezi více uzlů Redis, můžete použít clustering, která je dostupná na úrovni Premium Redis. Každý uzel se skládá z dvojice primární/replika cache spravuje Azure pro zajištění vysoké dostupnosti. 

**Redis clustering poskytuje maximální škálování a vyšší propustnost.** Propustnost se lineárně zvyšuje, zvýšit počet horizontálních oddílů (uzlů) v clusteru. Např. Pokud vytvoříte cluster P4 10 horizontálními oddíly, je k dispozici propustnost 250 tisíc * 10 = 2,5 milionu požadavků za sekundu. Podrobnosti najdete [mezipaměti Azure redis Cache – nejčastější dotazy](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) podrobné informace o velikosti, propustnosti a šířky pásma u prémiových mezipamětí.

Abyste mohli začít s clusteringem, naleznete v tématu [postup konfigurace clusterů pro mezipaměť Azure Premium pro Redis](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Vyšší míra zabezpečení a izolace
Mezipaměť vytvořena na úrovni Basic nebo Standard jsou dostupné na veřejném Internetu. Přístup k mezipaměti je omezen na základě klíče přístup. S úrovní Premium můžete dále zajistit pouze klienty v zadané síti přístup do mezipaměti. Mezipaměť Azure můžete nasadit pro Redis v [Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/). K dalšímu omezení přístupu k Redisu můžete použít všechny funkce sítě VNet, například podsítě, zásady řízení přístupu a další funkce.

Další informace najdete v tématu [konfigurace podpory služby Virtual Network pro mezipaměť Azure Premium pro Redis](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Import/export
Import/Export je Azure Cache pro operace správy dat Redis, který umožňuje importovat data do mezipaměti Azure Redis a export dat z mezipaměti Azure Redis import a export pro snímek Redis databáze (RDB) z mezipaměti úrovně premium pro Azure Cache Objekt blob stránky v účtu služby Azure Storage. To umožňuje migraci mezi různé mezipaměti Azure pro instance Redis nebo naplňte mezipaměť daty před použitím.

Import je možné uvést kompatibilní soubory RDB Redis z jakéhokoli serveru Redis spuštěná v jakémkoli cloudu nebo prostředí, včetně Redis běžící na Linuxu, Windows nebo libovolného poskytovatele cloudových služeb, jako je například Amazon Web Services a další. Import dat je snadný způsob, jak vytvořit mezipaměť s předem naplněných daty. Během procesu importu mezipaměti Redis Azure načte soubory RDB ze služby Azure storage do paměti a pak vloží klíčů do mezipaměti.

Export umožňuje exportovat data uložená v mezipaměti Azure pro Redis k Redis kompatibilní soubory RDB. Tato funkce slouží k přesunu dat z jedné mezipaměti Azure pro instanci Redis do jiné nebo na jiný server Redis. Během procesu exportu dočasný soubor vytvořen na virtuálním počítači, který je hostitelem ukládání do mezipaměti Azure pro instanci serveru Redis a se soubor nahraje do účtu úložiště určený. Po dokončení operace exportu se stavem úspěch nebo neúspěch dočasný soubor bude odstraněn.

Další informace najdete v tématu [jak data importovat a exportovat data z mezipaměti Azure Redis](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Restartování
Úroveň premium umožňuje restartovat jeden nebo více uzlů z mezipaměti na vyžádání. To umožňuje testovat aplikaci pro odolnost proti chybám v případě selhání. Tyto uzly můžete restartovat.

* Hlavní uzel mezipaměti
* Podřízený uzel mezipaměti
* Nadřízeného a podřízeného uzly mezipaměti
* Při použití cache ve verzi premium s clusteringem, restartujte hlavní, podřízený server nebo oba uzly pro jednotlivých horizontálních oddílů v mezipaměti

Další informace najdete v tématu [restartovat](cache-administration.md#reboot) a [nejčastější dotazy k restartování](cache-administration.md#reboot-faq).

>[!NOTE]
>Restartování funkce je teď zapnutá pro všechny mezipaměti Azure Redis úrovní.
>
>

## <a name="schedule-updates"></a>Plán aktualizací
Funkci naplánované aktualizace umožňuje určit časové období údržby pro mezipaměť. Pokud je zadána časové období údržby, všechny aktualizace serveru Redis probíhají během tohoto časového období. Určete časové období údržby, vyberte požadovanou dny a zadejte údržby spusťte okno hodinu za každý den. Všimněte si, že časového období údržby se ve standardu UTC. 

Další informace najdete v tématu [naplánovat aktualizace](cache-administration.md#schedule-updates) a [naplánovat aktualizace nejčastější dotazy k](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Pouze Redis server provedeny aktualizace během plánované časové období údržby. Časové období údržby se nevztahuje na Azure aktualizace nebo aktualizace operačního systému virtuálního počítače.
> 
> 

## <a name="geo-replication"></a>Geografická replikace

**Geografická replikace** poskytuje mechanismus pro propojení dvou úroveň Premium mezipaměti Azure pro instance Redis. Jeden mezipaměti je určený jako primární propojené mezipaměti a druhý jako sekundární propojené mezipaměti. Propojené sekundární mezipaměť bude jen pro čtení a data zapsaná do primární mezipaměti se replikuje do sekundární propojené mezipaměti. Tuto funkci je možné replikovat do mezipaměti v různých oblastech Azure.

Další informace najdete v tématu [konfigurace geografické replikace pro Azure Cache pro Redis](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Škálování na úrovni premium
Pokud chcete škálovat na úrovni premium, jednoduše zvolte jednu z úrovně premium ve **změnit cenovou úroveň** okno. Také můžete škálovat mezipaměť na úrovni premium pomocí prostředí PowerShell a rozhraní příkazového řádku. Podrobné pokyny najdete v tématu [postup škálování Azure mezipaměti Redis](cache-how-to-scale.md) a [jak automatizovat operaci škálování](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Další postup
Vytvoření mezipaměti a prozkoumání nových funkcí úrovně premium.

* [Konfigurace trvalosti pro mezipaměť Azure Premium pro Redis](cache-how-to-premium-persistence.md)
* [Postup konfigurace podpory služby Virtual Network pro mezipaměť Azure Premium pro Redis](cache-how-to-premium-vnet.md)
* [Konfigurace clusteringu pro Azure Cache úrovně Premium pro Redis](cache-how-to-premium-clustering.md)
* [Postup importu dat do a export dat z mezipaměti Azure Redis](cache-how-to-import-export-data.md)
* [Správa mezipaměti Azure pro Redis](cache-administration.md)

