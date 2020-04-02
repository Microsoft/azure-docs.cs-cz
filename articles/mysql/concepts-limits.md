---
title: Omezení – databáze Azure pro MySQL
description: Tento článek popisuje omezení v Azure Database pro MySQL, jako je například počet možností připojení a úložiště motoru.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 6ca09ab0578fb88e443d6e9e1f920c22457eb042
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548471"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Omezení v azure databázi pro MySQL
Následující části popisují kapacitu, podporu modulu úložiště, podporu oprávnění, podporu příkazu pro manipulaci s daty a funkční limity v databázové službě. Viz také [obecná omezení](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) vztahující se na databázový stroj MySQL.

## <a name="server-parameters"></a>Parametry serveru

Minimální a maximální hodnoty několika populárních parametrů serveru jsou určeny cenovou vrstvou a virtuálními jádry. Omezení naleznete v následujících tabulkách.

### <a name="max_connections"></a>max_connections

|**Cenová úroveň**|**virtuální jádra (hlavní)**|**Výchozí hodnota**|**Hodnota min**|**Maximální hodnota**|
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

Pokud připojení překročí limit, může se zobrazit následující chyba:
> CHYBA 1040 (08004): Příliš mnoho připojení

> [!IMPORTANT]
> Pro nejlepší zkušenosti, doporučujeme použít připojení sdružovací jako ProxySQL efektivně spravovat připojení.

