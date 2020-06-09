---
title: Parametry serveru – Azure Database for MySQL
description: Toto téma poskytuje pokyny pro konfiguraci parametrů serveru v Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/5/2020
ms.openlocfilehash: f78e6969c98545ec0b9b3e0a0822d4b9ae35903a
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84562004"
---
# <a name="server-parameters-in-azure-database-for-mysql"></a>Parametry serveru v Azure Database for MySQL

Tento článek popisuje informace a pokyny pro konfiguraci parametrů serveru v Azure Database for MySQL.

## <a name="what-are-server-parameters"></a>Co jsou parametry serveru? 

Modul MySQL poskytuje mnoho různých proměnných serveru/parametrů, které lze použít ke konfiguraci a ladění chování modulu. Některé parametry lze nastavit dynamicky během doby běhu, zatímco jiné jsou "statické", což vyžaduje restart serveru, aby se mohl použít.

Azure Database for MySQL zpřístupňuje možnost změnit hodnotu různých parametrů serveru MySQL pomocí [Azure Portal](./howto-server-parameters.md), rozhraní příkazového [řádku Azure](./howto-configure-server-parameters-using-cli.md)a [PowerShellu](./howto-configure-server-parameters-using-powershell.md) tak, aby odpovídaly potřebám vašich úloh.

## <a name="configurable-server-parameters"></a>Konfigurovatelné parametry serveru

Seznam podporovaných parametrů serveru se neustále zvětšuje. Pomocí karty parametry serveru v Azure Portal zobrazte úplný seznam a nakonfigurujte hodnoty parametrů serveru.

Další informace o omezeních několika běžně aktualizovaných parametrů serveru najdete v následujících částech. Omezení se určují podle cenové úrovně a virtuální jádra serveru.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) .

#### <a name="servers-supporting-up-to-4-tb-storage"></a>Servery podporující až 4 TB úložiště

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota (bajty)**|**Minimální hodnota (bajty)**|**Maximální hodnota (v bajtech)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|Pro obecné účely|2|3758096384|134217728|3758096384|
|Pro obecné účely|4|8053063680|134217728|8053063680|
|Pro obecné účely|8|16106127360|134217728|16106127360|
|Pro obecné účely|16|32749125632|134217728|32749125632|
|Pro obecné účely|32|66035122176|134217728|66035122176|
|Pro obecné účely|64|132070244352|134217728|132070244352|
|Optimalizováno pro paměť|2|7516192768|134217728|7516192768|
|Optimalizováno pro paměť|4|16106127360|134217728|16106127360|
|Optimalizováno pro paměť|8|32212254720|134217728|32212254720|
|Optimalizováno pro paměť|16|65498251264|134217728|65498251264|
|Optimalizováno pro paměť|32|132070244352|134217728|132070244352|

#### <a name="servers-support-up-to-16-tb-storage"></a>Servery podporují až 16 TB úložiště.

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota (bajty)**|**Minimální hodnota (bajty)**|**Maximální hodnota (v bajtech)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|Pro obecné účely|2|7516192768|134217728|7516192768|
|Pro obecné účely|4|16106127360|134217728|16106127360|
|Pro obecné účely|8|32212254720|134217728|32212254720|
|Pro obecné účely|16|65498251264|134217728|65498251264|
|Pro obecné účely|32|132070244352|134217728|132070244352|
|Pro obecné účely|64|264140488704|134217728|264140488704|
|Optimalizováno pro paměť|2|15032385536|134217728|15032385536|
|Optimalizováno pro paměť|4|32212254720|134217728|32212254720|
|Optimalizováno pro paměť|8|64424509440|134217728|64424509440|
|Optimalizováno pro paměť|16|130996502528|134217728|130996502528|
|Optimalizováno pro paměť|32|264140488704|134217728|264140488704|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

> [!NOTE]
> `innodb_file_per_table`dá se aktualizovat jenom v Pro obecné účely a paměťově optimalizované cenové úrovně.

MySQL ukládá tabulku InnoDB v různých tabulkových prostorech na základě konfigurace, kterou jste zadali během vytváření tabulky. [Systémový tabulkový prostor](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) je oblast úložiště pro slovník InnoDB data Dictionary. [Tabulkový prostor pro tabulku](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) obsahuje data a indexy pro jednu tabulku InnoDB a je uložený v systému souborů ve vlastním datovém souboru. Toto chování je řízeno `innodb_file_per_table` parametrem serveru. Nastavení `innodb_file_per_table` na `OFF` způsobí, že InnoDB vytvoří tabulky v systémovém tabulkovém prostoru. V opačném případě InnoDB vytvoří tabulky v tabulkových prostorech v souborové tabulce.

V jednom datovém souboru podporuje Azure Database for MySQL v největších **1 TB**. Pokud je velikost databáze větší než 1 TB, měli byste vytvořit tabulku v [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) tabulkovém prostoru. Pokud máte velikost jedné tabulky větší než 1 TB, měli byste použít tabulku oddílů.

