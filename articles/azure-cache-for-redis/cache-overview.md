---
title: Co je Azure Cache for Redis?
description: Přečtěte si o Azure cache pro Redis, jak povolit ukládání do mezipaměti, ukládání obsahu do mezipaměti, ukládání uživatelských relací do mezipaměti, úlohy a služby Řízení front zpráv a distribuované transakce.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 26f6c8e3aceddc6f766bb43a1e384d761dee32bf
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91631373"
---
# <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure cache pro Redis poskytuje úložiště dat v paměti založené na open source softwaru [Redis](https://redis.io/). Redis vylepšuje výkon a škálovatelnost aplikace, která se používá v úložištích back-end dat. Může zpracovávat velké objemy žádostí o aplikace tím, že udržuje často používaná data v paměti serveru, na kterou je možné zapisovat a číst rychleji. Redis přináší pro moderní aplikace kritická řešení pro úložiště dat s nízkou latencí a vysokou propustností.

Azure cache pro Redis nabízí Redis jako spravovanou službu. Poskytuje zabezpečené a vyhrazené instance serveru Redis a úplnou kompatibilitu s rozhraním Redis API. Služba je provozována společností Microsoft, která je hostována v Azure a dostupná pro libovolnou aplikaci v rámci nebo mimo Azure.

Mezipaměť Azure pro Redis se dá použít jako distribuovaná data nebo mezipaměť obsahu, úložiště relací, zprostředkovatele zpráv a další. Dá se nasadit jako samostatná nebo spolu s jinou databázovou službou Azure, jako je Azure SQL nebo Cosmos DB.

## <a name="key-scenarios"></a>Klíčové scénáře
Azure cache pro Redis vylepšuje výkon aplikace tím, že podporuje běžné vzory architektury aplikací. Mezi nejběžnější případy použití patří:

