---
title: Omezení prostředků – spravovaná instance
description: Tento článek obsahuje přehled omezení prostředků Azure SQL Database pro spravované instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 02/25/2020
ms.openlocfilehash: b2871ec87e4d7f337c26b3ff3de83c1c3c88aea2
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365390"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Přehled Omezení prostředků spravovaných instancí Azure SQL Database

Tento článek obsahuje přehled technických charakteristik a omezení prostředků pro spravovanou instanci Azure SQL Database a poskytuje informace o tom, jak požádat o zvýšení těchto limitů.

> [!NOTE]
> Rozdíly v podporovaných funkcích a příkazech T-SQL naleznete v [tématu Rozdíly funkcí](sql-database-features.md) a [podpora příkazů T-SQL](sql-database-managed-instance-transact-sql-information.md). Obecné rozdíly mezi úrovněmi služeb v jedné databázi a spravované instanci najdete v tématu [Porovnání vrstev služby](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Vlastnosti generování hardwaru

Spravovaná instance má charakteristiky a omezení prostředků, které závisí na základní infrastruktuře a architektuře. Spravovaná instance Azure SQL Database se dá nasadit na dvě generace hardwaru: Gen4 a Gen5. Generace hardwaru mají různé vlastnosti, jak je popsáno v následující tabulce:

|   | **Gen4 (Gen4)** | **Gen5 (Gen5)** |
| --- | --- | --- |
| Hardware | Procesory Intel E5-2673 v3 (Haswell) 2,4 GHz, připojený virtuální jádrsssd = 1 PP (fyzické jádro) | Procesory Intel E5-2673 v4 (Broadwell) 2,3 GHz a Intel SP-8160 (Skylake), rychlé NVMe SSD, vCore=1 LP (hyper-thread) |
| Počet virtuálních jader | 8, 16, 24 virtuálních jader | 4, 8, 16, 24, 32, 40, 64, 80 virtuálních jader |
| Maximální paměť (poměr paměti k jádru) | 7 GB na virtuální jádro<br/>Přidejte další virtuální jádra, abyste získali více paměti. | 5,1 GB na virtuální jádro<br/>Přidejte další virtuální jádra, abyste získali více paměti. |
| Maximální paměť OLTP v paměti | Limit instance: 1-1.5 GB na virtuální jádro| Limit instance: 0,8 - 1,65 GB na virtuální jádro |
| Maximální rezervované úložiště instance |  Všeobecné použití: 8 TB<br/>Kritické pro podnikání: 1 TB | Všeobecné použití: 8 TB<br/> Business Critical 1 TB, 2 TB nebo 4 TB v závislosti na počtu jader |

> [!IMPORTANT]
> - Gen4 hardware je postupně ukončován a již není k dispozici pro nová nasazení. Všechny nové spravované instance musí být nasazeny na hardwaru Gen5.
> - Zvažte [přesunutí spravovaných instancí do](sql-database-service-tiers-vcore.md) hardwaru Gen 5, abyste měli k dispozici širší škálu virtuálních jader a úložišť, zrychlené sítě, nejlepší výkon vstupně-výstupních řízení a minimální latenci.

### <a name="in-memory-oltp-available-space"></a>Dostupné místo OLTP v paměti 

Velikost místa OLTP v paměti v úrovni služby [Business Critical](sql-database-service-tier-business-critical.md) závisí na počtu virtuálních jader a generování hardwaru. V následující tabulce jsou uvedeny limity paměti, které lze použít pro objekty OLTP v paměti.

| Místo OLTP v paměti  | **Gen5 (Gen5)** | **Gen4 (Gen4)** |
| --- | --- | --- |
| 4 virtuální jádra  | 3,14 GB | |   
| 8 virtuálních jader  | 6,28 GB | 8 GB |
| 16 virtuálních jader | 15,77 GB | 20 GB |
| 24 virtuálních jader | 25,25 GB | 36 GB |
| 32 virtuálních jader | 37,94 GB | |
| 40 virtuálních jader | 52,23 GB | |
| 64 virtuálních jader | 99,9 GB    | |
| 80 virtuálních jader | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Charakteristiky úrovně služby

Spravovaná instance má dvě úrovně služeb: [Obecné účely](sql-database-service-tier-general-purpose.md) a Důležité [pro podnikání](sql-database-service-tier-business-critical.md). Tyto úrovně poskytují [různé možnosti](sql-database-service-tiers-general-purpose-business-critical.md), jak je popsáno v následující tabulce.

> [!Important]
> Podnikkritická úroveň služeb poskytuje další předdefinovanou kopii instance (sekundární replika), kterou lze použít pro úlohy jen pro čtení. Pokud můžete oddělit dotazy pro čtení a zápis a dotazy jen pro čtení/analytics/reporting, získáváte dvakrát virtuální jádra a paměť za stejnou cenu. Sekundární replika může zaostávat několik sekund za primární instance, takže je navržen tak, aby převést načtení vykazování nebo analytické úlohy, které nepotřebují přesný aktuální stav dat. V následující tabulce jsou **dotazy jen pro čtení** dotazy, které jsou spouštěny na sekundární replice.

| **Funkce** | **Obecný účel** | **Kritické pro podnikání** |
| --- | --- | --- |
| Počet virtuálních jader\* | Gen4: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*Stejný počet virtuálních jader je vyhrazen pro dotazy jen pro čtení. |
| Maximální paměť | Gen4: 56 GB - 168 GB (7 GB/virtuální jádro)<br/>Gen5: 20,4 GB - 408 GB (5,1 GB/virtuální jádro)<br/>Přidejte další virtuální jádra, abyste získali více paměti. | Gen4: 56 GB - 168 GB (7 GB/virtuální jádro)<br/>Gen5: 20,4 GB - 408 GB (5,1 GB/virtuální jádro) pro dotazy pro čtení a zápis<br/>+ dalších 20,4 GB - 408 GB (5,1 GB/virtuálního jádra) pro dotazy jen pro čtení.<br/>Přidejte další virtuální jádra, abyste získali více paměti. |
| Maximální velikost úložiště instance (rezervovaná) | - 2 TB pro 4 virtuální jádra (pouze Gen5)<br/>- 8 TB pro ostatní velikosti | Gen4: 1 TB <br/> Gen5: <br/>- 1 TB pro 4, 8, 16 virtuálních jader<br/>- 2 TB pro 24 virtuálních jader<br/>- 4 TB pro 32, 40, 64, 80 virtuálních jader |
| Maximální velikost databáze | Až do aktuálně dostupné velikosti instance (max 2 TB - 8 TB v závislosti na počtu virtuálních jader). | Až do aktuálně dostupné velikosti instance (max 1 TB - 4 TB v závislosti na počtu virtuálních jader). |
| Maximální velikost tempDB | Omezeno na 24 GB/vCore (96 - 1 920 GB) a aktuálně dostupnou velikost úložiště instancí.<br/>Přidejte další virtuální jádra, abyste získali více prostoru TempDB.<br/> Velikost souboru protokolu je omezena na 120 GB.| Až do aktuálně dostupné velikosti úložiště instancí. |
| Maximální počet databází na instanci | 100, pokud nebylo dosaženo limitu velikosti úložiště instance. | 100, pokud nebylo dosaženo limitu velikosti úložiště instance. |
| Maximální počet databázových souborů na instanci | Až 280, pokud nebylo dosaženo velikosti úložiště instance nebo [limitu přidělení úložiště disku Azure Premium.](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files) | 32 767 souborů na databázi, pokud nebylo dosaženo limitu velikosti úložiště instance. |
| Maximální velikost datového souboru | Omezeno na aktuálně dostupnou velikost úložiště instancí (max. 2 TB - 8 TB) a [místo přidělení disku Azure Premium](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files). | Omezeno na aktuálně dostupnou velikost úložiště instancí (až 1 TB - 4 TB). |
| Maximální velikost souboru protokolu | Omezeno na 2 TB a aktuálně dostupnou velikost úložiště instancí. | Omezeno na 2 TB a aktuálně dostupnou velikost úložiště instancí. |
| Data/Log IOPS (přibližné) | Až 30-40 K IOPS na instanci*, 500 - 7500 na soubor<br/>\*[Zvětšení velikosti souboru pro získání více viopů](#file-io-characteristics-in-general-purpose-tier)| 10 K - 200 K (2500 IOPS/vCore)<br/>Přidejte další virtuální jádra, abyste získali lepší výkon vi. |
| Omezení propustnost zápisu protokolu (na instanci) | 3 MB/s na virtuální jádro<br/>Maximální velikost 22 MB/s | 4 MB/s na virtuální jádro<br/>Maximální velikost 48 MB/s |
| Propustnost dat (přibližná) | 100 - 250 MB/s na soubor<br/>\*[Zvětšení velikosti souboru pro lepší výkon vi](#file-io-characteristics-in-general-purpose-tier) | Není omezen. |
| Latence vi videa úložiště (přibližná) | 5-10 ms | 1-2 ms |
| OLTP v paměti | Nepodporuje se | K dispozici, [velikost závisí na počtu virtuálních jader](#in-memory-oltp-available-space) |
| Maximální počet relací | 30000 | 30000 |
| [Repliky jen pro čtení](sql-database-read-scale-out.md) | 0 | 1 (v ceně) |

> [!NOTE]
> - **Aktuálně dostupná velikost úložiště instance** je rozdíl mezi rezervovanou velikostí instance a použitým úložným prostorem.
> - Velikost dat i souborů protokolu v uživatelských a systémových databázích jsou zahrnuty ve velikosti úložiště instance, která je porovnána s maximálním limitem velikosti úložiště. Pomocí systémového zobrazení <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> určete celkový využitý prostor v databázích. Protokoly chyb nejsou trvalé a nejsou zahrnuty ve velikosti. Zálohy nejsou zahrnuty ve velikosti úložiště.
> - Propustnost a vstupně-up na úrovni obecného použití také závisí na [velikosti souboru,](#file-io-characteristics-in-general-purpose-tier) který není explicitně omezen spravovanou instancí.
> - Můžete vytvořit další čitelnou repliku v různých oblastech Azure pomocí skupin s podporou převzetí služeb při selhání.
> - Maximální počet vstupně-diagramů instance závisí na rozložení souboru a distribuci úlohy. Pokud například vytvoříte soubory o velikosti 7 x 1 TB s maximálním 5K IOPS a 7 malých souborů (menší než 128 GB) s 500 IOPS, můžete získat 38500 IOPS na instanci (7x5000 + 7x500), pokud vaše úloha může používat všechny soubory. Všimněte si, že některé množství viops se také používá pro automatické zálohování.

> [!NOTE]
> Další informace o [omezeních prostředků ve fondech spravovaných instancí naleznete v tomto článku](sql-database-instance-pools.md#instance-pools-resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Vlastnosti vstupně-veřejných otomek souboru ve vrstvě obecného použití

V úrovni služby pro obecné účely každý databázový soubor získává vyhrazené vstupně-operace a propustnost, která závisí na velikosti souboru. Větší soubory jsou stále více viops a propustnost. Charakteristiky vi vdatabázových souborů jsou uvedeny v následující tabulce:

| Velikost souboru | >=0 a <=128 GiB | >128 a <=256 GiB | >256 a <= 512 GiB | >0,5 a <=1 TiB    | >1 a <=2 TiB    | >2 a <=4 TiB | >4 a <=8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Viops na soubor       | 500   | 1100 | 2300              | 5000              | 7 500              | 7 500              | 12 500   |
| Propustnost na soubor | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Pokud zjistíte vysokou latenci vstupně-výstupních operací u některého databázového souboru nebo zjistíte, že vstupně-výstupní operace/propustnost dosahuje limitu, můžete zvýšit výkon [zvětšením velikosti souboru](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Existují také omezení na úrovni instance, jako je maximální propustnost zápisu protokolu 22 MB/s, takže pravděpodobně nebudete moci dosáhnout souboru v celém souboru protokolu, protože dosahujete limitu propustnost instance.

## <a name="supported-regions"></a>Podporované oblasti

Spravované instance lze vytvořit pouze v [podporovaných oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Chcete-li vytvořit spravovanou instanci v oblasti, která momentálně není podporovaná, můžete [odeslat žádost o podporu prostřednictvím portálu Azure](quota-increase-request.md).

## <a name="supported-subscription-types"></a>Podporované typy předplatného

Spravovaná instance aktuálně podporuje nasazení pouze u následujících typů předplatných:

- [Smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Poskytovatel cloudových služeb (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Průběžné platby dle aktuálního využití pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Předplatná s měsíčním kreditem Azure pro předplatitele Visual Studia](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Omezení regionálních zdrojů

Podporované typy předplatného mohou obsahovat omezený počet prostředků na oblast. Spravovaná instance má dvě výchozí omezení pro oblast Azure (které lze zvýšit na vyžádání vytvořením speciální [žádosti o podporu na webu Azure Portal v](quota-increase-request.md) závislosti na typu typu předplatného:

- **Limit podsítě**: Maximální počet podsítí, kde jsou spravované instance nasazeny v jedné oblasti.
- **Limit jednotky virtuálních jader**: Maximální počet jednotek virtuálních jader, které lze nasadit ve všech instancích v jedné oblasti. Jeden virtuální jádro GP používá jednu virtuální jádro a jeden virtuální jádr BC přebírá 4 virtuální jádra. Celkový počet instancí není omezen, pokud je v rámci limitu jednotky virtuálních jader.

> [!Note]
> Tato omezení jsou výchozí nastavení a nikoli technická omezení. Limity lze zvýšit na vyžádání vytvořením speciální [žádosti o podporu na webu Azure Portal,](quota-increase-request.md) pokud potřebujete více spravovaných instancí v aktuální oblasti. Jako alternativu můžete vytvořit nové spravované instance v jiné oblasti Azure bez odesílání žádostí o podporu.

V následující tabulce jsou uvedena **výchozí místní omezení** pro podporované typy předplatného (výchozí limity lze rozšířit pomocí žádosti o podporu popsané níže):

|Typ předplatného| Maximální počet podsítí spravovaných instancí | Maximální počet virtuálních jader* |
| :---| :--- | :--- |
|Pay-as-you-go|3|320|
|CSP |8 (15 v některých regionech**)|960 (1440 v některých regionech**)|
|Vývoj/test s průběžným platbou|3|320|
|Enterprise pro vývoj/testování|3|320|
|Ea|8 (15 v některých regionech**)|960 (1440 v některých regionech**)|
|Visual Studio Enterprise|2 |64|
|Platformy Visual Studio Professional a MSDN|2|32|

\*Při plánování nasazení vezměte v úvahu, že úroveň služeb Business Critical (BC) vyžaduje čtyři (4) více kapacity virtuálních jader než úroveň služeb pro obecné účely (GP). Například: 1 GP vCore = 1 virtuální jádro a 1 BC vCore = 4 jednotky virtuálních jader. Chcete-li zjednodušit analýzu spotřeby oproti výchozím limitům, shrňte jednotky virtuálních jader ve všech podsítích v oblasti, kde se nasazují spravované instance, a porovnejte výsledky s limity jednotek instance pro typ předplatného. **Maximální počet jednotek virtuálních jader** se vztahuje na každé předplatné v oblasti. Neexistuje žádné omezení pro jednotlivé podsítě s tím rozdílem, že součet všech virtuálních jader nasazených ve více podsítích musí být nižší nebo roven **maximálnímu počtu virtuálních jader**.

\*\*Větší limity podsítě a virtuálních jader jsou k dispozici v následujících oblastech: Austrálie – východ, východ USA, východní USA 2, severní Evropa, jižní střed USA, jihovýchodní Asie, Velká Británie – jih, Západní Evropa, Západní USA 2.

## <a name="request-a-quota-increase-for-sql-managed-instance"></a>Požádat o zvýšení kvóty pro spravovanou instanci SQL

Pokud potřebujete více spravovaných instancí ve vašich aktuálních oblastech, odešlete žádost o podporu k rozšíření kvóty pomocí portálu Azure. Další informace naleznete v [tématu Požadavek na zvýšení kvóty pro Azure SQL Database](quota-increase-request.md).

## <a name="next-steps"></a>Další kroky

- Další informace o spravované instanci najdete [v tématu Co je spravovaná instance?](sql-database-managed-instance.md).
- Informace o cenách naleznete v [tématu SQL Database managed instance pricing](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Informace o tom, jak vytvořit první spravovanou instanci, naleznete [v příručce k rychlému startu](sql-database-managed-instance-get-started.md).
