---
title: Konfigurace replikace dat k replikaci dat do služby Azure Database pro MariaDB.
description: Tento článek popisuje, jak nastavit dat replikace pro službu Azure Database pro MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 3897c402e45962836880ccebbeb252d189188d3c
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548897"
---
# <a name="how-to-configure-azure-database-for-mariadb-data-in-replication"></a>Jak nakonfigurovat – Azure Database pro MariaDB Data replikace

V tomto článku se dozvíte, jak nastavit replikaci dat ve službě Azure Database pro MariaDB služby díky konfiguraci serverů master a repliky. Replikace dat umožňuje synchronizovat data z hlavního serveru MariaDB spuštěné místně, v databázi služby hostované jiných poskytovatelů cloudových služeb do repliky ve službě Azure Database pro MariaDB službu nebo virtuálních počítačích. 

Tento článek předpokládá, že máte alespoň nějaké zkušenosti s MariaDB serverům a databázím.

## <a name="create-a-mariadb-server-to-be-used-as-replica"></a>Vytvoření serveru MariaDB má být použit jako repliky

1. Vytvořit novou službu Azure Database pro MariaDB server

   Vytvoření nového serveru MariaDB (např.) "replica.mariadb.database.azure.com"). Odkazovat na [vytvoření serveru Azure Database for MariaDB pomocí webu Azure portal](quickstart-create-mariadb-server-database-using-azure-portal.md) pro vytvoření serveru. Tímto serverem je server "replika" v dat replikace.

   > [!IMPORTANT]
   > Azure Database pro MariaDB server musí být vytvořeny v obecné účely nebo k paměťově optimalizovaným cenové úrovně.
   > 

2. Vytváření stejných uživatelských účtů a odpovídající oprávnění

   Uživatelské účty se nereplikují z hlavního serveru serverem repliky. Pokud plánujete, že uživatel s přístupem k serveru repliky, musíte ručně vytvořit všechny účty a odpovídající oprávnění na tuto nově vytvořenou – Azure Database pro MariaDB server.

## <a name="configure-the-master-server"></a>Konfigurace hlavního serveru
Následující kroky připravte a nakonfigurujte MariaDB serveru místní, v virtuálního počítače nebo databáze služba hostovaná společností jiných poskytovatelů cloudových služeb pro Data replikace. Tento server je "hlavní" v dat replikace. 

1. Zapnout v binární protokolování

   Zaškrtněte, pokud chcete zobrazit, pokud je binární protokolování povoleno na hlavním serveru spuštěním následujícího příkazu: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Pokud proměnná [ `log_bin` ](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) je vrácen s hodnotou "ON", je na serveru zapnuté binární protokolování. 

   Pokud `log_bin` se vrátila hodnotu "OFF", zapnout protokolování tak, že upravíte soubor my.cnf tak binární soubor, který `log_bin=ON` a restartujte server se změna projevila.

