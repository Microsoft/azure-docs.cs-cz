---
title: Co je Azure mezipaměti Redis? | Dokumenty Microsoft
description: Zjistěte, co je Azure mezipaměti Redis a jak se běžně používá.
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: overview
ms.date: 03/26/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 1f41172a493f3e5e1b98cbba32b0180cee16c072
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53019196"
---
# <a name="what-is-azure-cache-for-redis"></a>Co je Azure mezipaměti Redis

Pro Redis Azure Cache je založená na oblíbené open source [mezipaměti Azure Redis](https://redis.io/). Obvykle se používá jako mezipaměť ke zlepšení výkonu a škálovatelnosti systémů, které z velké části spoléhají na back-endová úložiště dat. Lepšího výkonu se dosahuje dočasným zkopírováním často používaných dat do rychlého úložiště umístěného v blízkosti aplikace. S [mezipaměti Azure Redis](https://redis.io/), toto rychlé úložiště je umístěné v paměti s mezipamětí Azure pro Redis místo se načetl z disku databáze.

Azure mezipaměti Redis také slouží jako úložiště dat v paměti struktury, distribuované nerelační databáze a zprostředkovatele zpráv. Lepšího výkonu aplikací se dosahuje díky nízké latenci a vysoké propustnosti modulu Redis.

Mezipaměť Azure pro Redis poskytuje přístup k zabezpečené vyhrazené mezipaměti Azure pro Redis, spravovanou microsoftem hostované v Azure a přístupná pro všechny aplikace v rámci nebo mimo Azure.

## <a name="why-use-azure-cache-for-redis"></a>Proč používat mezipaměť Azure pro Redis

Existuje mnoho společných vzorů použití mezipaměti Redis Azure pro podporu architektury aplikace nebo chcete-li zlepšit výkon aplikace. Mezi nejběžnější případy použití patří:

| Vzor      | Popis                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Vzhledem k tomu, že databáze může být rozsáhlá, nedoporučuje se načítat celou databázi do mezipaměti. Běžně se využívá princip [s doplňováním mezipaměti aplikací](https://docs.microsoft.com/azure/architecture/patterns/cache-aside), při kterém se datové položky načítají do mezipaměti pouze v případě potřeby. Když systém provede změny v back-endových datech, může také současně aktualizovat mezipaměť, která se rozdistribuuje do ostatních klientů. Dále systém může nastavit dobu platnosti datových položek nebo použít zásady vyřazení, aby se aktualizace dat opakovaně načítaly do mezipaměti.|
| [Ukládání obsahu do mezipaměti](cache-aspnet-output-cache-provider.md) | Většina webových stránek se generuje ze šablon, které obsahují záhlaví, zápatí, panely nástrojů, nabídky a další prvky. Webové stránky se aktualizují zřídka a neměly by se generovat dynamicky. Použití mezipaměti v paměti, jako je Azure Cache pro redis cache, vám poskytne vaše webové servery, rychlý přístup k tomuto typu statický obsah ve srovnání s back-endu úložiště. Tento vzor zkracuje dobu zpracování a načítání serveru, která je potřeba k dynamickému generování obsahu. Umožňuje webovým serverům rychleji reagovat a snižuje počet serverů, které jsou nutné pro zpracování zátěže. Mezipaměť Azure pro Redis poskytuje Redis zprostředkovatele mezipaměti výstupu aby bylo možné podporovat tento model s technologií ASP.NET.|
| [Ukládání uživatelských relací do mezipaměti](cache-aspnet-session-state-provider.md) | Tento vzor se běžně používá u nákupních košíků a dalších informací o historii uživatele, které webová aplikace může chtít přidružit k souborům cookie uživatele. Ukládání příliš velkého objemu dat v souboru cookie může mít negativní dopad na výkon, protože velikost souboru cookie roste a předává se a ověřuje s každou žádostí. Typickým řešením je použití souboru cookie jako klíče pro dotazování dat v back-endové databázi. Použití mezipaměti v paměti, jako je Azure mezipaměti Redis, pro informace o přidružení uživatele je mnohem rychlejší než interakci s relační databáze. |
| Zařazování úloh a zpráv do fronty | Když aplikace obdrží žádost, provedení operací spojených se žádostí často vyžaduje další čas. Běžně se dlouhotrvající operace odkládají do fronty, která se zpracuje později a případně jiným serverem. Tato metoda odkládání práce se označuje jako řazení úloh do fronty. K podpoře front úloh existuje celá řada softwarových komponent. Mezipaměť Azure Redis je slouží také tento účel i distribuované fronty.|
| Distribuované transakce | Běžně se po aplikacích vyžaduje, aby byly schopné provádět sérii příkazů oproti back-endovému úložišti dat jako jedinou operaci (atomická operace). Všechny příkazy musí být úspěšné nebo se musí vrátit zpět do počátečního stavu. Mezipaměti Redis Azure podporuje dávkové příkazy se provádí jako jediná operace v podobě [transakce](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Mezipaměť Azure pro Redis nabídky

Azure mezipaměti Redis je k dispozici v následujících úrovních:

| Vrstva | Popis |
|---|---|
Basic | Mezipaměť s jedním uzlem. Tato vrstva podporuje více velikostí paměti (250 MB – 53 GB). Jedná se o ideální vrstvu pro vývoj a testování nebo pro méně náročné úlohy. Vrstva Basic nemá žádnou smlouvu o úrovni služeb (SLA). |
| Standard | Replikovaná mezipaměť v konfiguraci primárního a sekundárního uzlu spravovaná Microsoftem, se smlouvou SLA zajišťující vysokou dostupnost (99,9 %). |
| Premium | Vrstva Premium představuje vrstvu připravenou pro podniky. Mezipaměti vrstvy Premium podporují více funkcí a mají větší propustnost s nižší latencí. Mezipaměti ve vrstvě Premium se nasazují na výkonnější hardware a poskytují lepší výkon v porovnání s vrstvou Basic nebo Standard. Tato výhoda znamená, že propustnost mezipaměti o stejné velikosti bude ve vrstvě Premium vyšší než ve vrstvě Standard. |

> [!TIP]
> Další informace o velikosti, propustnosti a šířky pásma u prémiových mezipamětí najdete v tématu [mezipaměti Azure redis Cache – nejčastější dotazy](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

Po vytvoření můžete mezipaměť škálovat na vyšší vrstvu. Škálování na nižší vrstvu se nepodporuje. Podrobné pokyny škálování najdete v tématu [postup škálování Azure mezipaměti Redis](cache-how-to-scale.md) a [jak automatizovat operaci škálování](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparision"></a>Srovnání funkcí

[Mezipaměti Azure redis Cache ceny](https://azure.microsoft.com/pricing/details/cache/) stránka obsahuje podrobné porovnání jednotlivých vrstev. V následující tabulce jsou popsány některé funkce podporované jednotlivými vrstvami:

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

## <a name="next-steps"></a>Další postup

* [Rychlý úvod k aplikacím ASP.NET Web](cache-web-app-howto.md) vytvoření jednoduché webové aplikace v ASP.NET, která používá Azure Cache pro Redis.
* [Rychlý úvod k .NET](cache-dotnet-how-to-use-azure-redis-cache.md) vytvoření aplikace .NET využívající Azure Cache pro Redis.
* [Rychlý úvod k .NET core](cache-dotnet-core-quickstart.md) vytvoření aplikace .NET Core, která používá Azure Cache pro Redis.
* [Rychlý úvod k Node.js](cache-nodejs-get-started.md) vytvoření jednoduché aplikace v Node.js používající Azure Cache pro Redis.
* [Rychlý start Java](cache-java-get-started.md) vytvořit jednoduchou aplikaci v Javě, která používá Azure Cache pro Redis.
* [Rychlý start Python](cache-python-get-started.md) vytvoření aplikace v Pythonu, který používá Azure Cache pro Redis.
