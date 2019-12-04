---
title: Omezení – Azure Database for MariaDB
description: Tento článek popisuje omezení Azure Database for MariaDB, například počet připojení a možnosti modulu úložiště.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: fc89b6233602c81ea622a528c223adf2003f0f68
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772492"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Omezení Azure Database for MariaDB
Následující části popisují kapacitu, podporu modulu úložiště, podporu oprávnění, podporu příkazů pro manipulaci s daty a funkční omezení v databázové službě.

## <a name="maximum-connections"></a>Maximální počet připojení
Maximální počet připojení na cenové úrovni a virtuální jádra je následující:

|**Cenová úroveň**|**vCore (celkem)**| **Maximální počet připojení**|
|---|---|---|
|Úroveň Basic| 1\. místo| 50|
|Úroveň Basic| 2| 100|
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

Když připojení překročí limit, může se zobrazit následující chyba:
> Chyba 1040 (08004): příliš mnoho připojení

## <a name="storage-engine-support"></a>Podpora modulu úložiště

### <a name="supported"></a>Podporováno
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [REZIDENT](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nepodporované
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ZÁLOHOVAT](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Podpora oprávnění

### <a name="unsupported"></a>Nepodporované
- Role DBA: mnoho parametrů serveru a nastavení může nechtěně snížit výkon serveru nebo vlastnosti s nezápornou KYSELINou systému DBMS. Za účelem zachování integrity služby a smlouvy SLA na úrovni produktu Tato služba nevystavuje roli DBA. Výchozí uživatelský účet, který je vytvořen při vytvoření nové instance databáze, umožňuje tomuto uživateli provádět většinu příkazů DDL a DML v instanci spravované databáze.
- Superuživatele (SUPER Privileged Privilege) je taky omezené [oprávnění Super](https://mariadb.com/kb/en/library/grant/#global-privileges) .
- DEFINe: vyžaduje pro vytvoření a omezení superuživatele oprávnění. Pokud importujete data pomocí zálohy, odeberte příkazy `CREATE DEFINER` ručně nebo pomocí příkazu `--skip-definer` při provádění mysqldump.

## <a name="data-manipulation-statement-support"></a>Podpora příkazů manipulace s daty

### <a name="supported"></a>Podporováno
- `LOAD DATA INFILE` se podporuje, ale je potřeba zadat parametr `[LOCAL]` a směrovat ho na cestu UNC (úložiště Azure připojené prostřednictvím protokolu SMB).

### <a name="unsupported"></a>Nepodporované
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Funkční omezení

### <a name="scale-operations"></a>Operace škálování
- Dynamické škálování na cenové úrovně Basic a z se v tuto chvíli nepodporuje.
- Zmenšení velikosti úložiště serveru se nepodporuje.

### <a name="server-version-upgrades"></a>Upgrady verze serveru
- Automatizovaná migrace mezi hlavními verzemi databázového stroje není v současnosti podporovaná.

### <a name="point-in-time-restore"></a>Obnovení do bodu v čase
- Při použití funkce PITR je nový server vytvořen se stejnými konfiguracemi jako server, na kterém je založena.
- Obnovení odstraněného serveru se nepodporuje.

### <a name="subscription-management"></a>Správa předplatného
- Dynamicky se přesouvá předem vytvořené servery v rámci předplatného a skupiny prostředků v současné době se nepodporují.

### <a name="vnet-service-endpoints"></a>Koncové body služby virtuální sítě
- Podpora koncových bodů služby virtuální sítě je určená jenom pro Pro obecné účely a paměťově optimalizované servery.

### <a name="storage-size"></a>Velikost úložiště
- Omezení velikosti úložiště na cenové úrovni najdete v [cenové úrovni](concepts-pricing-tiers.md) .

## <a name="current-known-issues"></a>Aktuální známé problémy
- Instance serveru MariaDB po navázání připojení zobrazí nesprávnou verzi serveru. Správnou verzi modulu instance serveru získáte pomocí příkazu `select version();`.

## <a name="next-steps"></a>Další kroky
- [Co je dostupné v jednotlivých úrovních služby](concepts-pricing-tiers.md)
- [Podporované verze databáze MariaDB](concepts-supported-versions.md)
