---
title: Co je Azure Cache for Redis?
description: Přečtěte si o Azure cache pro Redis, jak povolit ukládání do mezipaměti, ukládání obsahu do mezipaměti, ukládání uživatelských relací do mezipaměti, úlohy a služby Řízení front zpráv a distribuované transakce.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 02/08/2021
ms.openlocfilehash: 9b1176f579754d714490297e9ff960d7808bd834
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383156"
---
# <a name="about-azure-cache-for-redis"></a>Informace o službě Azure Cache for Redis
Azure cache pro Redis poskytuje úložiště dat v paměti založené na softwaru [Redis](https://redis.io/) . Redis vylepšuje výkon a škálovatelnost aplikace, která se používá v úložištích back-end dat. Může zpracovávat velké objemy žádostí o aplikace tím, že udržuje často používaná data v paměti serveru, na kterou je možné zapisovat a číst rychleji. Redis přináší pro moderní aplikace kritická řešení pro úložiště dat s nízkou latencí a vysokou propustností.

Azure cache for Redis nabízí jako spravovanou službu Redis Open Source (OSS Redis) i komerční produkt z Redis Labs (Redis Enterprise). Poskytuje zabezpečené a vyhrazené instance serveru Redis a úplnou kompatibilitu s rozhraním Redis API. Služba je provozována společností Microsoft, která je hostována v Azure a dostupná pro libovolnou aplikaci v rámci nebo mimo Azure.

Mezipaměť Azure pro Redis se dá použít jako distribuovaná data nebo mezipaměť obsahu, úložiště relací, zprostředkovatele zpráv a další. Dá se nasadit jako samostatná nebo spolu s dalšími databázovými službami Azure, jako je Azure SQL nebo Cosmos DB.

## <a name="key-scenarios"></a>Klíčové scénáře
Azure cache pro Redis vylepšuje výkon aplikace tím, že podporuje běžné vzory architektury aplikací. Mezi nejběžnější případy použití patří:

| Vzor      | Description                                        |
| ------------ | -------------------------------------------------- |
| [Mezipaměť dat](cache-web-app-cache-aside-leaderboard.md) | Databáze jsou často příliš velké, aby se načetly přímo do mezipaměti. Je běžné použít model doplňování [mezipaměti](/azure/architecture/patterns/cache-aside) k načtení dat do mezipaměti, jak je potřeba. Když systém provede změny dat, může systém aktualizovat také mezipaměť, která je poté distribuována jiným klientům. Kromě toho může systém nastavit vypršení platnosti dat nebo použít zásadu vyřazení k aktivaci aktualizací dat do mezipaměti.|
| [Mezipaměť obsahu](cache-aspnet-output-cache-provider.md) | Mnoho webových stránek se generuje ze šablon, které používají statický obsah, jako jsou záhlaví, zápatí a bannery. Tyto statické položky by se neměly často měnit. Použití mezipaměti v paměti poskytuje rychlý přístup ke statickému obsahu v porovnání s úložištěm dat back-endu. Tento model zkracuje dobu zpracování a zatížení serveru, což umožňuje webovým serverům rychlejší odezvu. Umožňuje snížit počet serverů potřebných ke zpracování zatížení. Azure cache pro Redis poskytuje poskytovateli výstupní mezipaměti Redis pro podporu tohoto vzoru s ASP.NET.|
| [Úložiště relací](cache-aspnet-session-state-provider.md) | Tento model se běžně používá u nákupních košíků a dalších dat historie uživatele, která webová aplikace může chtít přidružit k souborům cookie uživatele. Ukládání příliš velkého objemu dat v souboru cookie může mít negativní dopad na výkon, protože velikost souboru cookie roste a předává se a ověřuje s každou žádostí. Typické řešení používá soubor cookie jako klíč k dotazování dat v databázi. Použití mezipaměti v paměti, jako je například Azure cache pro Redis, k přidružení informací k uživateli je mnohem rychlejší než interakce s úplnou relační databází. |
| Zařazování úloh a zpráv do fronty | Aplikace často přidávají úlohy do fronty v případě, že operace přidružené k žádosti převezmou čas na provedení. Déle běžící operace jsou zařazeny do fronty, aby je bylo možné zpracovat v sekvenci, často na jiném serveru.  Tato metoda odkládání práce se označuje jako řazení úloh do fronty. Azure cache pro Redis poskytuje distribuovanou frontu pro povolení tohoto modelu ve vaší aplikaci.|
| Distribuované transakce | Aplikace někdy vyžadují sérii příkazů pro úložiště dat back-end, aby je bylo možné provést jako jedinou atomickou operaci. Všechny příkazy musí být úspěšné nebo se musí vrátit zpět do počátečního stavu. Azure cache pro Redis podporuje spouštění dávek příkazů jako jediné [transakce](https://redis.io/topics/transactions). |

## <a name="redis-versions"></a>Verze Redis

Azure cache pro Redis podporuje OSS Redis verze 4. x a jako verzi Preview 6,0. Provedli jsme rozhodnutí přeskočit Redis 5,0, které vám umožní dostat se k nejnovější verzi. Dřív mezipaměť Azure pro Redis udržovala jenom jednu verzi Redis. V rámci tohoto postupu bude k dispozici novější hlavní upgrade pro vydání a alespoň jednu starší stabilní verzi. Můžete [zvolit, která verze](cache-how-to-version.md) bude pro vaši aplikaci fungovat nejlépe.


## <a name="service-tiers"></a>Úrovně služby
Mezipaměť Azure pro Redis je k dispozici na následujících úrovních:

| Úroveň | Description |
|---|---|
| Basic | Mezipaměť OSS Redis spuštěná na jednom virtuálním počítači. Tato úroveň nemá smlouvu o úrovni služeb (SLA) a je ideální pro vývoj a testování a méně důležité úlohy. |
| Standard | Mezipaměť OSS Redis spuštěná na dvou virtuálních počítačích v replikované konfiguraci. |
| Premium | Vysoce výkonné Redis mezipaměti OSS. Tato úroveň nabízí vyšší propustnost, nižší latenci, lepší dostupnost a další funkce. Mezipaměti úrovně Premium se nasazují na výkonnější virtuální počítače ve srovnání se základními a standardními mezipaměťmi. |
| Enterprise (Preview) | Vysoce výkonné mezipaměti založené na Redis podnikovém softwaru Redis Labs. Tato vrstva podporuje Redis moduly, včetně RediSearch, RedisBloom a RedisTimeSeries. Kromě toho nabízí ještě vyšší dostupnost než úroveň Premium. |
| Enterprise Flash (Preview) | Nákladově efektivní velké mezipaměti využívající software Redis Labs "Redis Enterprise software. Tato vrstva rozšiřuje Redis úložiště dat do nestálé paměti, která je levnější než DRAM na virtuálním počítači. Snižuje celkové náklady na GB paměti. |

### <a name="feature-comparison"></a>Porovnání funkcí
[Ceny Azure cache for Redis](https://azure.microsoft.com/pricing/details/cache/) poskytují podrobné porovnání každé úrovně. V následující tabulce jsou popsány některé funkce podporované jednotlivými vrstvami:

| Popis funkce | Basic | Standard | Premium | Enterprise | Podnikový přehrávač |
| ------------------- | :-----: | :------: | :---: | :---: | :---: |
| [Smlouvy o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|✔|✔|✔|✔|
| Šifrování dat |✔|✔|✔|✔|✔|
| [Izolace sítě](cache-how-to-premium-vnet.md) |✔|✔|✔|✔|✔|
| [Škálování](cache-how-to-scale.md) |✔|✔|✔|-|-|
| [Cluster OSS](cache-how-to-premium-clustering.md) |-|-|✔|✔|✔|
| [Trvalost dat](cache-how-to-premium-persistence.md) |-|-|✔|-|-|
| [Zónová redundance](cache-how-to-zone-redundancy.md) |-|-|Preview|Preview|Preview|
| [Geografická replikace](cache-how-to-geo-replication.md) |-|-|✔|-|-|
| [Moduly](https://redis.io/modules) |-|-|-|✔|✔|
| [Import/export](cache-how-to-import-export-data.md) |-|-|✔|✔|✔|
| [Plánované aktualizace](cache-administration.md#schedule-updates) |✔|✔|✔|-|-|

### <a name="choosing-the-right-tier"></a>Výběr správné úrovně
Při výběru mezipaměti Azure pro úroveň Redis byste měli vzít v úvahu následující:

* **Paměť**: úrovně Basic a Standard nabízejí 250 MB – 53 GB; úroveň Premium 6 GB až 1,2 TB; úrovně Enterprise jsou 12 GB až 14 TB.  Pokud chcete vytvořit mezipaměť úrovně Premium větší než 120 GB, můžete použít clustering Redis OSS. Další informace najdete v tématu [ceny služby Azure cache pro Redis](https://azure.microsoft.com/pricing/details/cache/). Další informace najdete v tématu [Konfigurace clusteringu pro službu Azure cache v úrovni Premium pro Redis](cache-how-to-premium-clustering.md).
* **Výkon**: mezipaměti v úrovních Premium a Enterprise se nasazují na hardware, který má rychlejší procesory, což zajišťuje vyšší výkon v porovnání s úrovní Basic nebo Standard. Mezipaměti úrovně Premium mají vyšší propustnost a nižší latenci. Další informace najdete v tématu [o výkonu služby Azure cache pro Redis](cache-planning-faq.md#azure-cache-for-redis-performance).
* **Vyhrazená jádro pro server Redis**: všechny mezipaměti s výjimkou C0 spustí vyhrazené jádro virtuálních počítačů. Redis podle návrhu používá pro zpracování příkazů pouze jedno vlákno. Azure cache pro Redis využívá další jádra pro zpracování v/v. Větší množství jader zvyšuje výkon při propustnosti, i když nemusí způsobit Lineární škálování. Kromě toho větší velikosti virtuálních počítačů obvykle přináší větší omezení šířky pásma než menší. To vám pomůže vyhnout se sytosti sítě, což způsobí vypršení časového limitu aplikace.
* **Výkon sítě**: Pokud máte zatížení, které vyžaduje vysokou propustnost, úroveň Premium nebo Enterprise nabízí větší šířku pásma v porovnání se základní nebo standardní. Kromě toho v každé vrstvě mají větší velikost mezipaměti větší šířku pásma kvůli základnímu virtuálnímu počítači, který hostuje mezipaměť. Další informace najdete v tématu [o výkonu služby Azure cache pro Redis](cache-planning-faq.md#azure-cache-for-redis-performance).
* **Maximální počet připojení klientů**: úroveň Premium nabízí maximální počet klientů, kteří se mohou připojit k Redis s větším počtem připojení pro mezipaměti větší velikosti. Clustering nezvyšuje počet připojení dostupných pro clusterovou mezipaměť. Další informace najdete v tématu [ceny služby Azure cache pro Redis](https://azure.microsoft.com/pricing/details/cache/).
* **Vysoká dostupnost**: Azure cache for Redis nabízí několik možností [vysoké dostupnosti](cache-high-availability.md) . Zaručuje, že mezipaměť Standard, Premium nebo Enterprise je dostupná podle naší [smlouvy SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Smlouva SLA pokrývá jenom připojení ke koncovým bodům mezipaměti. Smlouva SLA nepokrývá ochranu před ztrátou dat. Pro zvýšení odolnosti proti ztrátě dat doporučujeme používat funkci Redis data Persistence na úrovních Premium a Enterprise.
* **Trvalost dat**: úrovně Premium a Enterprise umožňují uchovávat data z mezipaměti do účtu Azure Storage a spravovaného disku. Základní problémy s infrastrukturou můžou způsobit ztrátu dat. Pro zvýšení odolnosti proti ztrátě dat doporučujeme používat funkci trvalosti dat Redis v těchto úrovních. Azure cache pro Redis nabízí možnosti RDB i AOF (Preview). Podnikové úrovně mají při výchozím nastavení Trvalost dat povolené. Úroveň Premium najdete v tématu [Konfigurace trvalosti pro službu Azure cache Premium pro Redis](cache-how-to-premium-persistence.md).
* **Izolace sítě**: nasazení privátního propojení azure a Virtual Network (VNET) poskytují vylepšenou izolaci zabezpečení a provozu pro službu Azure cache pro Redis. Virtuální síť umožňuje další omezení přístupu prostřednictvím zásad řízení přístupu k síti. Další informace najdete v tématu věnovaném [službě Azure cache pro Redis s privátním odkazem na Azure](cache-private-link.md) a [Konfigurace podpory Virtual Network podporu pro Azure cache Premium pro Redis](cache-how-to-premium-vnet.md).
* **Rozšiřitelnost**: úrovně Enterprise podporují [RediSearch](https://docs.redislabs.com/latest/modules/redisearch/), [RedisBloom](https://docs.redislabs.com/latest/modules/redisbloom/) a [RedisTimeSeries](https://docs.redislabs.com/latest/modules/redistimeseries/). Tyto moduly přidávají nové datové typy a funkce do Redis.

Po vytvoření můžete škálovat mezipaměť z úrovně Basic až na Premium. Horizontální navýšení kapacity na nižší úroveň se momentálně nepodporuje. Podrobné pokyny k škálování najdete v tématu [Jak škálovat Azure cache pro Redis](cache-how-to-scale.md) a [Jak automatizovat operaci škálování](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="enterprise-and-enterprise-flash-tier-requirements"></a>Požadavky na úroveň Enterprise a Enterprise Flash

Podnikové úrovně spoléhají na Redis Enterprise, tedy na komerční variantu Redis z Redis Labs. Zákazníci obdrží licenci k tomuto softwaru a platíte za něj prostřednictvím nabídky Azure Marketplace. Azure cache pro Redis pomůže získání licence, takže je nebudete muset provádět samostatně. K nákupu v Azure Marketplace musíte mít následující požadavky:
* Předplatné Azure má platný platební nástroj. Kredity Azure nebo bezplatné předplatné MSDN se nepodporují.
* Jste vlastníkem nebo přispěvatelem předplatného.
* Vaše organizace umožňuje [nákupy Azure Marketplace](../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases).
* Pokud používáte privátní tržiště, musí obsahovat nabídku Redis Labs Enterprise.

## <a name="next-steps"></a>Další kroky
* [Vytvoření open source mezipaměti Redis Cache](quickstart-create-redis.md)
* [Vytvoření mezipaměti Redis Enterprise](quickstart-create-redis-enterprise.md)
* [Použití mezipaměti Azure pro Redis ve webové aplikaci v ASP.NET](cache-web-app-howto.md)
* [Použití mezipaměti Azure pro Redis v .NET Core](cache-dotnet-core-quickstart.md)
* [Použití mezipaměti Azure pro Redis v .NET Framework](cache-dotnet-how-to-use-azure-redis-cache.md)
* [Použití mezipaměti Azure pro Redis v Node.js](cache-nodejs-get-started.md)
* [Použití Azure cache pro Redis v jazyce Java](cache-java-get-started.md)
* [Použití mezipaměti Azure pro Redis v Pythonu](cache-python-get-started.md)