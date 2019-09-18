---
title: Co je Azure cache pro Redis? | Dokumenty Microsoft
description: Přečtěte si, co je Azure cache pro Redis a jak se běžně používá.
services: cache
documentationcenter: ''
author: yegu-ms
manager: martinekuan
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.topic: overview
ms.date: 03/26/2018
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 1f0c943bed473178dadb09cfb9d355821e5236e8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066850"
---
# <a name="azure-cache-for-redis-description"></a>Popis služby Azure cache pro Redis

Mezipaměť Azure pro Redis vychází z oblíbeného softwaru [Redis](https://redis.io/). Obvykle se používá jako mezipaměť ke zlepšení výkonu a škálovatelnosti systémů, které z velké části spoléhají na back-endová úložiště dat. Lepšího výkonu se dosahuje dočasným zkopírováním často používaných dat do rychlého úložiště umístěného v blízkosti aplikace. V případě [Azure cache pro Redis](https://redis.io/)se toto rychlé úložiště nachází v paměti s Azure cache pro Redis namísto načítání z disku databází.

Mezipaměť Azure pro Redis se dá použít taky jako úložiště struktur dat v paměti, distribuovaná nerelační databáze a zprostředkovatel zpráv. Lepšího výkonu aplikací se dosahuje díky nízké latenci a vysoké propustnosti modulu Redis.

Azure cache pro Redis poskytuje přístup k zabezpečené vyhrazené mezipaměti Redis. Služba Azure cache pro Redis je spravovaná Microsoftem, která je hostovaná v Azure, a dostupná pro libovolnou aplikaci v rámci Azure i mimo ni.

## <a name="using-azure-cache-for-redis"></a>Použití mezipaměti Azure pro Redis

Existuje mnoho běžných vzorů, ve kterých se mezipaměť Azure pro Redis používá k podpoře architektury aplikace nebo ke zlepšení výkonu aplikace. Mezi nejběžnější případy použití patří:

| Vzor      | Popis                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Vzhledem k tomu, že databáze může být rozsáhlá, nedoporučuje se načítat celou databázi do mezipaměti. Běžně se využívá princip [s doplňováním mezipaměti aplikací](https://docs.microsoft.com/azure/architecture/patterns/cache-aside), při kterém se datové položky načítají do mezipaměti pouze v případě potřeby. Když systém provede změny v back-endových datech, může také současně aktualizovat mezipaměť, která se rozdistribuuje do ostatních klientů. Dále systém může nastavit dobu platnosti datových položek nebo použít zásady vyřazení, aby se aktualizace dat opakovaně načítaly do mezipaměti.|
| [Ukládání obsahu do mezipaměti](cache-aspnet-output-cache-provider.md) | Většina webových stránek se generuje ze šablon, které obsahují záhlaví, zápatí, panely nástrojů, nabídky a další prvky. Webové stránky se aktualizují zřídka a neměly by se generovat dynamicky. Použití mezipaměti v paměti, jako je například Azure cache pro Redis, poskytne webovým serverům rychlý přístup k tomuto typu statického obsahu v porovnání s úložištěm dat back-endu. Tento vzor zkracuje dobu zpracování a načítání serveru, která je potřeba k dynamickému generování obsahu. Umožňuje webovým serverům rychleji reagovat a snižuje počet serverů, které jsou nutné pro zpracování zátěže. Azure cache pro Redis poskytuje poskytovateli výstupní mezipaměti Redis, aby pomohlo tento model podporovat s ASP.NET.|
| [Ukládání uživatelských relací do mezipaměti](cache-aspnet-session-state-provider.md) | Tento vzor se běžně používá u nákupních košíků a dalších informací o historii uživatele, které webová aplikace může chtít přidružit k souborům cookie uživatele. Ukládání příliš velkého objemu dat v souboru cookie může mít negativní dopad na výkon, protože velikost souboru cookie roste a předává se a ověřuje s každou žádostí. Typickým řešením je použití souboru cookie jako klíče pro dotazování dat v back-endové databázi. Použití mezipaměti v paměti, jako je například Azure cache pro Redis, k přidružení informací k uživateli je mnohem rychlejší než interakce s úplnou relační databází. |
| Zařazování úloh a zpráv do fronty | Když aplikace obdrží žádost, provedení operací spojených se žádostí často vyžaduje další čas. Běžně se dlouhotrvající operace odkládají do fronty, která se zpracuje později a případně jiným serverem. Tato metoda odkládání práce se označuje jako řazení úloh do fronty. K podpoře front úloh existuje celá řada softwarových komponent. Mezipaměť Azure pro Redis také slouží k tomuto účelu i k distribuované frontě.|
| Distribuované transakce | Běžně se po aplikacích vyžaduje, aby byly schopné provádět sérii příkazů oproti back-endovému úložišti dat jako jedinou operaci (atomická operace). Všechny příkazy musí být úspěšné nebo se musí vrátit zpět do počátečního stavu. Azure cache pro Redis podporuje spuštění dávky příkazů jako jediné operace ve formě [transakcí](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Mezipaměť Azure pro nabídky Redis

Mezipaměť Azure pro Redis je k dispozici na následujících úrovních:

| Vrstva | Popis |
|---|---|
Basic | Mezipaměť s jedním uzlem. Tato vrstva podporuje více velikostí paměti (250 MB – 53 GB). Jedná se o ideální vrstvu pro vývoj a testování nebo pro méně náročné úlohy. Vrstva Basic nemá žádnou smlouvu o úrovni služeb (SLA). |
| Standard | Replikovaná mezipaměť v konfiguraci primárního a sekundárního uzlu spravovaná Microsoftem, se smlouvou SLA zajišťující vysokou dostupnost (99,9 %). |
| Premium | Úroveň Premium je úroveň pro podnik připravený na úrovni podniku. Mezipaměti vrstvy Premium podporují více funkcí a mají větší propustnost s nižší latencí. Mezipaměti ve vrstvě Premium se nasazují na výkonnější hardware a poskytují lepší výkon v porovnání s vrstvou Basic nebo Standard. Tato výhoda znamená, že propustnost mezipaměti stejné velikosti bude vyšší v porovnání s úrovní Standard. |

> [!TIP]
> Další informace o velikosti, propustnosti a šířce pásma pomocí prémiových mezipamětí najdete v tématu [Nejčastější dotazy k Azure cache pro Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

Po vytvoření můžete mezipaměť škálovat na vyšší vrstvu. Škálování na nižší vrstvu se nepodporuje. Podrobné pokyny k škálování najdete v tématu [Jak škálovat Azure cache pro Redis](cache-how-to-scale.md) a [Jak automatizovat operaci škálování](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Porovnání funkcí

Stránka s [cenami Azure cache for Redis](https://azure.microsoft.com/pricing/details/cache/) poskytuje podrobné porovnání každé úrovně. V následující tabulce jsou popsány některé funkce podporované jednotlivými vrstvami:

| Popis funkce | Premium | Standard | Basic |
| ------------------- | :-----: | :------: | :---: |
| [Smlouvy o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Trvalost dat Redis](cache-how-to-premium-persistence.md) |✔|-|-|
| [Cluster Redis](cache-how-to-premium-clustering.md) |✔|-|-|
| [Zabezpečení prostřednictvím pravidel brány firewall](cache-configure.md#firewall) |✔|✔|✔|
| Šifrování při přenosu |✔|✔|✔|
| [Vyšší míra zabezpečení a izolace pomocí virtuální sítě](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/export](cache-how-to-import-export-data.md) |✔|-|-|
| [Plánované aktualizace](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Geografická replikace](cache-how-to-geo-replication.md) |✔|-|-|
| [Restartování](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Další postup

* [Rychlý Start webové aplikace v ASP.NET](cache-web-app-howto.md) Vytvořte jednoduchou webovou aplikaci v ASP.NET, která používá Azure cache pro Redis.
* [Rychlý Start .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Vytvořte aplikaci .NET, která používá službu Azure cache pro Redis.
* [Rychlý Start .NET Core](cache-dotnet-core-quickstart.md) Vytvořte aplikaci .NET Core, která používá službu Azure cache pro Redis.
* [Rychlý Start pro Node. js](cache-nodejs-get-started.md) Vytvořte jednoduchou aplikaci Node. js, která používá službu Azure cache pro Redis.
* [Rychlý Start Java](cache-java-get-started.md) Vytvořte jednoduchou aplikaci Java, která používá Azure cache pro Redis.
* [Rychlý Start Pythonu](cache-python-get-started.md) Vytvořte aplikaci v Pythonu, která používá službu Azure cache pro Redis.
