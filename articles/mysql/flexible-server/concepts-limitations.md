---
title: Omezení – Azure Database for MySQL – flexibilní Server
description: Tento článek popisuje omezení Azure Database for MySQLho flexibilního serveru, jako je třeba počet připojení a možnosti modulu úložiště.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: a3abde9092519be057dcd73ec63318f970fd5c74
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543081"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server-preview"></a>Omezení Azure Database for MySQL – flexibilní Server (Preview)

> [!IMPORTANT] 
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Tento článek popisuje omezení Azure Database for MySQL flexibilní serverovou službou. K dispozici jsou také [Obecná omezení](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html) v databázovém stroji MySQL. Pokud se chcete dozvědět víc o úrovních prostředků (výpočetních, paměť, úložiště), přečtěte si článek [výpočetní výkon a úložiště](concepts-compute-storage.md) .

## <a name="server-parameters"></a>Parametry serveru

> [!NOTE]
> Pokud hledáte minimální/maximální hodnoty parametrů serveru `max_connections` , například a `innodb_buffer_pool_size` , tyto informace se přesunuly na článek parametry serveru koncepty [serveru](./concepts-server-parameters.md) .

Azure Database for MySQL podporuje optimalizaci hodnot parametrů serveru. Minimální a maximální hodnota některých parametrů (např. `max_connections`, `join_buffer_size` , `query_cache_size` ) závisí na výpočetní úrovni a výpočetní velikosti serveru. Další informace o těchto omezeních najdete v [parametrech serveru](./concepts-server-parameters.md) .

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

### <a name="zone-redundant-ha"></a>Redundantní HA zóny
- Tato konfigurace se dá nastavit jenom během vytváření serveru.
- Nepodporováno v výpočetní úrovni, která je k dispozici.

### <a name="networking"></a>Sítě
- Metodu připojení nelze po vytvoření serveru změnit. Pokud je server vytvořený pomocí *privátního přístupu (Integration VNET)* , nedá se po vytvoření změnit na *veřejný přístup (povolené IP adresy)* a naopak.
- Protokol TLS/SSL je ve výchozím nastavení povolený a nedá se zakázat.
- Minimální verze protokolu TLS podporovaná na serveru je TLS 1.2. Další informace najdete [v tématu připojení pomocí protokolu TLS/SSL](./how-to-connect-tls-ssl.md) .

### <a name="stopstart-operation"></a>Operace zastavení/spuštění
- Není podporováno u redundantních konfigurací HA zóny (primární i pohotovostní).
- Nepodporováno s konfigurací repliky pro čtení (zdroj a repliky).

### <a name="scale-operations"></a>Operace škálování
- Snížení zřízené úložiště serveru se nepodporuje.

### <a name="read-replicas"></a>Čtení replik
- Není podporováno u redundantních konfigurací HA zóny (primární i pohotovostní).

### <a name="server-version-upgrades"></a>Upgrady verze serveru
- Automatizovaná migrace mezi hlavními verzemi databázového stroje není podporována. Pokud chcete upgradovat hlavní verzi, [vystavte výpis a obnovte](../concepts-migrate-dump-restore.md) ho na serveru, který byl vytvořen s novou verzí modulu.

### <a name="restoring-a-server"></a>Obnovení serveru
- Při obnovení k bodu v čase se vytvoří nové servery se stejnou konfigurací výpočtů a úložiště jako na zdrojovém serveru, na kterém je založena. Po vytvoření serveru se dá nově obnovený výpočetní Server škálovat.
- Obnovení odstraněného serveru se nepodporuje.

## <a name="next-steps"></a>Další kroky

- Seznamte [se s tím, co je k dispozici pro výpočetní a úložné možnosti](concepts-compute-storage.md)
- Další informace o [podporovaných verzích MySQL](concepts-supported-versions.md)
- Přečtěte si, [Jak zálohovat a obnovit server pomocí Azure Portal](how-to-restore-server-portal.md)