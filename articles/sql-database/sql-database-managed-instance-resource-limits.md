---
title: Omezení prostředků – spravovaná instance
description: Tento článek poskytuje přehled Azure SQL Databasech omezení prostředků pro spravované instance.
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
ms.openlocfilehash: 49a9355d0e5653ac453493a1808ab95136410d19
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680470"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Přehled Azure SQL Database omezení prostředků spravované instance

Tento článek poskytuje přehled technických charakteristik a omezení prostředků pro Azure SQL Database spravovanou instanci a poskytuje informace o tom, jak požádat o zvýšení těchto limitů.

> [!NOTE]
> Rozdíly v podporovaných funkcích a příkazech T-SQL najdete v tématu [rozdíly](sql-database-features.md) ve funkcích a [Podpora příkazů t-SQL](sql-database-managed-instance-transact-sql-information.md). Obecný rozdíl mezi úrovněmi služeb v izolovaných databázích a spravované instanci najdete v tématu [porovnání úrovně služby](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Charakteristiky generování hardwaru

Spravovaná instance má charakteristiky a omezení prostředků, které závisí na základní infrastruktuře a architektuře. Azure SQL Database spravované instance je možné nasadit na dvě generace hardwaru: COMPUTE GEN4 – a Gen5. Hardwarové generace mají různé charakteristiky, jak je popsáno v následující tabulce:

|   | **COMPUTE GEN4 –** | **Gen5** |
| --- | --- | --- |
| Hardware | Procesory Intel E5-2673 V3 (Haswell) 2,4 GHz, připojené SSD vCore = 1 PP (fyzický jádro) | Procesory Intel E5-2673 v4 (Broadwell) 2,3-GHz a Intel SP-8160 (Skylake), Fast NVMe SSD, vCore = 1 LP (Hyper-thread) |
| Počet virtuálních jader | 8, 16, 24 virtuální jádra | 4, 8, 16, 24, 32, 40, 64, 80 virtuální jádra |
| Maximální velikost paměti (poměr paměti/jádra) | 7 GB na vCore<br/>Přidejte další virtuální jádra, abyste získali více paměti. | 5,1 GB na vCore<br/>Přidejte další virtuální jádra, abyste získali více paměti. |
| Maximální paměť OLTP v paměti | Limit instance: 1 – 1,5 GB na vCore| Limit instance: 0,8 – 1,65 GB na vCore |
| Maximální rezervované úložiště instancí |  Pro obecné účely: 8 TB<br/>Pro důležité obchodní informace: 1 TB | Pro obecné účely: 8 TB<br/> V závislosti na počtu jader Pro důležité obchodní informace 1 TB, 2 TB nebo 4 TB. |

> [!IMPORTANT]
> - COMPUTE GEN4 – hardware je vyladěný a již není pro nová nasazení k dispozici. Všechny nové spravované instance musí být nasazené na Gen5 hardwaru.
> - Zvažte [přesunutí spravovaných instancí na hardware Gen 5](sql-database-service-tiers-vcore.md) a vyzkoušejte širší škálu Vcore a škálovatelnosti úložiště, akcelerované síťové služby, nejlepší vstupně-výstupní výkon a minimální latenci.

### <a name="in-memory-oltp-available-space"></a>OLTP volné místo v paměti 

Velikost OLTP prostoru v paměti v [pro důležité obchodní informace](sql-database-service-tier-business-critical.md) úrovni služby závisí na počtu virtuální jádra a generování hardwaru. V následující tabulce jsou uvedena omezení paměti, která lze použít pro objekty OLTP v paměti.

| OLTP místo v paměti  | **Gen5** | **COMPUTE GEN4 –** |
| --- | --- | --- |
| 4 virtuální jádra  | 3,14 GB | |   
| 8 virtuálních jader  | 6,28 GB | 8 GB |
| 16 virtuální jádra | 15,77 GB | 20 GB |
| 24 virtuální jádra | 25,25 GB | 36 GB |
| 32 virtuální jádra | 37,94 GB | |
| 40 virtuální jádra | 52,23 GB | |
| 64 virtuální jádra | 99,9 GB    | |
| 80 virtuální jádra | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Vlastnosti vrstvy služeb

Managed instance má dvě úrovně služeb: [pro obecné účely](sql-database-service-tier-general-purpose.md) a [pro důležité obchodní informace](sql-database-service-tier-business-critical.md). Tyto úrovně poskytují [různé možnosti](sql-database-service-tiers-general-purpose-business-critical.md), jak je popsáno v následující tabulce.

> [!Important]
> Pro důležité obchodní informace úroveň služby poskytuje další integrovanou kopii instance (sekundární repliky), která se dá použít pro úlohu určenou jen pro čtení. Pokud můžete oddělit dotazy pro čtení a zápis a dotazy jen pro čtení a analýzu nebo vytváření sestav, dostanete za stejnou cenu dvojnásobnou virtuální jádra a paměť. Sekundární replika může u primární instance prodlevu pár sekund provést, aby bylo možné přesměrovat úlohy vytváření sestav a analýzy, které nepotřebují přesný aktuální stav dat. V následující tabulce jsou **dotazy jen pro čtení** . Jedná se o dotazy, které se spouštějí u sekundární repliky.

| **Příznak** | **Pro obecné účely** | **Pro důležité obchodní informace** |
| --- | --- | --- |
| Počet virtuálních jader\* | COMPUTE GEN4 –: 8, 16, 24<br/>Gen5:4, 8, 16, 24, 32, 40, 64, 80 | COMPUTE GEN4 –: 8, 16, 24 <br/> Gen5:4, 8, 16, 24, 32, 40, 64, 80 <br/>\*Stejný počet virtuální jádra je vyhrazen pro dotazy jen pro čtení. |
| Maximální velikost paměti | COMPUTE GEN4 –: 56 GB-168 GB (7GB/vCore)<br/>Gen5:20,4 GB-408 GB (5.1 GB/vCore)<br/>Přidejte další virtuální jádra, abyste získali více paměti. | COMPUTE GEN4 –: 56 GB-168 GB (7GB/vCore)<br/>Gen5:20,4 GB-408 GB (5.1 GB/vCore) pro dotazy pro čtení i zápis<br/>+ dalších 20,4 GB až 408 GB (5.1 GB/vCore) pro dotazy jen pro čtení.<br/>Přidejte další virtuální jádra, abyste získali více paměti. |
| Maximální velikost úložiště instancí (rezervované) | – 2 TB pro 4 virtuální jádra (jenom Gen5)<br/>– 8 TB pro jiné velikosti | COMPUTE GEN4 –: 1 TB <br/> Gen5 <br/>-1 TB pro 4, 8, 16 virtuální jádra<br/>– 2 TB pro 24 virtuální jádra<br/>-4 TB pro 32, 40, 64, 80 virtuální jádra |
| Maximální velikost databáze | Až v aktuálně dostupné velikosti instance (max. 2 TB až 8 TB v závislosti na počtu virtuální jádra). | Až v aktuálně dostupné velikosti instance (max. 1 TB-4 TB v závislosti na počtu virtuální jádra). |
| Maximální velikost databáze tempDB | Omezeno na 24 GB/vCore (96 – 1 920 GB) a aktuálně dostupná velikost úložiště instance.<br/>Přidejte další virtuální jádra, abyste získali více místa v databázi TempDB.<br/> Velikost souboru protokolu je omezená na 120 GB.| Až do aktuálně dostupné velikosti úložiště instance. |
| Maximální počet databází na instanci | 100, pokud nedošlo k dosažení limitu velikosti úložiště instance. | 100, pokud nedošlo k dosažení limitu velikosti úložiště instance. |
| Maximální počet souborů databáze na instanci | Až 280, pokud nedošlo k dosažení limitu velikosti úložiště instance nebo [místa přidělení úložiště na disku Azure Premium](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files) . | 32 767 souborů na databázi, pokud nebylo dosaženo limitu velikosti úložiště instance. |
| Maximální velikost datového souboru | Omezeno na aktuálně dostupnou velikost úložiště instance (max. 2 TB-8 TB) a [místo přidělení diskového úložiště Azure Premium](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files). | Omezeno na velikost úložiště aktuálně dostupné instance (až do velikosti 1 TB až 4 TB). |
| Maximální velikost souboru protokolu | Omezeno na 2 TB a aktuálně dostupnou velikost úložiště instance. | Omezeno na 2 TB a aktuálně dostupnou velikost úložiště instance. |
| Data/protokol IOPS (přibližná) | Až 30-40 K IOPS na instanci *, 500-7500 na jeden soubor<br/>\*[Zvětšením velikosti souboru získat více IOPS](#file-io-characteristics-in-general-purpose-tier)| 10 K-200 K (2500 IOPS/vCore)<br/>Přidáním dalších virtuální jádra získáte lepší výkon v/v. |
| Limit propustnosti zápisu protokolu (na instanci) | 3 MB/s na vCore<br/>Max. 22 MB/s | 4 MB/s na vCore<br/>Maximální 48 MB/s |
| Propustnost dat (přibližná) | 100 – 250 MB/s na jeden soubor<br/>\*[Zvětšením velikosti souboru získáte lepší vstupně-výstupní operace.](#file-io-characteristics-in-general-purpose-tier) | Neomezeno. |
| Latence v/v úložiště (přibližná) | 5-10 MS | 1-2 MS |
| OLTP v paměti | Nepodporuje se | K dispozici, [velikost závisí na počtu Vcore](#in-memory-oltp-available-space) |
| Maximální počet relací | 30000 | 30000 |
| [Repliky jen pro čtení](sql-database-read-scale-out.md) | 0 | 1 (zahrnuto do ceny) |
| Izolace výpočtů | Gen5<br/>– podporováno pro 80 virtuální jádra<br/>-není podporováno pro jiné velikosti<br/><br/>COMPUTE GEN4 – se nepodporuje, protože je zastaralá.|Gen5<br/>– podporováno pro 60, 64, 80 virtuální jádra<br/>-není podporováno pro jiné velikosti<br/><br/>COMPUTE GEN4 – se nepodporuje, protože je zastaralá.|

> [!NOTE]
> - **Aktuálně dostupná velikost úložiště instance** je rozdíl mezi rezervovanou velikostí instance a využitým prostorem úložiště.
> - Velikost dat a souborů protokolu v uživatelských i systémových databázích jsou zahrnuté do velikosti úložiště instance, která je porovnávána s limitem maximální velikosti úložiště. Pomocí zobrazení <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">Sys. master_files</a> systému určíte celkové využité místo databáze. Protokoly chyb nejsou trvale uložené a nejsou zahrnuté do velikosti. Zálohy nejsou zahrnuté do velikosti úložiště.
> - Propustnost a IOPS v Pro obecné účely úrovni závisí taky na [velikosti souboru](#file-io-characteristics-in-general-purpose-tier) , která není explicitně omezená pomocí spravované instance.
> - Pomocí skupin s automatickým převzetím služeb při selhání můžete vytvořit další čitelnou repliku v jiné oblasti Azure.
> - Maximální instance IOPS závisí na rozložení souborů a distribuci úlohy. Pokud například vytváříte soubory o velikosti 7 až 1 TB s maximálním počtem 5K IOPS, každý a 7 malých souborů (menší než 128 GB) s 500 IOPS za sekundu, můžete získat 38500 IOPS na instanci (7x5000 + 7x500), pokud vaše úloha může použít všechny soubory. Všimněte si, že pro automatické zálohování se používá také určité množství IOPS.

> [!NOTE]
> Další informace o [omezeních prostředků ve fondech spravované instance v tomto článku](sql-database-instance-pools.md#instance-pools-resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Vlastnosti v/v souboru na úrovni Pro obecné účely

Na úrovni služby Pro obecné účely každý databázový soubor získává vyhrazené IOPS a propustnost, které závisí na velikosti souboru. Větší počet souborů je více vstupně-výstupních operací a propustnosti. Vlastnosti v/v souborů databáze jsou uvedené v následující tabulce:

| Velikost souboru | >= 0 a <= 128 GiB | >128 a <= 256 GiB | >256 a <= 512 GiB | >0,5 a <= 1 TiB    | >1 a <= 2 TiB    | >2 a <= 4 TiB | >4 a <= 8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Počet IOPS na soubor       | 500   | 1100 | 2300              | 5000              | 7 500              | 7 500              | 12 500   |
| Propustnost na jeden soubor | soubory MiB 100/s | soubory MiB 125/s | soubory MiB 150/s | soubory MiB 200/s | 250 MiB/s | 250 MiB/s | soubory MiB 480/s | 

Pokud si všimnete vysoké latence v/v některých databázových souborů nebo zjistíte, že počet IOPS/propustnost dosáhne limitu, můžete zvýšit výkon [zvýšením velikosti souboru](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Existují také omezení na úrovni instance, jako je maximální propustnost zápisu protokolu 22 MB/s, takže možná nebudete mít přístup k souboru v celém souboru protokolu, protože se blíží limitu propustnosti instancí.

## <a name="supported-regions"></a>Podporované oblasti

Spravované instance lze vytvořit pouze v [podporovaných oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Pokud chcete vytvořit spravovanou instanci v oblasti, která není aktuálně podporovaná, můžete [Odeslat žádost o podporu prostřednictvím Azure Portal](quota-increase-request.md).

## <a name="supported-subscription-types"></a>Podporované typy předplatného

Spravovaná instance aktuálně podporuje nasazení pouze u následujících typů předplatných:

- [Smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Poskytovatel cloudových služeb (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Průběžné platby dle aktuálního využití pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Předplatné s měsíčním kreditem Azure pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Omezení regionálních prostředků

Podporované typy předplatného můžou obsahovat omezený počet prostředků na oblast. Spravovaná instance má dvě výchozí omezení pro každou oblast Azure (to se dá zvýšit na vyžádání vytvořením speciální [žádosti o podporu v Azure Portal](quota-increase-request.md) v závislosti na typu typu předplatného:

- **Limit podsítě**: maximální počet podsítí, ve kterých se spravované instance nasazují v jedné oblasti.
- **limit jednotky Vcore**: maximální počet jednotek Vcore, které se dají nasadit napříč všemi instancemi v jedné oblasti. Jedna vCorea GP používá jednu vCore jednotku a jedna BC vCore přijímá 4 jednotky vCore. Celkový počet instancí není omezený, pokud se nachází v rámci limitu vCore jednotek.

> [!Note]
> Tato omezení představují výchozí nastavení a nejedná se o technická omezení. Omezení se dají zvýšit na vyžádání vytvořením speciální [žádosti o podporu v Azure Portal](quota-increase-request.md) , pokud v aktuální oblasti potřebujete víc spravovaných instancí. Jako alternativu můžete vytvořit nové spravované instance v jiné oblasti Azure bez nutnosti odesílat žádosti o podporu.

Následující tabulka ukazuje **výchozí regionální omezení** pro podporované typy předplatného (pomocí žádosti o podporu popsanou níže můžete rozšířit výchozí omezení):

|Typ předplatného| Maximální počet podsítí spravované instance | Maximální počet jednotek vCore * |
| :---| :--- | :--- |
|Průběžné platby|3|320|
|CSP |8 (15 v některých oblastech * *)|960 (1440 v některých oblastech * *)|
|Průběžné platby dle aktuálního využití pro vývoj/testování|3|320|
|Enterprise pro vývoj/testování|3|320|
|EA|8 (15 v některých oblastech * *)|960 (1440 v některých oblastech * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional a MSDN Platforms|2|32|

\*Při plánování nasazení Vezměte v úvahu, že úroveň služby Pro důležité obchodní informace (BC) vyžaduje čtyři (4) větší kapacitu vCore než úroveň služby Pro obecné účely (GP). Příklad: 1 GP vCore = 1 vCore jednotka a 1 BC vCore = 4 jednotky vCore. Abyste zjednodušili analýzu spotřeby proti výchozím omezením, Shrňte jednotky vCore ve všech podsítích v oblasti, ve které se nasazují spravované instance, a porovnejte výsledky s omezeními jednotky instance pro typ předplatného. **Maximální počet Vcore jednotek** se vztahuje na každé předplatné v oblasti. Pro jednotlivé podsítě neplatí žádné omezení s tím rozdílem, že součet všech virtuální jádra nasazených napříč více podsítěmi musí být nižší nebo roven **maximálnímu počtu Vcore jednotek**.

\*\*Větší podsíť a omezení vCore jsou k dispozici v následujících oblastech: Austrálie – východ, Východní USA, Východní USA 2, Severní Evropa, Střed USA – jih, jihovýchodní Asie, Velká Británie – jih, Západní Evropa, Západní USA 2.

## <a name="request-a-quota-increase-for-sql-managed-instance"></a>Požádat o zvýšení kvóty pro spravovanou instanci SQL

Pokud ve svých současných oblastech potřebujete víc spravovaných instancí, pošlete žádost o podporu, která kvótu rozšiřuje, pomocí Azure Portal. Další informace najdete v tématu [zvýšení kvóty žádostí o Azure SQL Database](quota-increase-request.md).

## <a name="next-steps"></a>Další kroky

- Další informace o spravované instanci najdete v tématu [co je spravovaná instance?](sql-database-managed-instance.md).
- Informace o cenách najdete v tématu [SQL Database ceny za Managed instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Informace o tom, jak vytvořit první spravovanou instanci, najdete v [příručce pro rychlý Start](sql-database-managed-instance-get-started.md).
