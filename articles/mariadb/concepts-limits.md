---
title: Omezení ve službě Azure Database pro MariaDB
description: Tento článek popisuje omezení ve službě Azure Database pro MariaDB, jako je třeba počet připojení a možnosti úložiště modul.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 4fb774341b19034d3905341a99be2114a0d1e18b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835705"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Omezení ve službě Azure Database pro MariaDB
Následující části popisují kapacitu, podpora modulu úložiště, oprávnění podpory, podpora příkaz manipulace dat a funkční omezení v databázi služby.

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
|Paměťově optimalizované| 2| 600|
|Paměťově optimalizované| 4| 1250|
|Paměťově optimalizované| 8| 2500|
|Paměťově optimalizované| 16| 5000|

Při připojení překročí limit, může se zobrazit následující chyba:
> Chyba 1040 (08004): Příliš mnoho připojení

## <a name="storage-engine-support"></a>Podpora modulu úložiště

### <a name="supported"></a>Podporováno
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [PAMĚŤ](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nepodporovaný
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [SMĚROVAČE BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/l)
- [ARCHIV](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Podpora oprávnění

### <a name="unsupported"></a>Nepodporovaný
- DBA role: mnoho parametrů serveru a nastavení můžete neúmyslně snížit výkon serveru nebo negate kyseliny vlastnosti správce databáze. V důsledku toho pro zajištění integrity služby a smlouvě SLA na úrovni produktu, tato služba nevystavuje DBA role. Výchozí uživatelský účet, který je vytvořen při vytvoření nové instance databáze, umožňuje provádět většinu příkazů DDL a jazyk DML instance spravované databáze.
- Oprávnění SUPERUŽIVATELE: podobně [SUPER oprávnění](https://mariadb.com/kb/en/library/grant/#global-privileges) je také omezen.

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
- Automatizovaný přenos mezi verzí vyhledávacích strojů hlavní databáze se aktuálně nepodporuje.

### <a name="point-in-time-restore"></a>Obnovení do bodu v čase
- Při použití funkce PITR se vytvoří nový server se stejnou konfiguraci jako server, který je založen na.
- Obnovení odstraněné serveru není podporováno.

### <a name="subscription-management"></a>Správa předplatného
- Dynamicky přesunutí předem vytvořené servery v předplatném a skupině prostředků se momentálně nepodporuje.

## <a name="current-known-issues"></a>Aktuální známé problémy
- Instance serveru MariaDB zobrazuje nesprávná verze po navázání připojení. Chcete-li získat správný server verze modulu instance, použijte `select version();` příkazu.

## <a name="next-steps"></a>Další postup
- [Co je k dispozici na jednotlivých úrovních služby](concepts-pricing-tiers.md)
- [Podporované verze databáze MariaDB](concepts-supported-versions.md)
