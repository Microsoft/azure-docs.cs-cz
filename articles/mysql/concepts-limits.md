---
title: Omezení – Azure Database for MySQL
description: Tento článek popisuje omezení ve službě Azure Database for MySQL, jako je třeba počet připojení a možnosti úložiště modul.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 6954f306e0d0a346bd8f39776d987af99f7574dd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299086"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Omezení ve službě Azure Database for MySQL
Následující části popisují kapacitu, podpora modulu úložiště, oprávnění podpory, podpora příkaz manipulace dat a funkční omezení v databázi služby. Viz také [Obecná omezení](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) platná pro databázový stroj MySQL.

## <a name="server-parameters"></a>Parametry serveru

Minimální a maximální hodnoty několika oblíbených parametrů serveru se určují v cenové úrovni a v virtuální jádra. Omezení najdete v následujících tabulkách.

### <a name="max_connections"></a>max_connections

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota**|**Minimální hodnota**|**Maximální hodnota**|
|---|---|---|---|---|
|Basic|1|50|10|50|
|Basic|2|100|10|100|
|Obecné použití|2|300|10|600|
|Obecné použití|4|625|10|1250|
|Obecné použití|8|1250|10|2500|
|Obecné použití|16|2500|10|5000|
|Obecné použití|32|5000|10|10000|
|Obecné použití|64|10000|10|20000|
|Paměťově optimalizované|2|600|10|800|
|Paměťově optimalizované|4|1250|10|2500|
|Paměťově optimalizované|8|2500|10|5000|
|Paměťově optimalizované|16|5000|10|10000|
|Paměťově optimalizované|32|10000|10|20000|

Při připojení překročí limit, může se zobrazit následující chyba:
> Chyba 1040 (08004): Příliš mnoho připojení

> [!IMPORTANT]
> Pro dosažení optimálního prostředí doporučujeme, abyste k efektivní správě připojení používali připojení Pooler jako ProxySQL.

Vytváření nových připojení klientů k MySQL trvá čas i po jejich navázání, tato připojení zabírají databázové prostředky i v případě nečinnosti. Většina aplikací vyžaduje mnoho krátkodobých připojení, což je v této situaci celé. Výsledkem je méně prostředků dostupných pro vaše skutečné zatížení, což vede ke snížení výkonu. Připojení Pooler, které zkracuje nečinné připojení a znovu používá existující připojení, jim pomůže vyhnout se. Další informace o nastavení ProxySQL najdete v našem [blogovém příspěvku](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

## <a name="query_cache_size"></a>query_cache_size

Mezipaměť dotazů je ve výchozím nastavení vypnutá. Pokud chcete povolit mezipaměť dotazů, nakonfigurujte parametr `query_cache_type`. 

Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) .

> [!NOTE]
> Mezipaměť dotazů je zastaralá od MySQL 5.7.20 a byla odebrána v MySQL 8,0

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota**|**Minimální hodnota**|**Maximální hodnota**|
|---|---|---|---|---|
|Basic|1|Nekonfigurovatelné na úrovni Basic|Není k dispozici|Není k dispozici|
|Basic|2|Nekonfigurovatelné na úrovni Basic|Není k dispozici|Není k dispozici|
|Obecné použití|2|0|0|16777216|
|Obecné použití|4|0|0|33554432|
|Obecné použití|8|0|0|67108864|
|Obecné použití|16|0|0|134217728|
|Obecné použití|32|0|0|134217728|
|Obecné použití|64|0|0|134217728|
|Paměťově optimalizované|2|0|0|33554432|
|Paměťově optimalizované|4|0|0|67108864|
|Paměťově optimalizované|8|0|0|134217728|
|Paměťově optimalizované|16|0|0|134217728|
|Paměťově optimalizované|32|0|0|134217728|

## <a name="sort_buffer_size"></a>sort_buffer_size

Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) .

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota**|**Minimální hodnota**|**Maximální hodnota**|
|---|---|---|---|---|
|Basic|1|Nekonfigurovatelné na úrovni Basic|Není k dispozici|Není k dispozici|
|Basic|2|Nekonfigurovatelné na úrovni Basic|Není k dispozici|Není k dispozici|
|Obecné použití|2|524288|32768|4194304|
|Obecné použití|4|524288|32768|8388608|
|Obecné použití|8|524288|32768|16777216|
|Obecné použití|16|524288|32768|33554432|
|Obecné použití|32|524288|32768|33554432|
|Obecné použití|64|524288|32768|33554432|
|Paměťově optimalizované|2|524288|32768|8388608|
|Paměťově optimalizované|4|524288|32768|16777216|
|Paměťově optimalizované|8|524288|32768|33554432|
|Paměťově optimalizované|16|524288|32768|33554432|
|Paměťově optimalizované|32|524288|32768|33554432|

## <a name="join_buffer_size"></a>join_buffer_size

Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) .

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota**|**Minimální hodnota**|**Maximální hodnota**|
|---|---|---|---|---|
|Basic|1|Nekonfigurovatelné na úrovni Basic|Není k dispozici|Není k dispozici|
|Basic|2|Nekonfigurovatelné na úrovni Basic|Není k dispozici|Není k dispozici|
|Obecné použití|2|262144|128|268435455|
|Obecné použití|4|262144|128|536870912|
|Obecné použití|8|262144|128|1073741824|
|Obecné použití|16|262144|128|2147483648|
|Obecné použití|32|262144|128|4294967295|
|Obecné použití|64|262144|128|4294967295|
|Paměťově optimalizované|2|262144|128|536870912|
|Paměťově optimalizované|4|262144|128|1073741824|
|Paměťově optimalizované|8|262144|128|2147483648|
|Paměťově optimalizované|16|262144|128|4294967295|
|Paměťově optimalizované|32|262144|128|4294967295|

