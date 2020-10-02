---
title: Omezení – Azure Database for MySQL
description: Tento článek popisuje omezení Azure Database for MySQL, například počet připojení a možnosti modulu úložiště.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 2c70e862364aea549c10c24a9dcc1c424c792993
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91652172"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Omezení Azure Database for MySQL
Následující části popisují kapacitu, podporu modulu úložiště, podporu oprávnění, podporu příkazů pro manipulaci s daty a funkční omezení v databázové službě. Viz také [Obecná omezení](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) platná pro databázový stroj MySQL.

## <a name="server-parameters"></a>Parametry serveru

> [!NOTE]
> Pokud hledáte minimální/maximální hodnoty parametrů serveru `max_connections` , například a `innodb_buffer_pool_size` , tyto informace se přesunuly na článek **[parametry serveru](./concepts-server-parameters.md)** .

Azure Database for MySQL podporuje optimalizaci hodnot parametrů serveru. Minimální a maximální hodnota některých parametrů (např. `max_connections`, `join_buffer_size` , `query_cache_size` ) závisí na cenové úrovni a virtuální jádra serveru. Další informace o těchto omezeních najdete v [parametrech serveru](./concepts-server-parameters.md) .

Při počátečním nasazení zahrnuje server Azure for MySQL systémové tabulky pro informace o časovém pásmu, ale tyto tabulky nejsou naplněny. Tabulky časových pásem lze naplnit voláním `mysql.az_load_timezone` uložené procedury z nástroje, jako je například příkazový řádek MySQL nebo MySQL Workbench. Informace o tom, jak volat uloženou proceduru a nastavit globální časová pásma na úrovni relace, najdete v článcích [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) nebo [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) .

Moduly plug-in pro heslo, jako je například "validate_password" a "caching_sha2_password", nejsou službou podporovány.

## <a name="storage-engines"></a>Moduly úložiště

MySQL podporuje mnoho úložných strojů. Na Azure Database for MySQL flexibilním serveru jsou podporované a nepodporované následující úložiště:

### <a name="supported"></a>Podporováno
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [REZIDENT](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nepodporované
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ZÁLOHOVAT](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDEROVANÉ](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>Oprávnění & podpoře manipulace s daty

Mnoho parametrů serveru a nastavení může nechtěně snížit výkon serveru nebo vlastnosti s nezápornou KYSELINou serveru MySQL. Za účelem zachování integrity služby a smlouvy SLA na úrovni produktu nezveřejňuje tato služba více rolí. 

Služba MySQL nepovoluje přímý přístup k základnímu systému souborů. Některé příkazy pro manipulaci s daty nejsou podporovány. 

### <a name="unsupported"></a>Nepodporované

Následující nejsou podporovány:
- Role DBA: omezeno. Alternativně můžete použít uživatele správce (vytvořený během vytváření nového serveru), což umožňuje provádět většinu příkazů DDL a DML. 
- Oprávnění SUPER: podobně, [oprávnění Super](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) je omezené.
- DEFINe: vyžaduje pro vytvoření a omezení superuživatele oprávnění. Pokud importujete data pomocí zálohy, odeberte `CREATE DEFINER` příkazy ručně nebo pomocí `--skip-definer` příkazu při provádění mysqldump.
- Systémové databáze: [Systémová databáze MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) je určena jen pro čtení a používá se k podpoře různých funkcí PaaS. Nelze provádět změny `mysql` systémové databáze.
- `SELECT ... INTO OUTFILE`: Nepodporováno ve službě.

### <a name="supported"></a>Podporováno
- `LOAD DATA INFILE` je podporováno, ale `[LOCAL]` musí být zadán parametr a směrován na cestu UNC (úložiště Azure připojené prostřednictvím protokolu SMB).

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
