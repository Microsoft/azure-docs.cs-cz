---
title: Co je Azure Cache for Redis?
description: Přečtěte si o Azure cache pro Redis, jak povolit ukládání do mezipaměti, ukládání obsahu do mezipaměti, ukládání uživatelských relací do mezipaměti, úlohy a služby Řízení front zpráv a distribuované transakce.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 03/11/2020
ms.openlocfilehash: cd4e7c8e2693c25f3fc092fb53874a97cfd62434
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113200"
---
# <a name="azure-cache-for-redis-description"></a>Popis služby Azure Cache for Redis

Azure cache pro Redis poskytuje úložiště dat v paměti založené na open source softwaru [Redis](https://redis.io/). Redis při použití jako mezipaměť vylepšuje výkon a škálovatelnost systémů, které spoléhají na úložiště dat back-endu. Zvýšení výkonu se zlepšuje zkopírováním často používaných dat do rychlého úložiště, které se nachází blízko aplikace. V případě služby Azure cache pro Redis je toto rychlé úložiště v paměti místo načítání z disku databází.

Mezipaměť Azure pro Redis se dá použít jako úložiště datové struktury v paměti, distribuované nerelační databáze a zprostředkovatele zpráv. Lepšího výkonu aplikací se dosahuje díky nízké latenci a vysoké propustnosti modulu Redis.

Azure cache pro Redis poskytuje přístup k zabezpečené vyhrazené mezipaměti Redis. Služba Azure cache pro Redis je spravovaná Microsoftem, která je hostovaná v Azure, a dostupná pro libovolnou aplikaci v rámci Azure i mimo ni. Kromě toho Azure Redis for Cache používá strategii replikace bez disků, což zlepšuje kompatibilitu s využitím odvětví platební karty.

## <a name="using-azure-cache-for-redis"></a>Použití mezipaměti Azure pro Redis

Azure cache pro Redis vylepšuje výkon aplikace tím, že podporuje běžné vzory architektury aplikací. Mezi nejběžnější případy použití patří:

| Vzor      | Popis                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Databáze jsou často příliš velké, aby se načetly přímo do mezipaměti. Je běžné použít model doplňování [mezipaměti](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) k načtení dat do mezipaměti, jak je potřeba. Když systém provede změny dat, může systém aktualizovat také mezipaměť, která je poté distribuována jiným klientům. Kromě toho může systém nastavit vypršení platnosti dat nebo použít zásadu vyřazení k aktivaci aktualizací dat do mezipaměti.|
| [Ukládání obsahu do mezipaměti](cache-aspnet-output-cache-provider.md) | Mnoho webových stránek se generuje ze šablon, které používají statický obsah, jako jsou záhlaví, zápatí a bannery. Tyto statické položky by se neměly často měnit. Použití mezipaměti v paměti poskytuje rychlý přístup ke statickému obsahu v porovnání s úložištěm dat back-endu. Tento model zkracuje dobu zpracování a zatížení serveru, což umožňuje webovým serverům rychlejší odezvu. Umožňuje snížit počet serverů potřebných ke zpracování zatížení. Azure cache pro Redis poskytuje poskytovateli výstupní mezipaměti Redis pro podporu tohoto vzoru s ASP.NET.|
| [Ukládání uživatelských relací do mezipaměti](cache-aspnet-session-state-provider.md) | Tento model se běžně používá u nákupních košíků a dalších dat historie uživatele, která webová aplikace může chtít přidružit k souborům cookie uživatele. Ukládání příliš velkého objemu dat v souboru cookie může mít negativní dopad na výkon, protože velikost souboru cookie roste a předává se a ověřuje s každou žádostí. Typické řešení používá soubor cookie jako klíč k dotazování dat v databázi. Použití mezipaměti v paměti, jako je například Azure cache pro Redis, k přidružení informací k uživateli je mnohem rychlejší než interakce s úplnou relační databází. |
| Zařazování úloh a zpráv do fronty | Aplikace často přidávají úlohy do fronty v případě, že operace přidružené k žádosti převezmou čas na provedení. Déle běžící operace jsou zařazeny do fronty, aby je bylo možné zpracovat v sekvenci, často na jiném serveru.  Tato metoda odkládání práce se označuje jako řazení úloh do fronty. Azure cache pro Redis poskytuje distribuovanou frontu pro povolení tohoto modelu ve vaší aplikaci.|
| Distribuované transakce | Aplikace někdy vyžadují sérii příkazů pro úložiště dat back-end, aby je bylo možné provést jako jedinou atomickou operaci. Všechny příkazy musí být úspěšné nebo se musí vrátit zpět do počátečního stavu. Azure cache pro Redis podporuje spouštění dávek příkazů jako jediné [transakce](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Mezipaměť Azure pro nabídky Redis

Mezipaměť Azure pro Redis je k dispozici na následujících úrovních:

| Úroveň | Popis |
|---|---|
Základní | Mezipaměť s jedním uzlem. Tato úroveň podporuje více velikostí paměti (250 MB až 53 GB) a je ideální pro vývoj a testování a méně důležité úlohy. Vrstva Basic nemá žádnou smlouvu o úrovni služeb (SLA). |
| Standard | Replikovaná mezipaměť v konfiguraci se dvěma uzly, primární a sekundární, spravovaná pomocí Azure s vysokou dostupností (99,9%) |
| Premium | Úroveň Premium je úroveň pro podnik připravený na úrovni podniku. Mezipaměti vrstvy Premium podporují více funkcí a mají větší propustnost s nižší latencí. Mezipaměti ve vrstvě Premium se nasazují na výkonnější hardware a poskytují lepší výkon v porovnání s vrstvou Basic nebo Standard. Tato výhoda znamená, že propustnost mezipaměti stejné velikosti bude vyšší v porovnání s úrovní Standard. |

> [!TIP]
> Další informace o velikosti, propustnosti a šířce pásma pomocí prémiových mezipamětí najdete v tématu [Nejčastější dotazy k Azure cache pro Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

Mezipaměť můžete po vytvoření škálovat až do vyšší úrovně. Škálování na nižší vrstvu se nepodporuje. Podrobné pokyny k škálování najdete v tématu [Jak škálovat Azure cache pro Redis](cache-how-to-scale.md) a [Jak automatizovat operaci škálování](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Porovnání funkcí

Stránka s [cenami Azure cache for Redis](https://azure.microsoft.com/pricing/details/cache/) poskytuje podrobné porovnání každé úrovně. V následující tabulce jsou popsány některé funkce podporované jednotlivými vrstvami:

| Popis funkce | Premium | Standard | Základní |
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

* [Rychlý Start webové aplikace v ASP.NET](cache-web-app-howto.md) Vytvořte jednoduchou webovou aplikaci v ASP.NET, která používá Azure cache pro Redis.
* [Rychlý Start .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Vytvořte aplikaci .NET, která používá službu Azure cache pro Redis.
* [Rychlý Start .NET Core](cache-dotnet-core-quickstart.md) Vytvořte aplikaci .NET Core, která používá službu Azure cache pro Redis.
* [Rychlý Start pro Node. js](cache-nodejs-get-started.md) Vytvořte jednoduchou aplikaci Node. js, která používá službu Azure cache pro Redis.
* [Rychlý Start Java](cache-java-get-started.md) Vytvořte jednoduchou aplikaci Java, která používá Azure cache pro Redis.
* [Rychlý Start Pythonu](cache-python-get-started.md) Vytvořte aplikaci v Pythonu, která používá službu Azure cache pro Redis.
