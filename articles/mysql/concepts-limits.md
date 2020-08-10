---
title: Omezení – Azure Database for MySQL
description: Tento článek popisuje omezení Azure Database for MySQL, například počet připojení a možnosti modulu úložiště.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: 24a214d63fd01fc4353be6563d18f9e28b820c6f
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036517"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Omezení Azure Database for MySQL
Následující části popisují kapacitu, podporu modulu úložiště, podporu oprávnění, podporu příkazů pro manipulaci s daty a funkční omezení v databázové službě. Viz také [Obecná omezení](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) platná pro databázový stroj MySQL.

## <a name="server-parameters"></a>Parametry serveru

> [!NOTE]
> Pokud hledáte minimální/maximální hodnoty parametrů serveru `max_connections` , například a `innodb_buffer_pool_size` , tyto informace se přesunuly na článek **[parametry serveru](./concepts-server-parameters.md)** .

Azure Database for MySQL podporuje optimalizaci hodnot parametrů serveru. Minimální a maximální hodnota některých parametrů (např. `max_connections`, `join_buffer_size` , `query_cache_size` ) závisí na cenové úrovni a virtuální jádra serveru. Další informace o těchto omezeních najdete v [parametrech serveru](./concepts-server-parameters.md) .

Při počátečním nasazení zahrnuje server Azure for MySQL systémové tabulky pro informace o časovém pásmu, ale tyto tabulky nejsou naplněny. Tabulky časových pásem lze naplnit voláním `mysql.az_load_timezone` uložené procedury z nástroje, jako je například příkazový řádek MySQL nebo MySQL Workbench. Informace o tom, jak volat uloženou proceduru a nastavit globální časová pásma na úrovni relace, najdete v článcích [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) nebo [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) .

## <a name="storage-engine-support"></a>Podpora modulu úložiště

### <a name="supported"></a>Podporováno
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
- Systémové databáze: v Azure Database for MySQL je [databáze systému MySQL](https://dev.mysql.com/doc/refman/8.0/en/system-schema.html) určena jen pro čtení, protože slouží k podpoře různých funkcí služby PaaS. Všimněte si, že nemůžete změnit žádnou z `mysql` systémových databází.

## <a name="data-manipulation-statement-support"></a>Podpora příkazů manipulace s daty

### <a name="supported"></a>Podporováno
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
