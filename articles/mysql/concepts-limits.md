---
title: Omezení – Azure Database for MySQL
description: Tento článek popisuje omezení Azure Database for MySQL, například počet připojení a možnosti modulu úložiště.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 9cf5c958a0dd9a19e6b976ff36a18c45e062f604
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659930"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Omezení Azure Database for MySQL
Následující části popisují kapacitu, podporu modulu úložiště, podporu oprávnění, podporu příkazů pro manipulaci s daty a funkční omezení v databázové službě. Viz také [Obecná omezení](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) platná pro databázový stroj MySQL.

## <a name="server-parameters"></a>Parametry serveru

Minimální a maximální hodnoty několika oblíbených parametrů serveru se určují v cenové úrovni a v virtuální jádra. Omezení najdete v následujících tabulkách.

### <a name="max_connections"></a>max_connections

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota**|**Minimální hodnota**|**Maximální hodnota**|
|---|---|---|---|---|
|Základní|1|50|10|50|
|Základní|2|100|10|100|
|Pro obecné účely|2|300|10|600|
|Pro obecné účely|4|625|10|1250|
|Pro obecné účely|8|1250|10|2500|
|Pro obecné účely|16|2500|10|5000|
|Pro obecné účely|32|5000|10|10000|
|Pro obecné účely|64|10000|10|20000|
|Optimalizováno pro paměť|2|600|10|800|
|Optimalizováno pro paměť|4|1250|10|2500|
|Optimalizováno pro paměť|8|2500|10|5000|
|Optimalizováno pro paměť|16|5000|10|10000|
|Optimalizováno pro paměť|32|10000|10|20000|

Když připojení překročí limit, může se zobrazit následující chyba:
> Chyba 1040 (08004): příliš mnoho připojení

> [!IMPORTANT]
> Pro dosažení optimálního prostředí doporučujeme, abyste k efektivní správě připojení používali připojení Pooler jako ProxySQL.

Vytváření nových připojení klientů k MySQL trvá čas i po jejich navázání, tato připojení zabírají databázové prostředky i v případě nečinnosti. Většina aplikací vyžaduje mnoho krátkodobých připojení, což je v této situaci celé. Výsledkem je méně prostředků dostupných pro vaše skutečné zatížení, což vede ke snížení výkonu. Připojení Pooler, které zkracuje nečinné připojení a znovu používá existující připojení, jim pomůže vyhnout se. Další informace o nastavení ProxySQL najdete v našem [blogovém příspěvku](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

### <a name="query_cache_size"></a>query_cache_size

Mezipaměť dotazů je ve výchozím nastavení vypnutá. Pokud chcete povolit mezipaměť dotazů, nakonfigurujte `query_cache_type` parametr. 

Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) .

> [!NOTE]
> Mezipaměť dotazů je zastaralá od MySQL 5.7.20 a byla odebrána v MySQL 8,0

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota**|**Minimální hodnota**|**Maximální hodnota**|
|---|---|---|---|---|
|Základní|1|Nekonfigurovatelné na úrovni Basic|–|–|
|Základní|2|Nekonfigurovatelné na úrovni Basic|–|–|
|Pro obecné účely|2|0|0|16777216|
|Pro obecné účely|4|0|0|33554432|
|Pro obecné účely|8|0|0|67108864|
|Pro obecné účely|16|0|0|134217728|
|Pro obecné účely|32|0|0|134217728|
|Pro obecné účely|64|0|0|134217728|
|Optimalizováno pro paměť|2|0|0|33554432|
|Optimalizováno pro paměť|4|0|0|67108864|
|Optimalizováno pro paměť|8|0|0|134217728|
|Optimalizováno pro paměť|16|0|0|134217728|
|Optimalizováno pro paměť|32|0|0|134217728|