### <a name="join_buffer_size"></a>join_buffer_size

Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) .

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota (bajty)**|**Minimální hodnota (bajty)**|**Maximální hodnota (v bajtech)**|
|---|---|---|---|---|
|Basic|1|Nekonfigurovatelné na úrovni Basic|–|–|
|Basic|2|Nekonfigurovatelné na úrovni Basic|–|–|
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

### <a name="max_connections"></a>max_connections

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota**|**Minimální hodnota**|**Maximální hodnota**|
|---|---|---|---|---|
|Basic|1|50|10|50|
|Basic|2|100|10|100|
|Pro obecné účely|2|300|10|600|
|Pro obecné účely|4|625|10|1250|
|Pro obecné účely|8|1250|10|2500|
|Pro obecné účely|16|2500|10|5000|
|Pro obecné účely|32|5000|10|10000|
|Pro obecné účely|64|10000|10|20000|
|Optimalizováno pro paměť|2|625|10|1250|
|Optimalizováno pro paměť|4|1250|10|2500|
|Optimalizováno pro paměť|8|2500|10|5000|
|Optimalizováno pro paměť|16|5000|10|10000|
|Optimalizováno pro paměť|32|10000|10|20000|

Když připojení překročí limit, může se zobrazit následující chyba:
> Chyba 1040 (08004): příliš mnoho připojení

> [!IMPORTANT]
> Pro dosažení optimálního prostředí doporučujeme, abyste k efektivní správě připojení používali připojení Pooler jako ProxySQL.

Vytváření nových připojení klientů k MySQL trvá čas i po jejich navázání, tato připojení zabírají databázové prostředky i v případě nečinnosti. Většina aplikací vyžaduje mnoho krátkodobých připojení, což je v této situaci celé. Výsledkem je méně prostředků dostupných pro vaše skutečné zatížení, což vede ke snížení výkonu. Připojení Pooler, které zkracuje nečinné připojení a znovu používá existující připojení, jim pomůže vyhnout se. Další informace o nastavení ProxySQL najdete v našem [blogovém příspěvku](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

### <a name="max_heap_table_size"></a>max_heap_table_size

Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) .

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota (bajty)**|**Minimální hodnota (bajty)**|**Maximální hodnota (v bajtech)**|
|---|---|---|---|---|
|Basic|1|Nekonfigurovatelné na úrovni Basic|–|–|
|Basic|2|Nekonfigurovatelné na úrovni Basic|–|–|
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

### <a name="query_cache_size"></a>query_cache_size

Mezipaměť dotazů je ve výchozím nastavení vypnutá. Pokud chcete povolit mezipaměť dotazů, nakonfigurujte `query_cache_type` parametr. 

Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) .

> [!NOTE]
> Mezipaměť dotazů je zastaralá od MySQL 5.7.20 a byla odebrána v MySQL 8,0

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota (bajty)**|**Minimální hodnota (bajty)**|* * Maximální hodnota * *|
|---|---|---|---|---|
|Basic|1|Nekonfigurovatelné na úrovni Basic|–|–|
|Basic|2|Nekonfigurovatelné na úrovni Basic|–|–|
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

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota (bajty)**|**Minimální hodnota (bajty)**|**Maximální hodnota (v bajtech)**|
|---|---|---|---|---|
|Basic|1|Nekonfigurovatelné na úrovni Basic|–|–|
|Basic|2|Nekonfigurovatelné na úrovni Basic|–|–|
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

### <a name="tmp_table_size"></a>tmp_table_size

Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) .

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota (bajty)**|**Minimální hodnota (bajty)**|**Maximální hodnota (v bajtech)**|
|---|---|---|---|---|
|Basic|1|Nekonfigurovatelné na úrovni Basic|–|–|
|Basic|2|Nekonfigurovatelné na úrovni Basic|–|–|
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

## <a name="non-configurable-server-parameters"></a>Nekonfigurovatelné parametry serveru

Níže uvedené parametry serveru se ve službě nedají konfigurovat:

|**Parametr**|**Pevná hodnota**|
| :------------------------ | :-------- |
|innodb_file_per_table na úrovni Basic|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|MB|
|innodb_log_files_in_group|2|

Další proměnné, které zde nejsou uvedeny, jsou nastaveny na výchozí hodnoty, které jsou předem připravené pro MySQL. Výchozí hodnoty najdete v dokumentaci MySQL pro verze [8,0](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html), [5,7](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)a [5,6](https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html) . 

## <a name="next-steps"></a>Další kroky

- Naučte se [Konfigurovat parametry serveru pomocí Azure Portal](./howto-server-parameters.md)
- Naučte se [Konfigurovat parametry serveru pomocí Azure CLI](./howto-configure-server-parameters-using-cli.md) .
- Naučte se [Konfigurovat parametry serveru pomocí PowerShellu](./howto-configure-server-parameters-using-powershell.md) .