2. Nastavení hlavního serveru

   Replikace dat vyžaduje parametr `lower_case_table_names` bude konzistentní napříč servery master a repliky. Tento parametr je 1 ve výchozím nastavení ve službě Azure Database pro MariaDB. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Vytvoření nové role replikace a nastavení oprávnění

   Vytvoření uživatelského účtu na hlavním serveru, který je nakonfigurovaný s oprávněními pro replikaci. To můžete udělat pomocí příkazů SQL nebo nástroje, jako je aplikace MySQL Workbench. Zvažte, zda máte v úmyslu na replikaci s protokolem SSL, jak to bude potřeba zadat při vytváření uživatele. Naleznete v dokumentaci MariaDB pochopit, jak [přidejte uživatelské účty](https://mariadb.com/kb/en/library/create-user/) na hlavní server. 

   V níže uvedených příkazů nové role replikace vytvořili má přístup k hlavnímu serveru z libovolného počítače, ne jenom počítač, který hostuje hlavní server sám. To se provádí tak, že zadáte "syncuser\@'%'" v příkazu pro vytvoření uživatele. V dokumentaci MariaDB Další informace o [určující názvy účtů](https://mariadb.com/kb/en/library/create-user/#account-names).

   **Příkaz SQL**

   *Replikace s protokolem SSL*

   Pokud chcete vyžadovat protokol SSL pro všechna připojení uživatele, použijte následující příkaz pro vytvoření uživatele: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikace bez protokolu SSL*

   Pokud není protokol SSL vyžaduje pro všechna připojení, použijte následující příkaz pro vytvoření uživatele:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **Aplikace MySQL Workbench**

   Chcete-li vytvořit role replikace v aplikaci MySQL Workbench, otevřete **uživatelů a oprávnění** panelu z **správu** panelu. Potom klikněte na **přidat účet**. 
 
   ![Uživatele a oprávnění](./media/howto-data-in-replication/users_privileges.png)

   Zadejte uživatelské jméno do **přihlašovací jméno** pole. 

   ![Synchronizovat uživatele](./media/howto-data-in-replication/syncuser.png)
 
   Klikněte na **správních rolí** panelu a pak vyberte **podřízený server replikace** ze seznamu **globální oprávnění**. Potom klikněte na **použít** vytvoření role replikace.

   ![Podřízený server replikace](./media/howto-data-in-replication/replicationslave.png)


4. Nastavte hlavní server do režimu jen pro čtení

   Před zahájením pro výpis na databázi, server musí být umístěny v režimu jen pro čtení. V režimu jen pro čtení, bude hlavní server nemůže zpracovat žádné transakce zápisu. Vyhodnotit její dopad na vaši firmu a naplánování okna jen pro čtení v dobu mimo špičku v případě potřeby.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Získání názvu souboru binárního protokolu a posun

   Spustit [ `show master status` ](https://mariadb.com/kb/en/library/show-master-status/) příkazu zjistěte aktuální název souboru binárního protokolu a posun.
    
   ```sql
   show master status;
   ```
   Výsledky by měly být jako následující. Ujistěte se, jak se použije v dalších krocích, poznamenejte si název binárního souboru.

   ![Hlavní stav výsledky](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Výpisu a obnovení hlavního serveru

1. Vypsat všechny databáze z hlavního serveru

   Mysqldump můžete použít k databázím s výpisem paměti z vašeho hlavního serveru. Podrobnosti najdete v [výpisu a obnovení](howto-migrate-dump-restore.md). Není nutné vypsat knihovny MySQL a testovací knihovnou.

2. Nastavte hlavní server do režimu pro čtení a zápis

   Jakmile byl zálohované databáze, změnit hlavní MariaDB server zpátky do režimu pro čtení a zápisu.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Obnovit soubor s výpisem paměti na nový server

   Obnovte soubor s výpisem paměti na server, který vytvořili ve službě Azure Database pro MariaDB službu. Odkazovat na [výpisu a obnovení](howto-migrate-dump-restore.md) pro obnovení serveru MariaDB soubor s výpisem paměti. Pokud je soubor s výpisem paměti velký, nahrajte ho do virtuálního počítače v Azure v rámci stejné oblasti jako server repliky. Obnovení k Azure Database pro MariaDB server z virtuálního počítače.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Hlavní a repliky servery odkaz ke spuštění replikace dat

1. Nastaví hlavní server

   Všechny funkce replikace dat provádí uložené procedury. Můžete najít všechny postupy v [Data v uložené procedury replikace](reference-data-in-stored-procedures.md). Uložené procedury lze spustit v prostředí MySQL nebo MySQL Workbench.

   K propojení dvou serverů a spuštění replikace, přihlášení k cílovému serveru repliky v Azure DB pro službu MariaDB a nastavit externí instanci jako hlavní server. To se provádí pomocí `mysql.az_replication_change_master` uloženou proceduru v Azure DB for MariaDB serveru.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: název hostitele hlavního serveru
   - master_user: uživatelské jméno nadřazeného serveru
   - master_password: heslo nadřazeného serveru
   - master_log_file: název souboru binárního protokolu spuštění `show master status`
   - master_log_pos: pozice binární protokol spuštění `show master status`
   - master_ssl_ca: Certifikát certifikační Autority kontextu. Pokud nepoužíváte protokol SSL, se předá prázdný řetězec.
       - Je doporučeno předat tento parametr jako proměnnou. Podívejte se na následující příklady pro další informace.

   **Příklady**

   *Replikace s protokolem SSL*

   Proměnná `@cert` se vytvoří pomocí průvodce následující příkazy: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Mezi hlavní server, který je hostován v doméně "SpolecnostA.cz" a serverem repliky hostované ve službě Azure Database pro MariaDB nastaven replikace s protokolem SSL. Tuto uloženou proceduru se spouští v replice. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
   ```
   *Replikace bez protokolu SSL*

   Replikace bez SSL je nastavená mezi hlavní server, který je hostován v doméně "SpolecnostA.cz" a serverem repliky hostované ve službě Azure Database pro MariaDB. Tuto uloženou proceduru se spouští v replice.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
   ```

2. Počáteční replikace

   Volání `mysql.az_replication_start` uložené procedury k zahájení replikace.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Zkontrolovat stav replikace

   Volání [ `show slave status` ](https://mariadb.com/kb/en/library/show-slave-status/) příkaz na serveru repliky, chcete-li zobrazit stav replikace.
    
   ```sql
   show slave status;
   ```

   Pokud státu `Slave_IO_Running` a `Slave_SQL_Running` jsou "Ano" a hodnota `Seconds_Behind_Master` je "0", dobře funguje replikace. `Seconds_Behind_Master` Určuje, jak pozdní repliky. Pokud hodnota je "0", znamená to, že je replika zpracování aktualizací. 

## <a name="other-stored-procedures"></a>Dalších uložených procedur

### <a name="stop-replication"></a>Zastavit replikaci

Pokud chcete zastavit replikaci mezi serverem pro hlavní a repliku, použijte následující uložené procedury:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Odeberte vztah replikace

Pokud chcete odebrat vztah mezi serverem pro hlavní a repliky, použijte následující uložené procedury:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Přeskočit replikace – chyba

Chcete-li přeskočit Chyba replikace a povolení replikace, aby bylo možné pokračovat, použijte následující uložené procedury:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Další postup
- Další informace o [replikace dat](concepts-data-in-replication.md) pro službu Azure Database pro MariaDB.
