---
title: Migrace databáze MySQL pomocí výpisu a obnovení ve službě Azure Database for MySQL
description: Tento článek vysvětluje dvě běžné způsoby, jak zálohovat a obnovit databáze ve službě Azure Database for MySQL pomocí nástroje, jako je mysqldump aplikace MySQL Workbench a phpmyadmin zobrazuje.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/02/2018
ms.openlocfilehash: e79c83ecb17c4dcd11f7ccbecded59e7d1d13dfd
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542457"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrace databáze MySQL do služby Azure Database for MySQL pomocí výpisu a obnovení.
Tento článek vysvětluje dvě běžné způsoby, jak zálohovat a obnovit databáze ve službě Azure Database for MySQL
- Výpis a obnovení z příkazového řádku (pomocí mysqldump) 
- Výpisu a obnovení pomocí phpmyadmin zobrazuje 

## <a name="before-you-begin"></a>Před zahájením
Pro jednotlivé kroky v této příručce s postupy, musíte mít:
- [Vytvoření Azure Database for MySQL server - webu Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) na počítači nainstalovaný nástroj příkazového řádku.
- Aplikace MySQL Workbench [stažení aplikace MySQL Workbench](https://dev.mysql.com/downloads/workbench/), Toad, Navicat nebo jiný nástroj třetí strany MySQL, výpisu a obnovení příkazů.

## <a name="use-common-tools"></a>Použít běžné nástroje
Pomocí běžných nástrojů a nástrojů, jako jsou aplikace MySQL Workbench, mysqldump, Toad nebo Navicat se vzdáleně připojte a obnovení dat do služby Azure Database for MySQL. Připojení ke službě Azure Database for MySQL pomocí těchto nástrojů v klientském počítači s připojením k Internetu. Osvědčené postupy zabezpečení připojení šifrováno SSL, najdete v tématu taky [konfigurace připojení SSL ve službě Azure Database for MySQL](concepts-ssl-connection-security.md). Není potřeba přesunout soubory s výpisem paměti do libovolného umístění speciální cloudových při migraci do služby Azure Database for MySQL. 

## <a name="common-uses-for-dump-and-restore"></a>Běžné použití výpisu a obnovení.
Můžete použít nástroje MySQL jako je například mysqldump a mysqlpump příkazy dump a load databází do Azure Database for MySQL v několika běžných scénářů. V jiných scénářích může použít [Import a Export](concepts-migrate-import-export.md) místo toho dosáhnout.

- Použít databázi výpisy při migraci celé databáze. Toto doporučení obsahuje při přesunu velké množství dat MySQL, nebo když chcete minimalizovat přerušení služeb pro weby nebo aplikace. 
-  Ujistěte se, že všechny tabulky v databázi použít modul InnoDB úložiště při načítání dat do služby Azure Database for MySQL. Azure Database for MySQL podporuje jenom modul InnoDB úložiště a proto nepodporuje moduly alternativní úložiště. Pokud vaše tabulky jsou nakonfigurované s ostatních vyhledávacích strojů úložiště, převeďte na formát modul InnoDB před migrací do služby Azure Database for MySQL.
   Například pokud máte WordPress nebo webové aplikace pomocí tabulky MyISAM, nejprve převeďte tyto tabulky jejich migrací do formátu InnoDB před obnovením do služby Azure Database for MySQL. Použití klauzule `ENGINE=InnoDB` nastavit modul používá při vytváření nové tabulky, pak přenést data do tabulky kompatibilní ještě před obnovením. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Aby se zabránilo žádné problémy s kompatibilitou, zajistěte, aby že stejnou verzi nástroje MySQL se používá na zdrojovém a cílovém systému při výpisu databází. Například pokud váš stávající server MySQL verzi 5.7, pak by měl migraci do služby Azure Database for MySQL nakonfigurováno pro běh verzi 5.7. `mysql_upgrade` Příkaz nefunguje v serveru Azure Database for MySQL a není podporována. Pokud potřebujete k upgradu mezi verzemi MySQL, nejprve výpisu paměti nebo ji exportujte nižší verzi databáze na vyšší verzi MySQL ve vašem prostředí. Potom spusťte `mysql_upgrade`, před provedením migrace do Azure Database for MySQL.

## <a name="performance-considerations"></a>Otázky výkonu
Za účelem optimalizace výkonu vzít v úvahu tyto aspekty při výpisu velkých databází:
-   Použití `exclude-triggers` možnost v mysqldump při výpisu databází. Vyloučíte ze souborů s výpisem paměti, aby aktivační událost příkazy ohlásí při obnovování dat aktivační události. 
-   Použití `single-transaction` možnost nastavit režim izolace transakce na REPEATABLE READ a odešle příkaz Spustit TRANSAKCI SQL serveru před vypsání data. Vypsání mnoho tabulek v rámci jedné transakce způsobí, že některé dodatečné úložiště, který se má používat při obnovení. `single-transaction` Možnost a `lock-tables` možnost se vzájemně vylučují, protože ZÁMEK tabulky způsobí, že všechny čekající transakce pro potvrzení implicitně. Pro výpis velké tabulky, kombinovat `single-transaction` spolu s možností `quick` možnost. 
-   Použití `extended-insert` syntaxi více řádků, která obsahuje několik seznamů hodnot. To má za následek menší soubor s výpisem paměti a urychluje operace vložení při opětovném načtení nástroje souboru.
-  Použití `order-by-primary` možnost v mysqldump při výpisu databáze, tak, aby data je vytvořena v pořadí podle primárních klíčů.
-   Použití `disable-keys` možnost mysqldump při výpisu data, zákaz omezení cizího klíče před zatížení. Zakázání cizího klíče kontroly poskytuje zvýšení výkonu. Povolte omezení a ověřit data po načtení k zajištění referenční integrity.
-   Dělené tabulky v případě potřeby použijte.
-   Načtení dat paralelně. Vyhněte se příliš mnoho paralelismu, který by vést k dosažení limitu prostředků a sledujte prostředky prostřednictvím metriky, které jsou k dispozici na webu Azure Portal. 
-   Použití `defer-table-indexes` možnost v mysqlpump při výpisu databáze, takže vytvoření indexu se stane po načtení dat tabulky.
-   Zkopírujte záložní soubory do Azure blob a úložiště a provést obnovení z něj, které by měly být mnohem rychlejší než provádění obnovení na Internetu.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Vytvořit záložní soubor z příkazového řádku pomocí mysqldump
Chcete-li zálohovat stávající databázi MySQL na serveru místní, nebo na virtuálním počítači, spusťte následující příkaz: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Zadejte parametry jsou:
- [uname] Vaše uživatelské jméno pro databázi 
- [heslo] Heslo pro vaši databázi (Všimněte si, neexistuje žádná mezera mezi -p a heslo) 
- [název_databáze] Název databáze 
- [backupfile.sql] název souboru pro zálohování databáze 
- [– optimalizované] Možnost mysqldump 

Například pokud chcete zálohovat databázi s názvem "testdb" na serveru MySQL s uživatelským jménem "testuser" a bez hesla do souboru testdb_backup.sql, použijte následující příkaz. Příkaz zálohuje `testdb` databáze do souboru s názvem `testdb_backup.sql`, která obsahuje všechny příkazy SQL potřebné k vytvoření nové databáze. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Pokud chcete vybrat konkrétní tabulky v databázi zálohovat, seznam názvů tabulek, oddělené mezerami. Například pokud chcete zálohovat pouze Tabulka1 a Tabulka2 tabulky z "testdb", postupujte podle tohoto příkladu: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Chcete-li více než jednu databázi zálohovat najednou, použijte--databáze přepnutí a seznamu názvů databáze oddělených mezerami. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Chcete-li v jednom okamžiku zálohovat všechny databáze na serveru, měli byste použít možnost--all databází.
```bash
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Vytvoření databáze na cílovou službu Azure Database for MySQL serveru
Vytvoření prázdné databáze na cílovou službu Azure Database pro server MySQL, ve které chcete migrovat data. Pomocí nástroje, jako je aplikace MySQL Workbench, Toad nebo Navicat k vytvoření databáze. Databáze může mít stejný název jako databáze, která je obsažená vypsaná bitová kopie řidicího dat nebo můžete vytvořit databázi s jiným názvem.

Se připojí, vyhledejte informace o připojení v **přehled** z Azure Database for MySQL.

![Najít informace o připojení webu Azure Portal](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Přidejte informace o připojení do vaší aplikace MySQL Workbench.

![Aplikace MySQL Workbench připojovací řetězec](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Obnovení databáze MySQL pomocí příkazového řádku nebo MySQL Workbench
Po vytvoření cílové databáze můžete použít příkaz mysql nebo MySQL Workbench k obnovení dat do určeného nově vytvořenou databázi ze souboru s výpisem paměti.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
V tomto příkladu obnovte data do nově vytvořenou databázi na cílovou službu Azure Database for MySQL server.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Export prostřednictvím phpmyadmin zobrazuje
Pokud chcete exportovat, můžete použít běžné phpmyadmin zobrazuje nástroj, který může už nainstalované místně ve vašem prostředí. Export databáze MySQL pomocí phpmyadmin zobrazuje:
1. Otevřete phpmyadmin zobrazuje.
2. Vyberte vaši databázi. Klikněte na název databáze v seznamu na levé straně. 
3. Klikněte na tlačítko **exportovat** odkaz. Chcete-li zobrazit výpis databáze se zobrazí nová stránka.
4. V oblasti exportu klikněte na tlačítko **Vybrat vše** odkaz vybrat tabulky v databázi. 
5. V oblasti možnosti SQL klepněte na příslušné možnosti. 
6. Klikněte na tlačítko **uložit jako soubor** možnost a odpovídající komprese možnost a potom klikněte na tlačítko **Přejít** tlačítko. By měl zobrazit dialogové okno s výzvou k uložení si soubor .CSR místně.

## <a name="import-using-phpmyadmin"></a>Import prostřednictvím phpmyadmin zobrazuje
Import databáze je podobný export. Proveďte následující akce:
1. Otevřete phpmyadmin zobrazuje. 
2. Na stránce nastavení phpmyadmin zobrazuje, klikněte na **přidat** přidání vašeho serveru Azure Database for MySQL. Zadejte podrobnosti připojení a přihlašovací údaje.
3. Vytvořit databázi příslušně pojmenovaných a vyberte na levé straně obrazovky. Přepsat stávající databázi, klikněte na název databáze, vyberte všechna zaškrtávací políčka vedle názvů tabulek a vyberte **vyřadit** odstranit existující tabulky. 
4. Klikněte na tlačítko **SQL** odkaz na zobrazení stránky, kde můžete zadat v příkazech SQL nebo odeslání souboru SQL. 
5. Použití **Procházet** tlačítko k vyhledání souboru databáze. 
6. Klikněte na tlačítko **Přejít** tlačítko Exportovat zálohování, spuštění příkazů SQL a znovu vytvořit databázi.

## <a name="next-steps"></a>Další postup
- [Připojení aplikace ke službě Azure Database for MySQL](./howto-connection-string.md).
- Další informace o migraci databází do služby Azure Database for MySQL, najdete v článku [Průvodce migrací databází](https://aka.ms/datamigration).
