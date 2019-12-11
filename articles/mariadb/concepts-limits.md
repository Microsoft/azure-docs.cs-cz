---
title: Omezení – Azure Database for MariaDB
description: Tento článek popisuje omezení Azure Database for MariaDB, například počet připojení a možnosti modulu úložiště.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 1f5824f349650e340e395221785266096da16d6f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969543"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Omezení Azure Database for MariaDB
Následující části popisují kapacitu, podpora modulu úložiště, oprávnění podpory, podpora příkaz manipulace dat a funkční omezení v databázi služby.

## <a name="maximum-connections"></a>Maximální počet připojení
Maximální počet připojení na cenová úroveň a virtuálními jádry jsou následující:

|**Cenová úroveň**|**počet virtuálních jader:**| **Maximální počet připojení**|
|---|---|---|
|Úroveň Basic| 1\. místo| 50|
|Úroveň Basic| 2| 100|
|Obecné použití| 2| 600|
|Obecné použití| 4| 1250|
|Obecné použití| 8| 2500|
|Obecné použití| 16| 5000|
|Obecné použití| 32| 10000|
|Obecné použití| 64| 20000|
|Paměťově optimalizované| 2| 800|
|Paměťově optimalizované| 4| 2500|
|Paměťově optimalizované| 8| 5000|
|Paměťově optimalizované| 16| 10000|
|Paměťově optimalizované| 32| 20000|

Při připojení překročí limit, může se zobrazit následující chyba:
> Chyba 1040 (08004): Příliš mnoho připojení

## <a name="storage-engine-support"></a>Podpora modulu úložiště

### <a name="supported"></a>Podporováno
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [PAMĚŤ](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nepodporované
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [SMĚROVAČE BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ARCHIV](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Podpora oprávnění

### <a name="unsupported"></a>Nepodporované
- DBA role: mnoho parametrů serveru a nastavení můžete neúmyslně snížit výkon serveru nebo negate kyseliny vlastnosti správce databáze. V důsledku toho pro zajištění integrity služby a smlouvě SLA na úrovni produktu, tato služba nevystavuje DBA role. Výchozí uživatelský účet, který je vytvořen při vytvoření nové instance databáze, umožňuje provádět většinu příkazů DDL a jazyk DML instance spravované databáze.
- Oprávnění SUPERUŽIVATELE: podobně [SUPER oprávnění](https://mariadb.com/kb/en/library/grant/#global-privileges) je také omezen.
- DEFINe: vyžaduje pro vytvoření a omezení superuživatele oprávnění. Pokud importujete data pomocí zálohy, odeberte příkazy `CREATE DEFINER` ručně nebo pomocí příkazu `--skip-definer` při provádění mysqldump.

## <a name="data-manipulation-statement-support"></a>Podpora příkaz manipulace dat

### <a name="supported"></a>Podporováno
- `LOAD DATA INFILE` je podporováno, ale `[LOCAL]` parametr musí být zadán a směrované na cestu UNC (úložiště Azure připojit přes protokol SMB).

### <a name="unsupported"></a>Nepodporované
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
- Dynamicky se přesouvá předem vytvořené servery v rámci předplatného a skupiny prostředků v současné době se nepodporují.

### <a name="vnet-service-endpoints"></a>Koncové body služby virtuální sítě
- Podpora pro koncové body služby virtuální sítě je pouze pro servery pro obecné účely a optimalizovaný pro paměť.

### <a name="storage-size"></a>Velikost úložiště
- Omezení velikosti úložiště na cenové úrovni najdete v [cenové úrovni](concepts-pricing-tiers.md) .

## <a name="current-known-issues"></a>Aktuální známé problémy
- Instance serveru MariaDB po navázání připojení zobrazí nesprávnou verzi serveru. Chcete-li získat správný server verze modulu instance, použijte `select version();` příkazu.

## <a name="next-steps"></a>Další kroky
- [Co je k dispozici na jednotlivých úrovních služby](concepts-pricing-tiers.md)
- [Podporované verze databáze MariaDB](concepts-supported-versions.md)