### <a name="sort_buffer_size"></a>sort_buffer_size

Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) .

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota**|**Minimální hodnota**|**Maximální hodnota**|
|---|---|---|---|---|
|Základní|1|Nekonfigurovatelné na úrovni Basic|–|–|
|Základní|2|Nekonfigurovatelné na úrovni Basic|–|–|
|Pro obecné účely|2|524288|32768|4194304|
|Pro obecné účely|4|524288|32768|8388608|
|Pro obecné účely|8|524288|32768|16777216|
|Pro obecné účely|16|524288|32768|33554432|
|Pro obecné účely|32|524288|32768|33554432|
|Pro obecné účely|64|524288|32768|33554432|
|Optimalizováno pro paměť|2|524288|32768|8388608|
|Optimalizováno pro paměť|4|524288|32768|16777216|
|Optimalizováno pro paměť|8|524288|32768|33554432|
|Optimalizováno pro paměť|16|524288|32768|33554432|
|Optimalizováno pro paměť|32|524288|32768|33554432|

### <a name="join_buffer_size"></a>join_buffer_size

Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) .

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota**|**Minimální hodnota**|**Maximální hodnota**|
|---|---|---|---|---|
|Základní|1|Nekonfigurovatelné na úrovni Basic|–|–|
|Základní|2|Nekonfigurovatelné na úrovni Basic|–|–|
|Pro obecné účely|2|262144|128|268435455|
|Pro obecné účely|4|262144|128|536870912|
|Pro obecné účely|8|262144|128|1073741824|
|Pro obecné účely|16|262144|128|2147483648|
|Pro obecné účely|32|262144|128|4294967295|
|Pro obecné účely|64|262144|128|4294967295|
|Optimalizováno pro paměť|2|262144|128|536870912|
|Optimalizováno pro paměť|4|262144|128|1073741824|
|Optimalizováno pro paměť|8|262144|128|2147483648|
|Optimalizováno pro paměť|16|262144|128|4294967295|
|Optimalizováno pro paměť|32|262144|128|4294967295|

### <a name="max_heap_table_size"></a>max_heap_table_size

Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) .

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota**|**Minimální hodnota**|**Maximální hodnota**|
|---|---|---|---|---|
|Základní|1|Nekonfigurovatelné na úrovni Basic|–|–|
|Základní|2|Nekonfigurovatelné na úrovni Basic|–|–|
|Pro obecné účely|2|16777216|16384|268435455|
|Pro obecné účely|4|16777216|16384|536870912|
|Pro obecné účely|8|16777216|16384|1073741824|
|Pro obecné účely|16|16777216|16384|2147483648|
|Pro obecné účely|32|16777216|16384|4294967295|
|Pro obecné účely|64|16777216|16384|4294967295|
|Optimalizováno pro paměť|2|16777216|16384|536870912|
|Optimalizováno pro paměť|4|16777216|16384|1073741824|
|Optimalizováno pro paměť|8|16777216|16384|2147483648|
|Optimalizováno pro paměť|16|16777216|16384|4294967295|
|Optimalizováno pro paměť|32|16777216|16384|4294967295|

### <a name="tmp_table_size"></a>tmp_table_size

Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) .

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota**|**Minimální hodnota**|**Maximální hodnota**|
|---|---|---|---|---|
|Základní|1|Nekonfigurovatelné na úrovni Basic|–|–|
|Základní|2|Nekonfigurovatelné na úrovni Basic|–|–|
|Pro obecné účely|2|16777216|1024|67108864|
|Pro obecné účely|4|16777216|1024|134217728|
|Pro obecné účely|8|16777216|1024|268435456|
|Pro obecné účely|16|16777216|1024|536870912|
|Pro obecné účely|32|16777216|1024|1073741824|
|Pro obecné účely|64|16777216|1024|1073741824|
|Optimalizováno pro paměť|2|16777216|1024|134217728|
|Optimalizováno pro paměť|4|16777216|1024|268435456|
|Optimalizováno pro paměť|8|16777216|1024|536870912|
|Optimalizováno pro paměť|16|16777216|1024|1073741824|
|Optimalizováno pro paměť|32|16777216|1024|1073741824|

### <a name="time_zone"></a>time_zone

Tabulky časových pásem lze naplnit voláním `mysql.az_load_timezone` uložené procedury z nástroje, jako je například příkazový řádek MySQL nebo MySQL Workbench. Informace o tom, jak volat uloženou proceduru a nastavit globální časová pásma na úrovni relace, najdete v článcích [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) nebo [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) .

### <a name="innodb_file_per_table"></a>innodb_file_per_table

