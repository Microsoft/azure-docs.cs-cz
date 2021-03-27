---
title: Migrace pomocí výpisu a obnovení Azure Database for MariaDB
description: Tento článek popisuje dva běžné způsoby, jak zálohovat a obnovovat databáze ve službě Azure Database for MariaDB pomocí nástrojů, jako je mysqldump, MySQL Workbench a phpMyAdmin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 35b1e84bdf74afd9577c7c98f023179dd769cd66
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628216"
---
# <a name="migrate-your-mariadb-database-to-an-azure-database-for-mariadb-by-using-dump-and-restore"></a>Migrace databáze MariaDB do Azure Database for MariaDB pomocí výpisu a obnovení

Tento článek popisuje dva běžné způsoby zálohování a obnovení databází ve službě Azure Database for MariaDB:
- Výpis a obnovení pomocí nástroje příkazového řádku (pomocí mysqldump) 
- Výpis a obnovení pomocí phpMyAdmin

## <a name="prerequisites"></a>Požadavky
Než začnete s migrací databáze, udělejte toto:
- Vytvořte [Azure Portal serveru Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md).
- Nainstalujte nástroj příkazového řádku [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) .
- Stáhněte a nainstalujte [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) nebo jiný nástroj MySQL třetí strany pro spuštění příkazů výpisu a obnovení.

## <a name="use-common-tools"></a>Použití běžných nástrojů
Pomocí běžných nástrojů a nástrojů, jako je MySQL Workbench nebo mysqldump, můžete vzdáleně připojit a obnovit data do své služby Azure Database for MariaDB. Pomocí těchto nástrojů na klientském počítači připojte k Internetu připojení ke službě Azure Database for MariaDB. Použijte připojení šifrované protokolem SSL jako nejlepší bezpečnostní praxi. Další informace najdete v tématu [Konfigurace připojení SSL v Azure Database for MariaDB](concepts-ssl-connection-security.md). Při migraci dat do služby Azure Database for MariaDB není nutné přesouvat soubory s výpisem paměti do jakéhokoli zvláštního umístění v cloudu. 

## <a name="common-uses-for-dump-and-restore"></a>Běžné použití pro výpis a obnovení
Pomocí nástrojů MySQL, jako je mysqldump a mysqlpump, můžete vypsat a načíst databáze do serveru Azure Database for MariaDB v několika běžných scénářích. 

- Při migraci celé databáze používejte výpisy paměti databáze. Toto doporučení je v případě, že přesouváte velké množství dat nebo pokud chcete minimalizovat přerušení služby pro živé weby nebo aplikace. 
-  Zajistěte, aby všechny tabulky v databázi používaly modul úložiště InnoDB při načítání dat do služby Azure Database for MariaDB. Azure Database for MariaDB podporuje jenom modul úložiště InnoDB a žádné další úložné moduly. Pokud jsou vaše tabulky nakonfigurované s jinými úložnými moduly, převeďte je do formátu modulu InnoDB, než je migrujete do Azure Database for MariaDB.
   
   Pokud máte například aplikaci WordPress nebo webovou aplikaci, která používá tabulky MyISAM, nejprve tyto tabulky převeďte tak, že je migrujete do formátu InnoDB, než je obnovíte do Azure Database for MariaDB. Pomocí klauzule `ENGINE=InnoDB` Nastavte modul, který se má použít pro vytvoření nové tabulky, a pak přeneste data do kompatibilní tabulky před obnovením. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Abyste se vyhnuli jakýmkoli problémům s kompatibilitou při dumpingových databázích, ujistěte se, že na zdrojovém a cílovém systému používáte stejnou verzi MariaDB. Pokud například váš stávající server MariaDB má verzi 10,2, měli byste migrovat do své služby Azure Database for MariaDB, která je nakonfigurovaná tak, aby běžela verze 10,2. `mysql_upgrade`Příkaz nefunguje na Azure Database for MariaDBm serveru a není podporovaný. Pokud potřebujete upgradovat přes MariaDB verze, nejdřív vypíšete nebo exportujte databázi starší verze do novější verze MariaDB ve vašem vlastním prostředí. Potom můžete provést `mysql_upgrade` migraci do Azure Database for MariaDB.

## <a name="performance-considerations"></a>Otázky výkonu
Pro optimalizaci výkonu při ukládání velkých databází mějte na paměti následující skutečnosti:
-   Použijte `exclude-triggers` možnost v mysqldump. Vylučte triggery ze souborů s výpisem paměti, abyste se vyhnuli spuštění příkazů triggeru během obnovování dat. 
-   Použijte `single-transaction` možnost pro nastavení režimu izolace transakcí na možnost opakované čtení a odeslání příkazu spustit transakci SQL na server před dumpingovými daty. Dumpingové množství tabulek v rámci jedné transakce způsobí, že se během obnovování spotřebuje nějaké dodatečné úložiště. `single-transaction`Možnost a `lock-tables` možnost se vzájemně vylučují. Důvodem je to, že uzamčené tabulky způsobují implicitní potvrzení všech čekajících transakcí. Pokud chcete vypsat velké tabulky, zkombinujte `single-transaction` možnost s `quick` možností. 
-   Použijte `extended-insert` syntaxi s více řádky, která obsahuje několik seznamů hodnot. Tento přístup vede k menšímu souboru s výpisem paměti a při opětovném načtení souboru se urychlí vložení.
-  `order-by-primary`Možnost v mysqldump použijte při ukládání databází, aby se data nahlásila v pořadí primárního klíče.
-   `disable-keys`Při ukládání dat použijte možnost v mysqldump, abyste před zatížením zakázali omezení cizího klíče. Zakázání kontrol cizích klíčů pomáhá zvýšit výkon. Povolte omezení a ověřte data po zatížení, abyste zajistili referenční integritu.
-   V případě potřeby použijte dělené tabulky.
-   Načtěte data paralelně. Vyhněte se příliš přílišnému paralelismuům, což by mohlo způsobit dosažení limitu prostředků a monitorování prostředků pomocí metrik dostupných v Azure Portal. 
-   Použijte `defer-table-indexes` možnost v mysqlpump, pokud se jedná o dumpingové databáze, takže vytvoření indexu se stane po načtení dat tabulky.
-   Zkopírujte záložní soubory do úložiště objektů BLOB v Azure a proveďte obnovení z těchto souborů. Tento přístup by měl být mnohem rychlejší než provádění obnovení přes Internet.