## <a name="max_heap_table_size"></a>max_heap_table_size

Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) .

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota**|**Minimální hodnota**|**Maximální hodnota**|
|---|---|---|---|---|
|Basic|1|Nekonfigurovatelné na úrovni Basic|Není k dispozici|Není k dispozici|
|Basic|2|Nekonfigurovatelné na úrovni Basic|Není k dispozici|Není k dispozici|
|Obecné použití|2|16777216|16384|268435455|
|Obecné použití|4|16777216|16384|536870912|
|Obecné použití|8|16777216|16384|1073741824|
|Obecné použití|16|16777216|16384|2147483648|
|Obecné použití|32|16777216|16384|4294967295|
|Obecné použití|64|16777216|16384|4294967295|
|Paměťově optimalizované|2|16777216|16384|536870912|
|Paměťově optimalizované|4|16777216|16384|1073741824|
|Paměťově optimalizované|8|16777216|16384|2147483648|
|Paměťově optimalizované|16|16777216|16384|4294967295|
|Paměťově optimalizované|32|16777216|16384|4294967295|

## <a name="tmp_table_size"></a>tmp_table_size

Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) .

|**Cenová úroveň**|**vCore (celkem)**|**Výchozí hodnota**|**Minimální hodnota**|**Maximální hodnota**|
|---|---|---|---|---|
|Basic|1|Nekonfigurovatelné na úrovni Basic|Není k dispozici|Není k dispozici|
|Basic|2|Nekonfigurovatelné na úrovni Basic|Není k dispozici|Není k dispozici|
|Obecné použití|2|16777216|1024|67108864|
|Obecné použití|4|16777216|1024|134217728|
|Obecné použití|8|16777216|1024|268435456|
|Obecné použití|16|16777216|1024|536870912|
|Obecné použití|32|16777216|1024|1073741824|
|Obecné použití|64|16777216|1024|1073741824|
|Paměťově optimalizované|2|16777216|1024|134217728|
|Paměťově optimalizované|4|16777216|1024|268435456|
|Paměťově optimalizované|8|16777216|1024|536870912|
|Paměťově optimalizované|16|16777216|1024|1073741824|
|Paměťově optimalizované|32|16777216|1024|1073741824|

## <a name="storage-engine-support"></a>Podpora modulu úložiště

### <a name="supported"></a>Podporováno
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [REZIDENT](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nepodporovaný
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ZÁLOHOVAT](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDEROVANÉ](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Podpora oprávnění

### <a name="unsupported"></a>Nepodporovaný
- DBA role: mnoho parametrů serveru a nastavení můžete neúmyslně snížit výkon serveru nebo negate kyseliny vlastnosti správce databáze. V důsledku toho pro zajištění integrity služby a smlouvě SLA na úrovni produktu, tato služba nevystavuje DBA role. Výchozí uživatelský účet, který je vytvořen při vytvoření nové instance databáze, umožňuje provádět většinu příkazů DDL a jazyk DML instance spravované databáze. 
- Superuživatele (SUPER Privileged Privilege) je taky omezené [oprávnění Super](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) .
- DEFINe: vyžaduje pro vytvoření a omezení superuživatele oprávnění. Pokud importujete data pomocí zálohy, odeberte příkazy `CREATE DEFINER` ručně nebo pomocí příkazu `--skip-definer` při provádění mysqldump.

## <a name="data-manipulation-statement-support"></a>Podpora příkaz manipulace dat

### <a name="supported"></a>Podporováno
- `LOAD DATA INFILE` se podporuje, ale je potřeba zadat parametr `[LOCAL]` a směrovat ho na cestu UNC (úložiště Azure připojené prostřednictvím protokolu SMB).

### <a name="unsupported"></a>Nepodporovaný
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Funkční omezení

### <a name="scale-operations"></a>Operace škálování
- Dynamické škálování do a ze základní cenové úrovně se aktuálně nepodporuje.
- Snížení velikosti úložiště serveru se nepodporuje.

### <a name="server-version-upgrades"></a>Upgrady verze serveru
- Automatizovaný přenos mezi verzí vyhledávacích strojů hlavní databáze se aktuálně nepodporuje. Pokud chcete upgradovat na další hlavní verzi, [vystavte výpis a obnovte](./concepts-migrate-dump-restore.md) ho na serveru, který byl vytvořen s novou verzí modulu.

### <a name="point-in-time-restore"></a>Obnovení do bodu v čase
- Při použití funkce PITR se vytvoří nový server se stejnou konfiguraci jako server, který je založen na.
- Obnovení odstraněné serveru není podporováno.

### <a name="vnet-service-endpoints"></a>Koncové body služby virtuální sítě
- Podpora pro koncové body služby virtuální sítě je pouze pro servery pro obecné účely a optimalizovaný pro paměť.

### <a name="storage-size"></a>Velikost úložiště
- Omezení velikosti úložiště na cenové úrovni najdete v [cenové úrovni](concepts-pricing-tiers.md) .

## <a name="current-known-issues"></a>Aktuální známé problémy
- Instanci MySQL serveru zobrazuje verze serveru měla špatný po navázání připojení. Správnou verzi modulu instance serveru získáte pomocí příkazu `select version();`.

## <a name="next-steps"></a>Další kroky
- [Co je dostupné v jednotlivých úrovních služby](concepts-pricing-tiers.md)
- [Podporované verze databáze MySQL](concepts-supported-versions.md)