| Vzor      | Popis                                        |
| ------------ | -------------------------------------------------- |
| [Mezipaměť dat](cache-web-app-cache-aside-leaderboard.md) | Databáze jsou často příliš velké, aby se načetly přímo do mezipaměti. Je běžné použít model doplňování [mezipaměti](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) k načtení dat do mezipaměti, jak je potřeba. Když systém provede změny dat, může systém aktualizovat také mezipaměť, která je poté distribuována jiným klientům. Kromě toho může systém nastavit vypršení platnosti dat nebo použít zásadu vyřazení k aktivaci aktualizací dat do mezipaměti.|
| [Mezipaměť obsahu](cache-aspnet-output-cache-provider.md) | Mnoho webových stránek se generuje ze šablon, které používají statický obsah, jako jsou záhlaví, zápatí a bannery. Tyto statické položky by se neměly často měnit. Použití mezipaměti v paměti poskytuje rychlý přístup ke statickému obsahu v porovnání s úložištěm dat back-endu. Tento model zkracuje dobu zpracování a zatížení serveru, což umožňuje webovým serverům rychlejší odezvu. Umožňuje snížit počet serverů potřebných ke zpracování zatížení. Azure cache pro Redis poskytuje poskytovateli výstupní mezipaměti Redis pro podporu tohoto vzoru s ASP.NET.|
| [Úložiště relací](cache-aspnet-session-state-provider.md) | Tento model se běžně používá u nákupních košíků a dalších dat historie uživatele, která webová aplikace může chtít přidružit k souborům cookie uživatele. Ukládání příliš velkého objemu dat v souboru cookie může mít negativní dopad na výkon, protože velikost souboru cookie roste a předává se a ověřuje s každou žádostí. Typické řešení používá soubor cookie jako klíč k dotazování dat v databázi. Použití mezipaměti v paměti, jako je například Azure cache pro Redis, k přidružení informací k uživateli je mnohem rychlejší než interakce s úplnou relační databází. |
| Zařazování úloh a zpráv do fronty | Aplikace často přidávají úlohy do fronty v případě, že operace přidružené k žádosti převezmou čas na provedení. Déle běžící operace jsou zařazeny do fronty, aby je bylo možné zpracovat v sekvenci, často na jiném serveru.  Tato metoda odkládání práce se označuje jako řazení úloh do fronty. Azure cache pro Redis poskytuje distribuovanou frontu pro povolení tohoto modelu ve vaší aplikaci.|
| Distribuované transakce | Aplikace někdy vyžadují sérii příkazů pro úložiště dat back-end, aby je bylo možné provést jako jedinou atomickou operaci. Všechny příkazy musí být úspěšné nebo se musí vrátit zpět do počátečního stavu. Azure cache pro Redis podporuje spouštění dávek příkazů jako jediné [transakce](https://redis.io/topics/transactions). |

## <a name="redis-versions"></a>Verze Redis

Azure cache pro Redis podporuje Redis verze 4. x a jako verzi Preview 6,0. Provedli jsme rozhodnutí přeskočit Redis 5,0, které vám umožní dostat se k nejnovější verzi. Dřív mezipaměť Azure pro Redis udržovala jenom jednu verzi Redis. V rámci tohoto postupu bude k dispozici novější hlavní upgrade pro vydání a alespoň jednu starší stabilní verzi. Můžete [zvolit, která verze](cache-how-to-version.md) bude pro vaši aplikaci fungovat nejlépe.

> [!NOTE]
> Redis 6,0 je teď ve verzi Preview – Pokud vás zajímáte, [kontaktujte nás](mailto:azurecache@microsoft.com) . Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="service-tiers"></a>Úrovně služby
Mezipaměť Azure pro Redis je k dispozici na následujících úrovních:

| Úroveň | Popis |
|---|---|
| Základní | Mezipaměť s jedním uzlem. Tato úroveň podporuje více velikostí paměti (250 MB až 53 GB) a je ideální pro vývoj a testování a méně důležité úlohy. Úroveň Basic nemá smlouvu o úrovni služeb (SLA). |
| Standard | Replikovaná mezipaměť v konfiguraci se dvěma uzly, primárním serverem nebo replikou, kterou spravuje Azure s smlouvou [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)s vysokou dostupností. |
| Premium | Úroveň Premium je úroveň pro podnik připravený na úrovni podniku. Mezipaměti vrstvy Premium podporují více funkcí a mají větší propustnost s nižší latencí. Mezipaměti ve vrstvě Premium se nasazují na výkonnější hardware a poskytují lepší výkon v porovnání s vrstvou Basic nebo Standard. Tato výhoda znamená, že propustnost mezipaměti stejné velikosti bude vyšší v porovnání s úrovní Standard. |

### <a name="feature-comparison"></a>Porovnání funkcí
[Ceny Azure cache for Redis](https://azure.microsoft.com/pricing/details/cache/) poskytují podrobné porovnání každé úrovně. V následující tabulce jsou popsány některé funkce podporované jednotlivými vrstvami:

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
| [Geografickou replikací](cache-how-to-geo-replication.md) |✔|-|-|
| [Restartování](cache-administration.md#reboot) |✔|✔|✔|

### <a name="choosing-the-right-tier"></a>Výběr správné úrovně
Při výběru mezipaměti Azure pro úroveň Redis byste měli vzít v úvahu následující:

* **Paměť**: úrovně Basic a Standard nabízejí 250 MB – 53 GB. Úroveň Premium nabízí až 1,2 TB (jako cluster) nebo 120 GB (neclusterovaný). Další informace najdete v tématu [ceny služby Azure cache pro Redis](https://azure.microsoft.com/pricing/details/cache/).
* **Výkon sítě**: Pokud máte úlohu, která vyžaduje vysokou propustnost, úroveň Premium nabízí větší šířku pásma v porovnání se standardem nebo základním. Kromě toho v každé vrstvě mají větší velikost mezipaměti větší šířku pásma kvůli základnímu virtuálnímu počítači, který hostuje mezipaměť. Další informace najdete v tématu [o výkonu služby Azure cache pro Redis](cache-planning-faq.md#azure-cache-for-redis-performance).
* **Propustnost**: úroveň Premium nabízí maximální dostupnou propustnost. Pokud server mezipaměti nebo klient dosáhne limitů šířky pásma, může docházet k vypršení časového limitu na straně klienta. Další informace najdete v následující tabulce.
* **Vysoká dostupnost**: mezipaměť Azure pro Redis zaručuje, že mezipaměť Standard/Premium je dostupná podle naší [smlouvy SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Smlouva SLA pokrývá jenom připojení ke koncovým bodům mezipaměti. Smlouva SLA nepokrývá ochranu před ztrátou dat. Pro zvýšení odolnosti proti ztrátě dat doporučujeme používat funkci trvalosti dat Redis na úrovni Premium.
* **Trvalost dat Redis**: úroveň Premium vám umožňuje uchovávat data z mezipaměti v účtu Azure Storage. V mezipaměti Basic a Standard jsou všechna data uložena pouze v paměti. Základní problémy s infrastrukturou můžou způsobit ztrátu dat. Pro zvýšení odolnosti proti ztrátě dat doporučujeme používat funkci trvalosti dat Redis na úrovni Premium. Azure cache for Redis nabízí možnosti RDB a AOF (Preview) v Redis persistenci. Další informace najdete v tématu [Konfigurace trvalosti pro službu Azure cache Premium pro Redis](cache-how-to-premium-persistence.md).
* **Cluster Redis**: Pokud chcete vytvořit mezipaměti větší než 120 GB nebo horizontálních oddílů data napříč několika uzly Redis, můžete použít clustering Redis, který je k dispozici na úrovni Premium. Každý uzel se skládá z dvojice mezipaměti primárního/repliky pro zajištění vysoké dostupnosti. Další informace najdete v tématu [Konfigurace clusteringu pro službu Azure cache v úrovni Premium pro Redis](cache-how-to-premium-clustering.md).
* **Rozšířené zabezpečení a izolace sítě**: nasazení služby Azure Virtual Network (VNET) poskytuje rozšířené zabezpečení a izolaci pro službu Azure cache pro Redis a podsítě, zásady řízení přístupu a další funkce, které umožňují další omezení přístupu. Další informace najdete v tématu [jak nakonfigurovat Virtual Network podporu pro Azure cache Premium pro Redis](cache-how-to-premium-vnet.md).
* **Konfigurace Redis**: v úrovních Standard a Premium můžete nakonfigurovat Redis pro oznámení na místě.
* **Maximální počet připojení klientů**: úroveň Premium nabízí maximální počet klientů, kteří se mohou připojit k Redis s větším počtem připojení pro mezipaměti větší velikosti. Clustering nezvyšuje počet připojení dostupných pro clusterovou mezipaměť. Další informace najdete v tématu [ceny služby Azure cache pro Redis](https://azure.microsoft.com/pricing/details/cache/).
* **Vyhrazená jádro pro Redis Server**: na úrovni Premium mají všechny velikosti mezipaměti vyhrazenou jádro pro Redis. V úrovních Basic a Standard má velikost C1 a vyšší velikost pro server Redis.
* **Zpracování s jedním vláknem**: Redis, podle návrhu, používá pouze jedno vlákno pro zpracování příkazu. Azure cache pro Redis využívá i další jádra pro zpracování v/v. Větší množství jader zvyšuje výkon při propustnosti, i když nemusí způsobit Lineární škálování. Kromě toho větší velikosti virtuálních počítačů obvykle přináší větší omezení šířky pásma než menší. To vám pomůže vyhnout se sytosti sítě, což způsobí vypršení časového limitu aplikace.
* **Vylepšení výkonu**: mezipamětí v úrovni Premium se nasazují na hardware, který má rychlejší procesory, což zajišťuje vyšší výkon v porovnání s úrovní Basic nebo Standard. Mezipaměti úrovně Premium mají vyšší propustnost a nižší latenci. Další informace najdete v tématu [výkon služby Azure cache pro Redis](cache-planning-faq.md#azure-cache-for-redis-performance) .

Mezipaměť můžete po vytvoření škálovat až do vyšší úrovně. Škálování na nižší vrstvu se nepodporuje. Podrobné pokyny k škálování najdete v tématu [Jak škálovat Azure cache pro Redis](cache-how-to-scale.md) a [Jak automatizovat operaci škálování](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Další kroky
* [Rychlý Start webové aplikace v ASP.NET](cache-web-app-howto.md) Vytvořte jednoduchou webovou aplikaci v ASP.NET, která používá Azure cache pro Redis.
* [Rychlý Start .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Vytvořte aplikaci .NET, která používá službu Azure cache pro Redis.
* [Rychlý Start .NET Core](cache-dotnet-core-quickstart.md) Vytvořte aplikaci .NET Core, která používá službu Azure cache pro Redis.
* [ Rychlý startNode.js](cache-nodejs-get-started.md) Vytvořte jednoduchou aplikaci Node.js, která používá mezipaměť Azure pro Redis.
* [Rychlý Start Java](cache-java-get-started.md) Vytvořte jednoduchou aplikaci Java, která používá Azure cache pro Redis.
* [Rychlý Start Pythonu](cache-python-get-started.md) Vytvořte aplikaci v Pythonu, která používá službu Azure cache pro Redis.
