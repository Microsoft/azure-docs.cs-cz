---
title: Co je Azure Redis Cache? | Microsoft Docs
description: Přečtěte si, co je Azure Redis Cache a jak se běžně používá.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: overview
ms.date: 03/26/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 8f477282e49104e9b034e11656ff50c2a67545f7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195868"
---
# <a name="what-is-azure-redis-cache"></a>Co je Azure Redis Cache

Služba Azure Redis Cache je založená na oblíbené opensourcové [mezipaměti Redis Cache](https://redis.io/). Obvykle se používá jako mezipaměť ke zlepšení výkonu a škálovatelnosti systémů, které z velké části spoléhají na back-endová úložiště dat. Lepšího výkonu se dosahuje dočasným zkopírováním často používaných dat do rychlého úložiště umístěného v blízkosti aplikace. V případě služby [Redis Cache](https://redis.io/) se toto rychlé úložiště nachází v paměti služby Redis Cache a databáze ho nenačítá z disku.

Azure Redis Cache je možné použít také jako úložiště datových struktur v paměti, distribuovanou nerelační databázi a zprostředkovatele zpráv. Lepšího výkonu aplikací se dosahuje díky nízké latenci a vysoké propustnosti modulu Redis.

Azure Redis Cache poskytuje přístup k zabezpečené vyhrazené mezipaměti Redis, která je spravovaná Microsoftem, hostovaná v Azure a dostupná z libovolné aplikace v Azure.

## <a name="why-use-azure-redis-cache"></a>Proč používat Azure Redis Cache

Existuje mnoho běžných vzorů, ve kterých se Redis Cache používá pro podporu architektury aplikací nebo ke zlepšení výkonu aplikací. Mezi nejběžnější případy použití patří:

| Vzor      | Popis                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Vzhledem k tomu, že databáze může být rozsáhlá, nedoporučuje se načítat celou databázi do mezipaměti. Běžně se využívá princip [s doplňováním mezipaměti aplikací](https://docs.microsoft.com/azure/architecture/patterns/cache-aside), při kterém se datové položky načítají do mezipaměti pouze v případě potřeby. Když systém provede změny v back-endových datech, může také současně aktualizovat mezipaměť, která se rozdistribuuje do ostatních klientů. Dále systém může nastavit dobu platnosti datových položek nebo použít zásady vyřazení, aby se aktualizace dat opakovaně načítaly do mezipaměti.|
| [Ukládání obsahu do mezipaměti](cache-aspnet-output-cache-provider.md) | Většina webových stránek se generuje ze šablon, které obsahují záhlaví, zápatí, panely nástrojů, nabídky a další prvky. Webové stránky se aktualizují zřídka a neměly by se generovat dynamicky. V porovnání s back-endovými úložišti dat poskytne mezipaměť v paměti, jako je Azure Redis Cache, webovým serverům rychlejší přístup k tomuto typu statického obsahu. Tento vzor zkracuje dobu zpracování a načítání serveru, která je potřeba k dynamickému generování obsahu. Umožňuje webovým serverům rychleji reagovat a snižuje počet serverů, které jsou nutné pro zpracování zátěže. Azure Redis Cache poskytuje zprostředkovatele mezipaměti výstupu Redis, který usnadňuje podporu tohoto vzoru pomocí ASP.NET.|
| [Ukládání uživatelských relací do mezipaměti](cache-aspnet-session-state-provider.md) | Tento vzor se běžně používá u nákupních košíků a dalších informací o historii uživatele, které webová aplikace může chtít přidružit k souborům cookie uživatele. Ukládání příliš velkého objemu dat v souboru cookie může mít negativní dopad na výkon, protože velikost souboru cookie roste a předává se a ověřuje s každou žádostí. Typickým řešením je použití souboru cookie jako klíče pro dotazování dat v back-endové databázi. Použití mezipaměti v paměti, jako je Azure Redis Cache, pro přidružení informací k uživateli je mnohem rychlejší než interakce s plně relační databází. |
| Zařazování úloh a zpráv do fronty | Když aplikace obdrží žádost, provedení operací spojených se žádostí často vyžaduje další čas. Běžně se dlouhotrvající operace odkládají do fronty, která se zpracuje později a případně jiným serverem. Tato metoda odkládání práce se označuje jako řazení úloh do fronty. K podpoře front úloh existuje celá řada softwarových komponent. K tomuto účelu je možné využít i Redis Cache, a to jako distribuovanou frontu.|
| Distribuované transakce | Běžně se po aplikacích vyžaduje, aby byly schopné provádět sérii příkazů oproti back-endovému úložišti dat jako jedinou operaci (atomická operace). Všechny příkazy musí být úspěšné nebo se musí vrátit zpět do počátečního stavu. Redis Cache podporuje provádění dávek příkazů jako jediné operace ve formě [transakcí](https://redis.io/topics/transactions). |

## <a name="azure-redis-cache-offerings"></a>Nabídky služby Azure Redis Cache

Služba Azure Redis Cache je dostupná v následujících úrovních:

| Vrstva | Popis |
|---|---|
Basic | Mezipaměť s jedním uzlem. Tato vrstva podporuje více velikostí paměti (250 MB – 53 GB). Jedná se o ideální vrstvu pro vývoj a testování nebo pro méně náročné úlohy. Vrstva Basic nemá žádnou smlouvu o úrovni služeb (SLA). |
| Standard | Replikovaná mezipaměť v konfiguraci primárního a sekundárního uzlu spravovaná Microsoftem, se smlouvou SLA zajišťující vysokou dostupnost (99,9 %). |
| Premium | Vrstva Premium představuje vrstvu připravenou pro podniky. Mezipaměti vrstvy Premium podporují více funkcí a mají větší propustnost s nižší latencí. Mezipaměti ve vrstvě Premium se nasazují na výkonnější hardware a poskytují lepší výkon v porovnání s vrstvou Basic nebo Standard. Tato výhoda znamená, že propustnost mezipaměti o stejné velikosti bude ve vrstvě Premium vyšší než ve vrstvě Standard. |

> [!TIP]
> Další podrobnosti o velikosti, propustnosti a šířce pásma u mezipamětí Premium najdete v [nejčastějších dotazech ke službě Azure Redis Cache](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).
>

Po vytvoření můžete mezipaměť škálovat na vyšší vrstvu. Škálování na nižší vrstvu se nepodporuje. Podrobné pokyny ke škálování najdete v článku o [škálování služby Azure Redis Cache](cache-how-to-scale.md) a v článku o [automatizaci operací škálování](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparision"></a>Srovnání funkcí

Na stránce věnované [cenovým úrovním služby Redis Cache](https://azure.microsoft.com/pricing/details/cache/) najdete podrobné srovnání jednotlivých vrstev. V následující tabulce jsou popsány některé funkce podporované jednotlivými vrstvami:

| Popis funkce | Premium | Standard | Basic |
| ------------------- | :-----: | :------: | :---: |
| [Smlouvy o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Trvalost dat Redis](cache-how-to-premium-persistence.md) |✔|-|-|
| [Cluster Redis](cache-how-to-premium-clustering.md) |✔|-|-|
| [Zabezpečení prostřednictvím pravidel brány firewall](cache-configure.md#firewall) |✔|✔|✔|
| [Vyšší míra zabezpečení a izolace pomocí virtuální sítě](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/export](cache-how-to-import-export-data.md) |✔|-|-|
| [Plán aktualizací](cache-administration.md#schedule-updates) |✔|-|-|
| [Geografická replikace](cache-how-to-geo-replication.md) |✔|-|-|
| [Restartování](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Další kroky

* [Rychlý start pro webové aplikace v ASP.NET](cache-web-app-howto.md): Vytvořte si jednoduchou webovou aplikaci v ASP.NET, která používá Azure Redis Cache.
* [Rychlý start pro .NET](cache-dotnet-how-to-use-azure-redis-cache.md): Vytvořte si aplikaci v .NET, která používá Azure Redis Cache.
* [Rychlý start pro Node.js](cache-nodejs-get-started.md): Vytvořte si jednoduchou aplikaci v Node.js, která používá Azure Redis Cache.
* [Rychlý start pro jazyk Java](cache-java-get-started.md): Vytvořte si jednoduchou aplikaci v jazyce Java, která používá Azure Redis Cache.
* [Rychlý start pro Python](cache-python-get-started.md): Vytvořte si aplikaci v Pythonu, která používá Azure Redis Cache.