MySQL ukládá tabulku InnoDB v různých tabulkových prostorech na základě konfigurace, kterou jste zadali během vytváření tabulky. [Systémový tabulkový prostor](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) je oblast úložiště pro slovník InnoDB data Dictionary. [Tabulkový prostor pro tabulku](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) obsahuje data a indexy pro jednu tabulku InnoDB a je uložený v systému souborů ve vlastním datovém souboru. Toto chování je řízeno `innodb_file_per_table` parametrem serveru. Nastavení `innodb_file_per_table` na `OFF` způsobí, že InnoDB vytvoří tabulky v systémovém tabulkovém prostoru. V opačném případě InnoDB vytvoří tabulky v tabulkových prostorech v souborové tabulce.

V jednom datovém souboru podporuje Azure Database for MySQL v největších **1 TB**. Pokud je velikost databáze větší než 1 TB, měli byste vytvořit tabulku v [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) tabulkovém prostoru. Pokud máte velikost jedné tabulky větší než 1 TB, měli byste použít tabulku oddílů.

## <a name="storage-engine-support"></a>Podpora modulu úložiště

### <a name="supported"></a>Podporuje se
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [REZIDENT](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nepodporované
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ZÁLOHOVAT](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDEROVANÉ](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Podpora oprávnění

### <a name="unsupported"></a>Nepodporované
- Role DBA: mnoho parametrů serveru a nastavení může nechtěně snížit výkon serveru nebo vlastnosti s nezápornou KYSELINou systému DBMS. Za účelem zachování integrity služby a smlouvy SLA na úrovni produktu Tato služba nevystavuje roli DBA. Výchozí uživatelský účet, který je vytvořen při vytvoření nové instance databáze, umožňuje tomuto uživateli provádět většinu příkazů DDL a DML v instanci spravované databáze. 
- Superuživatele (SUPER Privileged Privilege) je taky omezené [oprávnění Super](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) .
- DEFINe: vyžaduje pro vytvoření a omezení superuživatele oprávnění. Pokud importujete data pomocí zálohy, odeberte `CREATE DEFINER` příkazy ručně nebo pomocí `--skip-definer` příkazu při provádění mysqldump.

## <a name="data-manipulation-statement-support"></a>Podpora příkazů manipulace s daty

### <a name="supported"></a>Podporuje se
- `LOAD DATA INFILE`je podporováno, ale `[LOCAL]` musí být zadán parametr a směrován na cestu UNC (úložiště Azure připojené prostřednictvím protokolu SMB).

### <a name="unsupported"></a>Nepodporované
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Funkční omezení

### <a name="scale-operations"></a>Operace škálování
- Dynamické škálování na cenové úrovně Basic a z se v tuto chvíli nepodporuje.
- Zmenšení velikosti úložiště serveru se nepodporuje.

### <a name="server-version-upgrades"></a>Upgrady verze serveru
- Automatizovaná migrace mezi hlavními verzemi databázového stroje není v současnosti podporovaná. Pokud chcete upgradovat na další hlavní verzi, [vystavte výpis a obnovte](./concepts-migrate-dump-restore.md) ho na serveru, který byl vytvořen s novou verzí modulu.

### <a name="point-in-time-restore"></a>Obnovení do bodu v čase
- Při použití funkce PITR je nový server vytvořen se stejnými konfiguracemi jako server, na kterém je založena.
- Obnovení odstraněného serveru se nepodporuje.

### <a name="vnet-service-endpoints"></a>Koncové body služeb virtuální sítě
- Podpora koncových bodů služby virtuální sítě je určená jenom pro Pro obecné účely a paměťově optimalizované servery.

### <a name="storage-size"></a>Velikost úložiště
- Omezení velikosti úložiště na cenové úrovni najdete v [cenové úrovni](concepts-pricing-tiers.md) .

## <a name="current-known-issues"></a>Aktuální známé problémy
- Instance serveru MySQL po navázání připojení zobrazuje chybovou verzi serveru. Správnou verzi modulu instance serveru získáte pomocí `select version();` příkazu.

## <a name="next-steps"></a>Další kroky
- [Co je dostupné v jednotlivých úrovních služby](concepts-pricing-tiers.md)
- [Podporované verze databáze MySQL](concepts-supported-versions.md)
