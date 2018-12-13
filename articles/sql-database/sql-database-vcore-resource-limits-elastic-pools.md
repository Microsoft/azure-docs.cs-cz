---
title: Limity založený na virtuálních jádrech prostředků Azure SQL Database – elastických fondů | Dokumentace Microsoftu
description: Tato stránka popisuje některé běžné prostředek založený na virtuálních jádrech limity pro elastické fondy Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: f41974c6e2b2b0565f0a2703cfd638777a6bb9eb
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52878019"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-elastic-pools"></a>Založený na virtuálních jádrech zakoupení modelu limity pro elastické fondy Azure SQL Database

Tento článek obsahuje podrobné prostředků limity pro elastické fondy Azure SQL Database a databáze ve fondu pomocí nákupní model založený na virtuálních jádrech.

Založený na DTU nákupní model omezení najdete v tématu [omezení prostředků založený na DTU databáze SQL – elastických fondů](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

Můžete nastavit úroveň služby, výpočetního prostředí a pomocí velikost úložiště [webu Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [rozhraní příkazového řádku Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases), nebo [rozhraní REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!NOTE]
> Omezení prostředků jednotlivých databází v elastických fondech jsou obvykle stejné jako u izolovaných databází mimo fondy, které má stejnou výpočetní velikost. Maximální počet souběžných pracovních procesů pro databázi GP_Gen4_1 je například 200 pracovních procesů. Maximální počet souběžných pracovních procesů pro databáze ve fondu GP_Gen4_1 je tedy také 200 pracovních procesů. Všimněte si, že se celkový počet souběžných pracovních procesů ve fondu GP_Gen4_1 je 210.

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Obecné účely vrstvy služby: velikosti úložiště a výpočty velikostí

### <a name="generation-4-compute-platform"></a>Výpočetní platforma běžící generace 4

|Vypočítat velikost|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování H/W|4|4|4|4|4|4|
|Virtuální jádra|1|2|4|8|16|24|
|Paměť (GB)|7|14|28|56|112|168|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Typ úložiště|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|
|Maximální velikost dat (GB)|512|756|1536|2 048|3584|4 096|
|Maximální velikost protokolu|154|227|461|614|1075|1229|
|Databáze TempDB size(DB)|32|64|128|256|384|384|
|Cíl vstupně-výstupních operací (64 KB)|500|1000|2000|4000|7000|7000|
|Vstupně-výstupní latence (přibližné)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|
|Maximální počet souběžných pracovních procesů (požadavků)|210|420|840|1680|3360|5040|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|
|Maximální počet databází pro každý fond|100|200|500|500|500|500|
|Volby pro elastické fondy – vCore min/max na databázi|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|
|Počet replik|1|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si horizontální navýšení kapacity|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|
|||

### <a name="generation-5-compute-platform"></a>Výpočetní platforma běžící generace 5

|Vypočítat velikost|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Generování H/W|5|5|5|5|5|5|5|5|
|Virtuální jádra|2|4|8|16|24|32|40|80|
|Paměť (GB)|11|22|44|88|132|176|220|440|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Typ úložiště|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|
|Maximální velikost dat (GB)|512|756|1536|2 048|3072|4 096|4 096|4 096|
|Maximální velikost protokolu|154|227|461|614|922|1229|1229|1229|
|Databáze TempDB size(DB)|64|128|256|384|384|384|384|384|
|Cíl vstupně-výstupních operací (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Vstupně-výstupní latence (přibližné)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|
|Maximální počet souběžných pracovních procesů (požadavků)|210|420|840|1680|2520|3360|4200|8400
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|30000|30000|
|Maximální počet databází pro každý fond|100|200|500|500|500|500|500|500|
|Volby pro elastické fondy – vCore min/max na databázi|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, 0,5, 1, 2, 4, 8, 16, 24, 32|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Počet replik|1|1|1|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si horizontální navýšení kapacity|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|
|||

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Obchodní vrstvy služby: velikosti úložiště a výpočty velikostí

### <a name="generation-4-compute-platform"></a>Výpočetní platforma běžící generace 4

|Vypočítat velikost|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování H/W|4|4|4|4|4|4|
|Virtuální jádra|1|2|4|8|16|24|
|Paměť (GB)|7|14|28|56|112|168|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1|2|4|8|20|36|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|1024|
|Maximální velikost protokolu|307|307|307|307|307|307|
|Databáze TempDB size(DB)|32|64|128|256|384|384|
|Cíl vstupně-výstupních operací (64 KB)|5000|10000|20000|40000|80000|120000|
|Vstupně-výstupní latence (přibližné)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|
|Maximální počet souběžných pracovních procesů (požadavků)|210|420|840|1680|3360|5040|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|
|Maximální počet databází pro každý fond|Pro tato velikost výpočetních jsou podporovány pouze jedné databáze|50|100|100|100|100|
|Volby pro elastické fondy – vCore min/max na databázi|neuvedeno|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|
|Počet replik|3|3|3|3|3|3|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si horizontální navýšení kapacity|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|
|||

#### <a name="generation-5-compute-platform"></a>Výpočetní platforma běžící generace 5

|Vypočítat velikost|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Generování H/W|5|5|5|5|5|5|5|5|
|Virtuální jádra|2|4|8|16|24|32|40|80|
|Paměť (GB)|11|22|44|88|132|176|220|440|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1.571|3,142|6.284|15.768|25.252|37.936|52.22|131.64|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Vstupně-výstupní latence (přibližné)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|
|Maximální velikost dat (GB)|1024|1024|1024|1024|2 048|4 096|4 096|4 096|
|Maximální velikost protokolu|307|307|307|307|614|1229|1229|1229|
|Databáze TempDB size(DB)|64|128|256|384|384|384|384|384|
|Cíl vstupně-výstupních operací (64 KB)|5000|10000|20000|40000|60000|80000|100000|200000
|Maximální počet souběžných pracovních procesů (požadavků)|210|420|840|1680|2520|3360|5040|8400|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|30000|30000|
|Maximální počet databází pro každý fond|neuvedeno|50|100|100|100|100|100|100|
|Volby pro elastické fondy – vCore min/max na databázi|neuvedeno|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, 0,5, 1, 2, 4, 8, 16, 24, 32|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Počet replik|3|3|3|3|3|3|3|3|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si horizontální navýšení kapacity|Ano|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|
|||

Pokud se všechny virtuální jádra pro elastický fond je zaneprázdněný, každá databáze ve fondu obdrží stejné množství výpočetních prostředků pro zpracování dotazů. Služba SQL Database poskytuje spravedlivé sdílení prostředků mezi databázemi tím, že zajišťuje rovnoměrné rozdělení výpočetního času. Spravedlivé sdílení prostředků elastického fondu je kromě libovolným množstvím prostředků jinak zaručena pro každou databázi, pokud je minimální počet virtuálních jader na databázi nastaven na nenulovou hodnotu.

## <a name="database-properties-for-pooled-databases"></a>Vlastnosti databáze pro databáze ve fondu

Následující tabulka popisuje vlastnosti pro databáze ve fondu.

| Vlastnost | Popis |
|:--- |:--- |
| Maximální počet virtuálních jader na databázi |Maximální počet virtuálních jader, které může použít libovolnou databázi ve fondu, pokud jsou dostupné v závislosti na využití ostatními databázemi ve fondu. Maximální počet virtuálních jader na databázi není garancí prostředků pro databázi. Toto nastavení je globální a platí pro všechny databáze ve fondu. Nastavit maximální počet virtuálních jader na databázi dostatečně vysoký, aby se pro zpracování špičky využití databáze. Určitý stupeň over-pass-the potvrzování očekává se, protože fond obecně předpokládá využití horké a studené vzory pro databáze kde nejsou všechny databáze peaking současně.|
| Min virtuálních jader na databázi |Minimální počet virtuálních jader, které je zaručeno, že všechny databáze ve fondu. Toto nastavení je globální a platí pro všechny databáze ve fondu. Min virtuálních jader na databázi může být nastaven na hodnotu 0 a výchozí hodnota je také. Tato vlastnost nastavena na libovolné místo mezi 0 a využití průměrné virtuálních jader na databázi. Součinu počtu databází ve fondu a minimální virtuálních jader na databázi nesmí přesáhnout virtuálních jader na fond.|
| Max. úložiště na databázi |Maximální velikosti databáze nastavena podle uživatele pro databázi ve fondu. Databáze ve fondu sdílejí úložiště přidělené fondu, proto je velikost databáze můžete oslovit omezena na menší zbývající úložiště fondu a velikost databáze. Maximální velikost databáze odkazuje na maximální velikost datových souborů a nezahrnuje místo, které využívají soubory protokolu. |
|||

## <a name="next-steps"></a>Další postup

- Zobrazit [nejčastější dotazy k SQL Database](sql-database-faq.md) odpovědi na nejčastější dotazy.
- Zobrazit [Přehled prostředků omezuje na logickém serveru](sql-database-resource-limits-logical-server.md) informace o omezeních na úrovni serveru a předplatné.
- Informace o obecných omezeních Azure najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md).