Vytváření nových klientských připojení k MySQL vyžaduje čas a po navázání tato připojení zabírají databázové prostředky, i když jsou nečinné. Většina aplikací požadovat mnoho krátkodobé připojení, což komplikuje tuto situaci. Výsledkem je méně prostředků, které jsou k dispozici pro skutečné zatížení, což vede ke snížení výkonu. Sdružovací připojení, které snižuje nečinné připojení a opakovaně používá existující připojení, vám pomůže tomu zabránit. Chcete-li se dozvědět o nastavení ProxySQL, navštivte náš [blog post](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

### <a name="query_cache_size"></a>query_cache_size

Mezipaměť dotazů je ve výchozím nastavení vypnutá. Chcete-li povolit mezipaměť dotazů, nakonfigurujte `query_cache_type` parametr. 

Další informace o tomto parametru naleznete v dokumentaci k [MySQL.](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size)

> [!NOTE]
> Mezipaměť dotazů je od MySQL 5.7.20 zastaralá a byla odebrána v MySQL 8.0

|**Cenová úroveň**|**virtuální jádra (hlavní)**|**Výchozí hodnota**|**Hodnota min**|**Maximální hodnota**|
|---|---|---|---|---|
|Základní|1|Nelze konfigurovat v základní vrstvě|Není dostupné.|Není dostupné.|
|Základní|2|Nelze konfigurovat v základní vrstvě|Není dostupné.|Není dostupné.|
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

Další informace o tomto parametru naleznete v dokumentaci k [MySQL.](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size)

|**Cenová úroveň**|**virtuální jádra (hlavní)**|**Výchozí hodnota**|**Hodnota min**|**Maximální hodnota**|
|---|---|---|---|---|
|Základní|1|Nelze konfigurovat v základní vrstvě|Není dostupné.|Není dostupné.|
|Základní|2|Nelze konfigurovat v základní vrstvě|Není dostupné.|Není dostupné.|
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

Další informace o tomto parametru naleznete v dokumentaci k [MySQL.](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size)

|**Cenová úroveň**|**virtuální jádra (hlavní)**|**Výchozí hodnota**|**Hodnota min**|**Maximální hodnota**|
|---|---|---|---|---|
|Základní|1|Nelze konfigurovat v základní vrstvě|Není dostupné.|Není dostupné.|
|Základní|2|Nelze konfigurovat v základní vrstvě|Není dostupné.|Není dostupné.|
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

Další informace o tomto parametru naleznete v dokumentaci k [MySQL.](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size)

|**Cenová úroveň**|**virtuální jádra (hlavní)**|**Výchozí hodnota**|**Hodnota min**|**Maximální hodnota**|
|---|---|---|---|---|
|Základní|1|Nelze konfigurovat v základní vrstvě|Není dostupné.|Není dostupné.|
|Základní|2|Nelze konfigurovat v základní vrstvě|Není dostupné.|Není dostupné.|
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

Další informace o tomto parametru naleznete v dokumentaci k [MySQL.](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size)

|**Cenová úroveň**|**virtuální jádra (hlavní)**|**Výchozí hodnota**|**Hodnota min**|**Maximální hodnota**|
|---|---|---|---|---|
|Základní|1|Nelze konfigurovat v základní vrstvě|Není dostupné.|Není dostupné.|
|Základní|2|Nelze konfigurovat v základní vrstvě|Není dostupné.|Není dostupné.|
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

Tabulky časových pásem mohou být `mysql.az_load_timezone` naplněny voláním uložené procedury z nástroje, jako je příkazový řádek MySQL nebo MySQL Workbench. Nawebu na [portál Azure nebo](howto-server-parameters.md#working-with-the-time-zone-parameter) články azure [cli,](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) jak volat uloženou proceduru a nastavit globální nebo relace na úrovni časových pásem.

## <a name="storage-engine-support"></a>Podpora paměťového motoru

### <a name="supported"></a>Podporuje se
- [Innodb](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [Paměti](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nepodporované
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [ČERNÁ DÍRA](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [Archiv](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [Federované](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Podpora oprávnění

### <a name="unsupported"></a>Nepodporované
- Role DBA: Mnoho parametrů a nastavení serveru může neúmyslně snížit výkon serveru nebo negovat vlastnosti ACID systému DBMS. Jako takové zachovat integritu služby a SLA na úrovni produktu, tato služba nezveřejňuje roli DBA. Výchozí uživatelský účet, který je vytvořen při vytvoření nové instance databáze, umožňuje tomuto uživateli provádět většinu příkazů DDL a DML v instanci spravované databáze. 
- Super privilegium: Podobně [super privilegium](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) je také omezeno.
- DEFINER: Vyžaduje super oprávnění k vytvoření a je omezena. Pokud importdat pomocí zálohy, `CREATE DEFINER` odebrat příkazy ručně `--skip-definer` nebo pomocí příkazu při provádění mysqldump.

## <a name="data-manipulation-statement-support"></a>Podpora prohlášení o manipulaci s daty

### <a name="supported"></a>Podporuje se
- `LOAD DATA INFILE`je podporována, `[LOCAL]` ale parametr musí být zadán a směrován na cestu UNC (úložiště Azure připojené prostřednictvím Protokolu SMB).

### <a name="unsupported"></a>Nepodporované
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Funkční omezení

### <a name="scale-operations"></a>Operace v měřítku
- Dynamické škálování na základní cenové úrovně a z nich není aktuálně podporováno.
- Zmenšení velikosti úložiště serveru není podporováno.

### <a name="server-version-upgrades"></a>Upgrady verzí serveru
- Automatická migrace mezi hlavními verzemi databázového stroje není v současné době podporována. Pokud chcete upgradovat na další hlavní verzi, převzít [výpis a obnovit](./concepts-migrate-dump-restore.md) jej na server, který byl vytvořen s novou verzi motoru.

### <a name="point-in-time-restore"></a>Obnovení do bodu v čase
- Při použití funkce PITR je nový server vytvořen se stejnými konfiguracemi jako server, na který je založen.
- Obnovení odstraněného serveru není podporováno.

### <a name="vnet-service-endpoints"></a>Koncové body služeb virtuální sítě
- Podpora koncových bodů služby virtuální sítě je jenom pro servery optimalizované pro obecné účely a paměť.

### <a name="storage-size"></a>Velikost úložiště
- Informace o velikosti úložiště na cenovou úroveň najdete v [části Cenové úrovně.](concepts-pricing-tiers.md)

## <a name="current-known-issues"></a>Aktuální známé problémy
- Instance serveru MySQL zobrazí po navázání připojení nesprávnou verzi serveru. Chcete-li získat správnou verzi modulu instancí serveru, použijte `select version();` příkaz.

## <a name="next-steps"></a>Další kroky
- [Co je k dispozici v jednotlivých úrovních služeb](concepts-pricing-tiers.md)
- [Podporované verze databáze MySQL](concepts-supported-versions.md)
