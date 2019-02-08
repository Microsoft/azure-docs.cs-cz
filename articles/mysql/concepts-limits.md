---
title: Omezení ve službě Azure Database for MySQL
description: Tento článek popisuje omezení ve službě Azure Database for MySQL, jako je třeba počet připojení a možnosti úložiště modul.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/6/2018
ms.openlocfilehash: 55106f855d1f2cab82b751b306a3a289bd740e9e
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895342"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Omezení ve službě Azure Database for MySQL
Následující části popisují kapacitu, podpora modulu úložiště, oprávnění podpory, podpora příkaz manipulace dat a funkční omezení v databázi služby. Viz také [obecná omezení](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) pro databázového stroje MySQL.

## <a name="maximum-connections"></a>Maximální počet připojení
Maximální počet připojení na cenová úroveň a virtuálními jádry jsou následující: 

|**Cenová úroveň**|**počet virtuálních jader:**| **Maximální počet připojení**|
|---|---|---|
|Basic| 1| 50|
|Basic| 2| 100|
|Obecné použití| 2| 300|
|Obecné použití| 4| 625|
|Obecné použití| 8| 1250|
|Obecné použití| 16| 2500|
|Obecné použití| 32| 5000|
|Obecné použití| 64| 10000|
|Paměťově optimalizované| 2| 600|
|Paměťově optimalizované| 4| 1250|
|Paměťově optimalizované| 8| 2500|
|Paměťově optimalizované| 16| 5000|
|Paměťově optimalizované| 32| 10000|

Při připojení překročí limit, může se zobrazit následující chyba:
> ERROR 1040 (08004): Příliš mnoho připojení

## <a name="storage-engine-support"></a>Podpora modulu úložiště

### <a name="supported"></a>Podporováno
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [PAMĚŤ](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nepodporovaný
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [SMĚROVAČE BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIV](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDEROVANÉ](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Podpora oprávnění

### <a name="unsupported"></a>Nepodporovaný
- DBA role: Mnoho parametrů serveru a nastavení můžete neúmyslně snížit výkon serveru nebo negate kyseliny vlastnosti správce databáze. V důsledku toho pro zajištění integrity služby a smlouvě SLA na úrovni produktu, tato služba nevystavuje DBA role. Výchozí uživatelský účet, který je vytvořen při vytvoření nové instance databáze, umožňuje provádět většinu příkazů DDL a jazyk DML instance spravované databáze. 
- SUPER oprávnění: Podobně [SUPER oprávnění](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) je také omezen.
- DEFINER: Vyžaduje super oprávnění k vytváření a je omezený. Pokud importujete data pomocí zálohy, odeberte `CREATE DEFINER` ručně nebo pomocí příkazů `--skip-definer` příkaz při provádění mysqldump.

## <a name="data-manipulation-statement-support"></a>Podpora příkaz manipulace dat

### <a name="supported"></a>Podporováno
- `LOAD DATA INFILE` je podporováno, ale `[LOCAL]` parametr musí být zadán a směrované na cestu UNC (úložiště Azure připojit přes protokol SMB).

### <a name="unsupported"></a>Nepodporovaný
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Funkční omezení

### <a name="scale-operations"></a>Operace škálování
- Dynamické škálování do a ze základní cenové úrovně se aktuálně nepodporuje.
- Snížení velikosti úložiště serveru se nepodporuje.

### <a name="server-version-upgrades"></a>Upgrady verze serveru
- Automatizovaný přenos mezi verzí vyhledávacích strojů hlavní databáze se aktuálně nepodporuje. Pokud chcete upgradovat na novou hlavní verzi, [výpisu a obnovení](./concepts-migrate-dump-restore.md) ho na server, který byl vytvořen v nové verzi modulu.

### <a name="point-in-time-restore"></a>Obnovení do bodu v čase
- Při použití funkce PITR se vytvoří nový server se stejnou konfiguraci jako server, který je založen na.
- Obnovení odstraněné serveru není podporováno.

### <a name="vnet-service-endpoints"></a>Koncové body služby virtuální sítě
- Podpora pro koncové body služby virtuální sítě je pouze pro servery pro obecné účely a optimalizovaný pro paměť.

## <a name="current-known-issues"></a>Aktuální známé problémy
- Instanci MySQL serveru zobrazuje verze serveru měla špatný po navázání připojení. Chcete-li získat správný server verze modulu instance, použijte `select version();` příkazu.

## <a name="next-steps"></a>Další postup
- [Co je k dispozici na jednotlivých úrovních služby](concepts-pricing-tiers.md)
- [Podporované verze databáze MySQL](concepts-supported-versions.md)
