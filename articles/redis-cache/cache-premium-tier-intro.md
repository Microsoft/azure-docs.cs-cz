---
title: Úvod do Azure Redis Cache Premium vrstvy | Microsoft Docs
description: Zjistěte, jak vytvořit a spravovat trvalost Redis, Redis clustering a podpora virtuální sítě pro vaše instance služby Azure Redis Cache úrovně Premium
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 30f46f9f-e6ec-4c38-a8cc-f9d4444856e5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: wesmc
ms.openlocfilehash: 38a43756678a3628040b1b995966eff6dd9fb363
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27911199"
---
# <a name="introduction-to-the-azure-redis-cache-premium-tier"></a>Představení služby Azure Redis Cache na úrovni Premium
Azure Redis Cache je mezipaměť distribuovaná, kterou spravuje vám usnadní vytváření vysoce škálovatelné a dobře reagovaly aplikací tím, že poskytuje extrémně rychlého přístup k datům. 

Nové úrovni Premium je připraven vrstvou Enterprise, která zahrnuje všechny funkce úrovně Standard a informace, jako je například lepší výkon, větší zatížení, zotavení po havárii, importu a exportu a rozšířené zabezpečení. Pokračujte ve čtení Další informace o dalších funkcích vrstvy mezipaměti Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Lepší výkon ve srovnání s úroveň Standard nebo Basic
**Vyšší výkon přes standardní nebo základní vrstvě.** Mezipaměti v úrovni Premium jsou nasazeny na hardware, který má rychlejších procesorů a nabízí lepší výkon ve srovnání na základní nebo standardní úroveň. Premium úroveň mezipaměti mají vyšší propustnost a nižší latenci. 

**Propustnost pro stejnou velikostí mezipaměti je vyšší na úrovni Premium porovnání úrovně Standard.** Například propustnost 53 GB P4 (Premium) mezipaměť je 250 TIS požadavků za sekundu ve srovnání se 150 kB pro C6 (Standard).