## <a name="create-a-backup-file"></a>Vytvoření záložního souboru

Pokud chcete zálohovat stávající databázi MariaDB na místním serveru nebo na virtuálním počítači, spusťte následující příkaz pomocí mysqldump: 

```bash
$ mysqldump --opt -u <uname> -p<pass> <dbname> > <backupfile.sql>
```

K dispozici jsou následující parametry:
- *\<uname>*: Vaše uživatelské jméno databáze 
- *\<pass>*: Heslo pro vaši databázi (Všimněte si, že mezi-p a heslem není žádné místo) 
- *\<dbname>*: Název vaší databáze 
- *\<backupfile.sql>*: Název souboru pro zálohování databáze 
- *\<--opt>*: Možnost mysqldump 

Pokud například chcete zálohovat databázi s názvem *TestDB* na serveru MariaDB s uživatelským jménem *testuser* a bez hesla k souboru testdb_backup. SQL, použijte následující příkaz. Příkaz zálohuje `testdb` databázi do souboru s názvem `testdb_backup.sql` , který obsahuje všechny příkazy SQL potřebné k opětovnému vytvoření databáze. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Chcete-li vybrat konkrétní tabulky k zálohování v databázi, uveďte názvy tabulek oddělené mezerami. Chcete-li například zálohovat pouze tabulky Tabulka1 a Tabulka2 z *TestDB*, postupujte podle tohoto příkladu: 

```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Chcete-li zálohovat více než jednu databázi najednou, použijte přepínač--Database a seznam názvů databází oddělených mezerami. 

```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Vytvoření databáze na cílovém serveru
Na cílovém Azure Database for MariaDB serveru vytvořte prázdnou databázi, do které chcete migrovat data. K vytvoření databáze použijte nástroj, jako je například MySQL Workbench. Databáze může mít stejný název jako databáze, která obsahuje dumpingová data, nebo můžete vytvořit databázi s jiným názvem.

Pokud se chcete připojit, vyhledejte informace o připojení v podokně **Přehled** služby Azure Database for MariaDB.

![Snímek obrazovky s oknem Přehled pro server Azure Database for MariaDB v Azure Portal.](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

V aplikaci MySQL Workbench přidejte informace o připojení.

![Snímek obrazovky s podoknem připojení MySQL v aplikaci MySQL Workbench](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Obnovení databáze MariaDB
Po vytvoření cílové databáze můžete pomocí příkazu MySQL nebo MySQL Workbench obnovit data do nově vytvořené databáze ze souboru s výpisem paměti.

```bash
mysql -h <hostname> -u <uname> -p<pass> <db_to_restore> < <backupfile.sql>
```

V tomto příkladu obnovíte data do nově vytvořené databáze na cílovém serveru Azure Database for MariaDB.

```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-your-mariadb-database-by-using-phpmyadmin"></a>Export databáze MariaDB pomocí phpMyAdmin

K exportu můžete použít společný Nástroj phpMyAdmin, který se už může nainstalovat místně ve vašem prostředí. K exportu databáze MariaDB postupujte takto:
1. Otevřete phpMyAdmin.
1. V levém podokně vyberte svou databázi a potom vyberte odkaz **exportovat** . Zobrazí se nová stránka pro zobrazení výpisu databáze.
1. V oblasti **exportovat** vyberte odkaz **Vybrat vše** a vyberte tabulky v databázi. 
1. V oblasti **Možnosti SQL** vyberte příslušné možnosti. 
1. Vyberte možnost **Uložit jako soubor** a odpovídající možnost komprese a pak vyberte **Přejít**. Na příkazovém řádku uložte soubor místně.

## <a name="import-your-database-by-using-phpmyadmin"></a>Import databáze pomocí phpMyAdmin

Proces importu je podobný procesu exportu. Postupujte následovně:
1. Otevřete phpMyAdmin. 
1. Na stránce instalace phpMyAdmin vyberte **Přidat** a přidejte server Azure Database for MariaDB. 
1. Zadejte podrobnosti připojení a přihlašovací informace.
1. Vytvořte odpovídající pojmenovanou databázi a pak ji vyberte v levém podokně. Chcete-li přepsat existující databázi, vyberte název databáze, zaškrtněte políčka vedle názvů tabulek a **Vyberte možnost Odstranit pro odstranění** existujících tabulek. 
1. Vyberte odkaz **SQL** k zobrazení stránky, kde můžete zadat příkazy SQL, nebo nahrát soubor SQL. 
1. Vyberte tlačítko **Procházet** a vyhledejte soubor databáze. 
1. Vyberte tlačítko **Přejít** pro export zálohy, spusťte příkazy SQL a znovu vytvořte databázi.

## <a name="next-steps"></a>Další kroky
- [Připojte aplikace ke službě Azure Database for MariaDB](./howto-connection-string.md).
