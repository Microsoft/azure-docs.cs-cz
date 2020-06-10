---
title: Omezení – Azure Database for MariaDB
description: Tento článek popisuje omezení Azure Database for MariaDB, například počet připojení a možnosti modulu úložiště.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: 11b28acfbda8b2760f19aa130373ba0f24f94db2
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636593"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Omezení Azure Database for MariaDB
Následující části popisují kapacitu, podporu modulu úložiště, podporu oprávnění, podporu příkazů pro manipulaci s daty a funkční omezení v databázové službě.

## <a name="storage-engine-support"></a>Podpora modulu úložiště

### <a name="supported"></a>Podporuje se
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
- DEFINe: vyžaduje pro vytvoření a omezení superuživatele oprávnění. Pokud importujete data pomocí zálohy, odeberte `CREATE DEFINER` příkazy ručně nebo pomocí `--skip-definer` příkazu při provádění mysqldump.

## <a name="server-parameters"></a>Parametry serveru
Azure Database for MariaDB podporuje optimalizaci hodnot parametrů serveru. Minimální a maximální hodnota některých parametrů závisí na cenové úrovni a na virtuální jádra serveru. Další informace o těchto omezeních najdete v [parametrech serveru](./concepts-server-parameters.md) . 

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
- Automatizovaná migrace mezi hlavními verzemi databázového stroje není v současnosti podporovaná.

### <a name="point-in-time-restore"></a>Obnovení do bodu v čase
- Při použití funkce PITR je nový server vytvořen se stejnými konfiguracemi jako server, na kterém je založena.
- Obnovení odstraněného serveru se nepodporuje.

### <a name="subscription-management"></a>Správa předplatného
- Dynamicky se přesouvá předem vytvořené servery v rámci předplatného a skupiny prostředků v současné době se nepodporují.

### <a name="vnet-service-endpoints"></a>Koncové body služeb virtuální sítě
- Podpora koncových bodů služby virtuální sítě je určená jenom pro Pro obecné účely a paměťově optimalizované servery.

### <a name="storage-size"></a>Velikost úložiště
- Omezení velikosti úložiště na cenové úrovni najdete v [cenové úrovni](concepts-pricing-tiers.md) .

## <a name="current-known-issues"></a>Aktuální známé problémy
- Instance serveru MariaDB po navázání připojení zobrazí nesprávnou verzi serveru. Správnou verzi modulu instance serveru získáte pomocí `select version();` příkazu.

## <a name="next-steps"></a>Další kroky
- [Co je dostupné v jednotlivých úrovních služby](concepts-pricing-tiers.md)
- [Podporované verze databáze MariaDB](concepts-supported-versions.md)
