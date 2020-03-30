---
title: Co je Azure Cache for Redis?
description: Další informace o Azure Cache for Redis umožňují ukládání do mezipaměti stranou, ukládání obsahu do mezipaměti, ukládání uživatelských relací do mezipaměti, vytváření front úloh a zpráv a distribuované transakce.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 03/11/2020
ms.openlocfilehash: 38936000e426d560237295105b5456429d9ae16d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79126368"
---
# <a name="azure-cache-for-redis-description"></a>Popis služby Azure Cache for Redis

Azure Cache for Redis poskytuje úložiště dat v paměti založené na open source softwaru [Redis](https://redis.io/). Při použití jako mezipaměti redis zlepšuje výkon a škálovatelnost systémů, které jsou silně závislé na úložišti dat back-endu. Výkon je lepší zkopírováním často přístupná data do rychlého úložiště umístěného v blízkosti aplikace. S Azure Cache pro Redis se toto rychlé úložiště nachází v paměti, místo aby se načítá z disku databází.

Azure Cache for Redis lze použít jako úložiště datových struktur v paměti, distribuované nerelační databáze a zprostředkovatele zpráv. Lepšího výkonu aplikací se dosahuje díky nízké latenci a vysoké propustnosti modulu Redis.

Azure Cache for Redis poskytuje přístup k zabezpečené, vyhrazené mezipaměti Redis. Azure Cache for Redis spravuje Microsoft, hostuje v Azure a je přístupný pro všechny aplikace v rámci Azure nebo mimo něj.

## <a name="using-azure-cache-for-redis"></a>Použití Azure Cache pro Redis

Azure Cache for Redis zlepšuje výkon aplikací podporou běžných vzorů architektury aplikací. Mezi nejběžnější případy použití patří:

| Vzor      | Popis                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Databáze jsou často příliš velké načtení přímo do mezipaměti. Je běžné používat vzor [stranou mezipaměti](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) k načtení dat do mezipaměti pouze podle potřeby. Když systém provede změny dat, systém může také aktualizovat mezipaměť, která je pak distribuována do jiných klientů. Kromě toho systém může nastavit vypršení platnosti dat nebo použít zásady vyřazovacího příkazu k aktivaci aktualizací dat do mezipaměti.|
| [Ukládání obsahu do mezipaměti](cache-aspnet-output-cache-provider.md) | Mnoho webových stránek je generováno ze šablon, které používají statický obsah, jako jsou záhlaví, zápatí, bannery. Tyto statické položky by se neměly měnit často. Použití mezipaměti v paměti poskytuje rychlý přístup ke statickému obsahu ve srovnání s back-endovými datovými úložišti. Tento vzor zkracuje dobu zpracování a zatížení serveru, což umožňuje webovým serverům lépe reagovat. To vám umožní snížit počet serverů potřebných ke zpracování zatížení. Azure Cache for Redis poskytuje zprostředkovatele výstupní mezipaměti Redis pro podporu tohoto vzoru s ASP.NET.|
| [Ukládání uživatelských relací do mezipaměti](cache-aspnet-session-state-provider.md) | Tento vzor se běžně používá s nákupními košíky a dalšími daty historie uživatelů, které může webová aplikace chtít přidružit k souborům cookie uživatelů. Ukládání příliš velkého objemu dat v souboru cookie může mít negativní dopad na výkon, protože velikost souboru cookie roste a předává se a ověřuje s každou žádostí. Typické řešení používá soubor cookie jako klíč k dotazování na data v databázi. Použití mezipaměti v paměti, jako je Azure Cache pro Redis, k přidružení informací k uživateli je mnohem rychlejší než interakce s úplnou relační databází. |
| Zařazování úloh a zpráv do fronty | Aplikace často přidávají úkoly do fronty, když operace spojené s požadavkem nějakou dobu trvat. Delší spuštěné operace jsou zařazeny do fronty, které mají být zpracovány postupně, často jiným serverem.  Tato metoda odkládání práce se označuje jako řazení úloh do fronty. Azure Cache for Redis poskytuje distribuovanou frontu, která povoluje tento vzor ve vaší aplikaci.|
| Distribuované transakce | Aplikace někdy vyžadují řadu příkazů proti back-endu úložiště dat spustit jako jednu atomické operace. Všechny příkazy musí být úspěšné nebo se musí vrátit zpět do počátečního stavu. Azure Cache for Redis podporuje provádění dávky příkazů jako jedné [transakce](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Nabídky Azure Cache for Redis

Azure Cache for Redis je k dispozici v následujících úrovních:

| Úroveň | Popis |
|---|---|
Basic | Mezipaměť s jedním uzlem. Tato vrstva podporuje více velikostí paměti (250 MB - 53 GB) a je ideální pro vývoj/testování a nekritické úlohy. Vrstva Basic nemá žádnou smlouvu o úrovni služeb (SLA). |
| Standard | Replikovaná mezipaměť ve dvouuzlůové, primární/sekundární konfiguraci spravované Azure s vysokou dostupností s la (99,9 %) |
| Premium | Úroveň Premium je úroveň připravenosti pro podniky. Mezipaměti vrstvy Premium podporují více funkcí a mají větší propustnost s nižší latencí. Mezipaměti ve vrstvě Premium se nasazují na výkonnější hardware a poskytují lepší výkon v porovnání s vrstvou Basic nebo Standard. Tato výhoda znamená, že propustnost pro mezipaměť stejné velikosti bude vyšší v premium ve srovnání s úrovní Standard. |

> [!TIP]
> Další informace o velikosti, propustnosti a šířce pásma s prémiovými mezipamětmi najdete v [tématu Azure Cache for Redis FAQ](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

Můžete škálovat mezipaměť až na vyšší úroveň po jeho vytvoření. Škálování na nižší vrstvu se nepodporuje. Podrobné pokyny pro škálování najdete v tématu [Jak škálovat mezipaměť Azure pro Redis](cache-how-to-scale.md) a [Jak automatizovat operaci škálování](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Porovnání funkcí

Stránka [Azure Cache for Redis Pricing](https://azure.microsoft.com/pricing/details/cache/) poskytuje podrobné porovnání jednotlivých úrovní. V následující tabulce jsou popsány některé funkce podporované jednotlivými vrstvami:

| Popis funkce | Premium | Standard | Basic |
| ------------------- | :-----: | :------: | :---: |
| [Smlouvy o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Trvalost dat Redis](cache-how-to-premium-persistence.md) |✔|-|-|
| [Cluster Redis](cache-how-to-premium-clustering.md) |✔|-|-|
| [Zabezpečení prostřednictvím pravidel brány firewall](cache-configure.md#firewall) |✔|✔|✔|
| Šifrování během přenosu |✔|✔|✔|
| [Vyšší míra zabezpečení a izolace pomocí virtuální sítě](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/export](cache-how-to-import-export-data.md) |✔|-|-|
| [Plánované aktualizace](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Geografická replikace](cache-how-to-geo-replication.md) |✔|-|-|
| [Restartování](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Další kroky

* [Úvodní příručka ASP.NET Web Appu](cache-web-app-howto.md) Vytvořte jednoduchou ASP.NET webovou aplikaci, která používá Azure Cache for Redis.
* [Rychlý start rozhraní .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Vytvořte aplikaci .NET, která používá mezipaměť Azure pro Redis.
* [Rychlý start jádra rozhraní .NET](cache-dotnet-core-quickstart.md) Vytvořte aplikaci .NET Core, která používá mezipaměť Azure pro Redis.
* [Rychlý start node.js](cache-nodejs-get-started.md) Vytvořte jednoduchou aplikaci Node.js, která používá Azure Cache for Redis.
* [Rychlý start javy](cache-java-get-started.md) Vytvořte jednoduchou aplikaci Java, která používá Azure Cache pro Redis.
* [Rychlý start pythonu](cache-python-get-started.md) Vytvořte aplikaci Pythonu, která používá Azure Cache pro Redis.