Další informace o velikosti, propustnosti a šířky pásma u prémiových mezipamětí najdete v tématu [Azure Redis Cache – nejčastější dotazy](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Trvalost dat Redis
Úroveň Premium umožňuje zachovat data v mezipaměti v účtu Azure Storage. V mezipaměti Basic nebo Standard všechna data uložena pouze v paměti. V případě základní infrastruktura může být problémy existuje potenciální ztrátě dat. Doporučujeme používat funkce trvalosti dat Redis zvyšte odolnost proti ztrátě dat v úrovni Premium. Azure Redis Cache nabízí RDB a AOF (už brzy) možnosti v [trvalosti Redis](http://redis.io/topics/persistence). 

Pokyny týkající se konfigurace trvalosti najdete v článku [Postup konfigurace trvalosti pro mezipaměť Azure Redis Cache Premium](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Cluster redis
Pokud budete chtít vytvořit větší než 53 GB mezipaměti nebo chcete sdílení dat mezi různými uzly Redis, můžete použít clustering, která je dostupná v úrovni Premium Redis. Každý uzel se skládá z dvojice primární/replika mezipaměti spravuje Azure pro vysokou dostupnost. 

**Redis clustering poskytuje maximální měřítko a propustnosti.** Propustnost zvyšuje lineárně zvýšit počet horizontálních oddílů (uzlů) v clusteru. Eg. Pokud vytvoříte cluster P4 10 horizontálními oddíly, je k dispozici propustnost 250 TIS * 10 = 2,5 milionu požadavků za sekundu. Podrobnosti najdete [Azure Redis Cache – nejčastější dotazy](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) další podrobnosti o velikosti, propustnosti a šířky pásma u prémiových mezipamětí.

Začínáme s clustering, najdete v tématu [postup konfigurace clusterů pro mezipaměť Azure Redis Cache Premium](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Vyšší míra zabezpečení a izolace
Mezipamětí vytvořené v úroveň Basic nebo Standard jsou přístupné z veřejného Internetu. Přístup do mezipaměti je omezen na základě klíče přístup. S úroveň Premium můžete další zajistit, že jenom pro klienty v zadané síti přístup do mezipaměti. Můžete nasadit Redis Cache v [Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/). K dalšímu omezení přístupu k Redisu můžete použít všechny funkce sítě VNet, například podsítě, zásady řízení přístupu a další funkce.

Další informace najdete v článku [Postup konfigurace podpory služby Virtual Network pro mezipaměť Azure Redis Cache Premium](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Import/export
Import a Export je operace Azure Redis Cache dat správy, který umožňuje importovat data do Azure Redis Cache nebo exportovat data z Azure Redis Cache pomocí import a Export snímku databáze Redis Cache (RDB) z mezipaměti premium do objektu blob stránky v Azure Účet úložiště. To umožňuje migraci mezi různými instancemi Azure Redis Cache nebo naplnění mezipaměti s daty před použitím.

Import umožňuje přinést si Redis kompatibilní RDB soubory z jakéhokoli Redis serveru se systémem v libovolném cloudu nebo prostředí, včetně Redis systémem Linux, Windows nebo kteréhokoli poskytovatele cloudových služeb jako Amazon Web Services a dalších. Import dat je snadný způsob, jak vytvořit mezipaměti s předem vyplněná data. Během procesu importu Azure Redis Cache načte RDB soubory z úložiště Azure do paměti a vloží klíčů do mezipaměti.

Export umožňuje exportovat data uložená ve službě Azure Redis Cache k Redis kompatibilní soubory RDB. Tato funkce slouží pro přesun dat z jedné instance Azure Redis Cache do jiné nebo jinému serveru Redis. Během procesu exportu ve virtuálním počítači, který je hostitelem instance serveru Azure Redis Cache je vytvořit dočasný soubor a soubor je odeslán k účtu úložiště určený. Po dokončení operace exportu se stavem úspěch nebo selhání dočasný soubor bude odstraněn.

Další informace najdete v tématu [postup importu dat do a exportu dat z Azure Redis Cache](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Restartování
Úroveň premium umožňuje restartovat jeden nebo více uzlů vaší mezipaměti na vyžádání. To umožňuje testování vaší aplikace pro odolnost v případě selhání. Následující uzly můžete restartovat.

* Hlavní uzel vaší mezipaměti
* Podřízený uzel vaší mezipaměti
* Hlavní a podřízené uzly mezipaměti
* Při použití s clusteringem cache ve verzi premium, můžete restartovat hlavní, podřízený nebo oba uzly pro jednotlivé horizontálních oddílů v mezipaměti

Další informace najdete v tématu [restartovat](cache-administration.md#reboot) a [nejčastější dotazy týkající se restartovat](cache-administration.md#reboot-faq).

>[!NOTE]
>Restartovat funkce je teď zapnutá pro všechny úrovně Azure Redis Cache.
>
>

## <a name="schedule-updates"></a>Aktualizace plánu
Funkci plánovaných aktualizací umožňuje určit údržby pro mezipaměť. Pokud je zadána pro správu a údržbu, jsou všechny aktualizace serveru Redis provedené během této doby. Určete časové období údržby, vyberte požadované dny a zadejte údržby zahájení okna hodinu pro každý den. Všimněte si, že časového období údržby se ve standardu UTC. 

Další informace najdete v tématu [naplánovat aktualizace](cache-administration.md#schedule-updates) a [naplánovat aktualizace – nejčastější dotazy](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Pouze Redis serveru, které jsou provedeny aktualizace během plánované údržby. Pro správu a údržbu se nevztahuje na Azure aktualizace nebo aktualizace pro operační systém virtuálního počítače.
> 
> 

## <a name="geo-replication"></a>Geografická replikace

**Geografická replikace** poskytuje mechanismus pro propojení dvě instance vrstvy Azure Redis Cache Premium. Jeden mezipaměti je určený jako primární propojené mezipaměti a druhý jako sekundární propojené mezipaměti. Sekundární propojené mezipaměti se stane, jen pro čtení a data zapsat do mezipaměti. primární se replikují do sekundární propojené mezipaměti. Tato funkce slouží k replikaci mezipaměti mezi oblastmi Azure.

Další informace najdete v tématu [konfiguraci geografická replikace pro Azure Redis Cache](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Škálování na plán úrovně premium
Škálování na vrstvě | premium, jednoduše vyberte jednu z úrovní premium v **změna cenové úrovně** okno. Také je možné škálovat vaše mezipaměť na vrstvě | premium pomocí prostředí PowerShell a rozhraní příkazového řádku. Podrobné pokyny najdete v tématu [postup škálování Azure Redis Cache](cache-how-to-scale.md) a [jak automatizovat škálování operaci](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Další postup
Vytvoření mezipaměti a prozkoumat nové funkce úrovně premium.

* [Konfigurace trvalosti pro Azure Redis Cache ve verzi Premium](cache-how-to-premium-persistence.md)
* [Konfigurace podpory služby Virtual Network pro Azure Redis Cache ve verzi Premium](cache-how-to-premium-vnet.md)
* [Konfigurace clusteringu pro Azure Redis Cache ve verzi Premium](cache-how-to-premium-clustering.md)
* [Postup importu dat do a exportu dat z Azure Redis Cache](cache-how-to-import-export-data.md)
* [Postupy při správě Azure Redis Cache](cache-administration.md)

