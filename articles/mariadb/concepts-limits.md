---
title: Omezení – Azure Database for MariaDB
description: Tento článek popisuje omezení Azure Database for MariaDB, například počet připojení a možnosti modulu úložiště.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/2/2020
ms.openlocfilehash: d4546732f067988c9d7dd2d11c718a15fbe32d23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664295"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Omezení Azure Database for MariaDB
Následující části popisují kapacitu, podporu modulu úložiště, podporu oprávnění, podporu příkazů pro manipulaci s daty a funkční omezení v databázové službě.

## <a name="server-parameters"></a>Parametry serveru

> [!NOTE]
> Pokud hledáte minimální/maximální hodnoty parametrů serveru `max_connections` , například a `innodb_buffer_pool_size` , tyto informace se přesunuly na článek **[parametry serveru](./concepts-server-parameters.md)** .

Azure Database for MariaDB podporuje optimalizaci hodnot parametrů serveru. Minimální a maximální hodnota některých parametrů (např. `max_connections`, `join_buffer_size` , `query_cache_size` ) závisí na cenové úrovni a virtuální jádra serveru. Další informace o těchto omezeních najdete v [parametrech serveru](./concepts-server-parameters.md) .

Při počátečním nasazení zahrnuje server Azure pro MariaDB systémové tabulky pro informace o časovém pásmu, ale tyto tabulky se neplní. Tabulky časových pásem lze naplnit voláním `mysql.az_load_timezone` uložené procedury z nástroje, jako je například příkazový řádek MySQL nebo MySQL Workbench. Informace o tom, jak volat uloženou proceduru a nastavit globální časová pásma na úrovni relace, najdete v článcích [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) nebo [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) .

Moduly plug-in pro heslo, jako je například "validate_password" a "caching_sha2_password", nejsou službou podporovány.

## <a name="storage-engine-support"></a>Podpora modulu úložiště

### <a name="supported"></a>Podporováno
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [REZIDENT](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nepodporované
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ZÁLOHOVAT](https://mariadb.com/kb/en/library/archive/)

## <a name="privileges--data-manipulation-support"></a>Oprávnění & podpoře manipulace s daty

Mnoho parametrů serveru a nastavení může nechtěně snížit výkon serveru nebo vlastnosti s nezápornou KYSELINou MariaDB serveru. Za účelem zachování integrity služby a smlouvy SLA na úrovni produktu nezveřejňuje tato služba více rolí. 

Služba MariaDB nepovoluje přímý přístup k základnímu systému souborů. Některé příkazy pro manipulaci s daty nejsou podporovány. 

## <a name="privilege-support"></a>Podpora oprávnění

### <a name="unsupported"></a>Nepodporované

Následující nejsou podporovány:
- Role DBA: omezeno. Alternativně můžete použít uživatele správce (vytvořený během vytváření nového serveru), což umožňuje provádět většinu příkazů DDL a DML. 
- Oprávnění SUPER: podobně, má také omezení [Super oprávnění](https://mariadb.com/kb/en/library/grant/#global-privileges) .
- DEFINe: vyžaduje pro vytvoření a omezení superuživatele oprávnění. Pokud importujete data pomocí zálohy, odeberte `CREATE DEFINER` příkazy ručně nebo pomocí `--skip-definer` příkazu při provádění mysqldump.
- Systémové databáze: [Systémová databáze MySQL](https://mariadb.com/kb/en/the-mysql-database-tables/) je určena jen pro čtení a používá se k podpoře různých funkcí PaaS. Nelze provádět změny `mysql` systémové databáze.
- `SELECT ... INTO OUTFILE`: Nepodporováno ve službě.

### <a name="supported"></a>Podporováno
- `LOAD DATA INFILE` je podporováno, ale `[LOCAL]` musí být zadán parametr a směrován na cestu UNC (úložiště Azure připojené prostřednictvím protokolu SMB).

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
