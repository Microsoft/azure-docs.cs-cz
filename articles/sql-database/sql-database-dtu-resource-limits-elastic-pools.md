---
title: Prostředků na základě DTU databáze SQL Azure omezuje elastické fondy | Microsoft Docs
description: Tato stránka popisuje některé běžné limitů prostředků na základě DTU pro elastické fondy ve službě Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: adf0cd8fb8511b01d2c8e773444b0af128102ad1
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309866"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-based-purchasing-model"></a>Omezení prostředků pro elastické fondy pomocí modelu na základě DTU nákupu 

Tento článek obsahuje podrobné prostředků limity pro elastické fondy Azure SQL Database a ve fondu databáze pomocí modelu na základě DTU nákupu. 

Na základě DTU nákupní model prostředků limity pro izolované databáze, najdete v části [limitů prostředků na základě DTU - izolovaných databází](sql-database-vcore-resource-limits-elastic-pools.md). Omezení zdrojů na základě vCore, najdete v části [limitů prostředků na základě vCore - izolovaných databází](sql-database-vcore-resource-limits-single-databases.md) a [limitů prostředků na základě vCore - elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Elastický fond: velikosti úložiště a úrovně výkonu

Pro databáze SQL elastické fondy následující tabulky popisují dostupné prostředky na úrovni jednotlivých služeb a výkonu. Můžete nastavit úroveň služby, úroveň výkonu a velikost úložiště pomocí [portál Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [prostředí PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [rozhraní příkazového řádku Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases), nebo [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!NOTE]
> Omezení prostředků jednotlivých databází v elastické fondy jsou obvykle stejné jako u izolovaných databází mimo fondů na základě Dtu a vrstvy služeb. Maximální počet souběžných pracovních procesů pro databázi S2 je například 120 pracovních procesů. Ano maximální počet souběžných pracovních procesů pro databázi v standardní fond je také 120 pracovníci Pokud 50 Dtu (což odpovídá S2) je maximální počet jednotek DTU na databázi ve fondu.

### <a name="basic-elastic-pool-limits"></a>Základní limity elastického fondu

| Počet eDTU na fond | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Zahrnuté úložiště na jeden fond (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximální počet možností úložiště na jeden fond (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximální počet OLTP v paměti úložiště na jeden fond (GB) | neuvedeno | neuvedeno | neuvedeno | neuvedeno | neuvedeno | neuvedeno | neuvedeno | neuvedeno |
| Maximální počet databází pro každý fond | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Maximální počet souběžných pracovních procesů (požadavků) na fond | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Maximální počet souběžných relací na fond | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Možnosti min Edtu na databázi | 0,5 | 0,5 | 0,5 | 0,5 | 0,5 | 0,5 | 0,5 | 0,5 |
| Možnosti Max Edtu na databázi | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Maximální velikost úložiště na databázi (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 
||||||||

### <a name="standard-elastic-pool-limits"></a>Standardní limity elastického fondu

| Počet eDTU na fond | **50** | **100** | **200** | **300** | **400** | **800**| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Zahrnuté úložiště na jeden fond (GB) | 50 | 100 | 200 | 300 | 400 | 800 | 
| Maximální počet možností úložiště na jeden fond (GB) | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 | 
| Maximální počet OLTP v paměti úložiště na jeden fond (GB) | neuvedeno | neuvedeno | neuvedeno | neuvedeno | neuvedeno | neuvedeno | 
| Maximální počet databází pro každý fond | 100 | 200 | 500 | 500 | 500 | 500 | 
| Maximální počet souběžných pracovních procesů (požadavků) na fond | 100 | 200 | 400 | 600 | 800 | 1600 |
| Maximální počet souběžných relací na fond | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Možnosti min Edtu na databázi | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Možnosti Max Edtu na databázi | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Maximální velikost úložiště na databázi (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limity elastického fondu úrovně Standard (pokračování) 

| Počet eDTU na fond | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Zahrnuté úložiště na jeden fond (GB) | 1200 | 1600 | 2000 | 2500 | 3000 | 
| Maximální počet možností úložiště na jeden fond (GB) | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Maximální počet OLTP v paměti úložiště na jeden fond (GB) | neuvedeno | neuvedeno | neuvedeno | neuvedeno | neuvedeno | 
| Maximální počet databází pro každý fond | 500 | 500 | 500 | 500 | 500 | 
| Maximální počet souběžných pracovních procesů (požadavků) na fond | 2400 | 3200 | 4000 | 5000 | 6000 |
| Maximální počet souběžných relací na fond | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Možnosti min Edtu na databázi | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000, 2 500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000, 2 500, 3 000 |
| Možnosti Max Edtu na databázi | 10, 20, 50, 100, 200, 300, 400, 800, 1 200 | 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600 | 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000 | 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000, 2 500 | 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000, 2 500, 3 000 | 
| Maximální počet možností úložiště na databázi (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Prémiové limity elastického fondu

| Počet eDTU na fond | **125** | **250** | **500** | **1000** | **1500**| 
|:---|---:|---:|---:| ---: | ---: | 
| Zahrnuté úložiště na jeden fond (GB) | 250 | 500 | 750 | 1024 | 1536 | 
| Maximální počet možností úložiště na jeden fond (GB) | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Maximální počet OLTP v paměti úložiště na jeden fond (GB) | 1 | 2 | 4 | 10 | 12 | 
| Maximální počet databází pro každý fond | 50 | 100 | 100 | 100 | 100 | 
| Maximální počet souběžných pracovních procesů na fond (požadavky) | 200 | 400 | 800 | 1600 | 2400 | 
| Maximální počet souběžných relací na fond | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Minimální počet eDTU na databázi | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1 000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 | 
| Maximální počet eDTU na databázi | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1 000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Maximální velikost úložiště na databázi (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limity elastického fondu úrovně Premium (pokračování) 

| Počet eDTU na fond | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: | 
| Zahrnuté úložiště na jeden fond (GB) | 2 048 | 2560 | 3072 | 3548 | 4 096 |
| Maximální počet možností úložiště na jeden fond (GB) | 2 048 | 2560 | 3072 | 3548 | 4 096|
| Maximální počet OLTP v paměti úložiště na jeden fond (GB) | 16 | 20 | 24 | 28 | 32 |
| Maximální počet databází pro každý fond | 100 | 100 | 100 | 100 | 100 | 
| Maximální počet souběžných pracovních procesů (požadavků) na fond | 3200 | 4000 | 4800 | 5600 | 6400 |
| Maximální počet souběžných relací na fond | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Možnosti min Edtu na databázi | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750, 4 000 | 
| Možnosti Max Edtu na databázi | 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 25, 50, 75, 125, 250, 500, 1 000, 1 750, 4 000 | 
| Maximální velikost úložiště na databázi (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

> [!IMPORTANT]
> Více než 1 TB místa v úrovni Premium je nyní k dispozici ve všech oblastech s následující výjimkou: Spojené království – sever, – Západ střední USA, UK South2, východní Čína, USDoDCentral, Německo – střed, indiánský motiv verze pro státní správu USDoDEast, USA, nám verze pro státní správu – jih centrální, Německo – severovýchod, Čína Severní, verze pro státní správu USA – východ. V ostatních oblastech je úložiště na úrovni Premium omezeno na 1 TB. Viz [Aktuální omezení pro P11–P15](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

Pokud se využívají všechny DTU elastického fondu, pak každá databáze ve fondu obdrží stejné množství prostředků ke zpracování dotazů. Služba SQL Database poskytuje spravedlivé sdílení prostředků mezi databázemi tím, že zajišťuje rovnoměrné rozdělení výpočetního času. Pokud je minimální počet DTU na databázi nastaven na nenulovou hodnotu, pro každou databázi bude garantováno spravedlivé sdílení prostředků elastického fondu je společně s libovolným množstvím prostředků.

### <a name="database-properties-for-pooled-databases"></a>Vlastnosti databáze pro databáze ve fondu

Následující tabulka popisuje vlastnosti pro databáze ve fondu.

| Vlastnost | Popis |
|:--- |:--- |
| Maximální počet eDTU na databázi |Maximální počet eDTU, které může využívat libovolná databáze ve fondu za předpokladu, že jsou dostupné v závislosti na využití ostatními databázemi ve fondu. Maximální počet eDTU na databázi není garancí prostředků pro databázi. Toto nastavení je globální a platí pro všechny databáze ve fondu. Nastavte maximální počet eDTU na databázi dostatečně vysoký, aby databáze zvládly využití ve špičkách. Očekává se určitý stupeň předimenzování, protože fond obecně předpokládá studené a horké vzory používání, při nichž nenastávají špičky u všech databází ve stejnou chvíli. Předpokládejme například, že využití ve špičce na databázi je 20 eDTU a špička v jednu chvíli nastává pouze u 20 % ze 100 databází ve fondu. Pokud je maximální počet eDTU na databázi nastaven na 20 eDTU, je rozumné fond 5krát předimenzovat a nastavit počet eDTU na fond na 400. |
| Minimální počet eDTU na databázi |Minimální počet eDTU garantovaných pro každou databázi ve fondu. Toto nastavení je globální a platí pro všechny databáze ve fondu. Minimální počet eDTU na databázi může být nastaven na 0, což je i výchozí hodnota. Tato vlastnost je nastavena na libovolnou hodnotu mezi 0 a průměrným využitím eDTU na databázi. Násobek počtu databází ve fondu a minimálním počtem eDTU na databázi nemůže překročit počet eDTU na fond. Pokud je například ve fondu 20 databází a minimální počet eDTU na databázi je nastaven na 10 eDTU, pak musí být počet eDTU na fond alespoň 200 eDTU. |
| Maximální počet úložiště na databázi |Maximální velikost nastavený uživatelem databáze ve fondu. Však sdílet databáze ve fondu úložiště přidělené fondu. I v případě celkové maximální úložiště *na databázi* je nastaven jako větší než celkový úložiště k dispozici *místa na fondu*, celkového místa ve skutečnosti používané všechny databáze, nebude možné delší než limit fondu k dispozici. Maximální velikost databáze odkazuje na maximální velikost datové soubory a nezahrnuje místo využité soubory protokolu. |
|||
 


## <a name="next-steps"></a>Další postup

- V tématu [nejčastější dotazy k databázi SQL](sql-database-faq.md) odpovědi na nejčastější dotazy.
- Informace o obecných omezeních Azure najdete v tématu [předplatného Azure a omezení služby, kvóty a omezení](../azure-subscription-service-limits.md).
- Informace o Dtu a Edtu najdete v tématu [Dtu a Edtu](sql-database-what-is-a-dtu.md).
- Informace o databázi tempdb velikosti omezení naleznete v tématu https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
