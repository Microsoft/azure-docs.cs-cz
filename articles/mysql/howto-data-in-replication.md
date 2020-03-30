---
title: Konfigurace replikace datového připojení – databáze Azure pro MySQL
description: Tento článek popisuje, jak nastavit replikaci dat pro databázi Azure pro MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 2148ce41267627d9d6e0437897a99a8dbdbe0746
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382762"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Jak nakonfigurovat Azure Database for MySQL Data-in Replication

V tomto článku se dozvíte, jak nastavit replikaci dat ve službě Azure Database for MySQL konfigurací hlavních a replikových serverů. Replikace dat umožňuje synchronizovat data z hlavního serveru MySQL spuštěného místně, ve virtuálních počítačích nebo databázových službách hostovaných jinými poskytovateli cloudu do repliky ve službě Azure Database for MySQL. 

Tento článek předpokládá, že máte alespoň některé předchozí zkušenosti se servery MySQL a databáze.

Před provedením kroků v tomto článku zkontrolujte [omezení a požadavky](concepts-data-in-replication.md#limitations-and-considerations) replikace datového systému.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Vytvoření serveru MySQL, který bude použit jako replika

1. Vytvoření nové databáze Azure pro server MySQL

   Vytvořte nový MySQL server (např. "replica.mysql.database.azure.com"). Odkazovat na [vytvoření databáze Azure pro server MySQL pomocí portálu Azure](quickstart-create-mysql-server-database-using-azure-portal.md) pro vytvoření serveru. Tento server je "replika" server v datové replikace.

   > [!IMPORTANT]
   > Azure Database for MySQL server musí být vytvořen v obecné účely nebo optimalizace paměti cenové úrovně.
   > 

2. Vytvoření stejných uživatelských účtů a odpovídajících oprávnění

   Uživatelské účty nejsou replikovány z hlavního serveru na replikovací server. Pokud plánujete poskytnout uživatelům přístup k serveru replik, budete muset ručně vytvořit všechny účty a odpovídající oprávnění na této nově vytvořené azure databáze pro server MySQL.

3. Přidejte ip adresu hlavního serveru do pravidel brány firewall repliky. 

   Pomocí webu [Azure Portal](howto-manage-firewall-using-portal.md) nebo [Azure CLI](howto-manage-firewall-using-cli.md) aktualizujte pravidla brány firewall.

## <a name="configure-the-master-server"></a>Konfigurace hlavního serveru
Následující kroky připraví a nakonfigurují server MySQL hostovaný místně, ve virtuálním počítači nebo databázovou službu hostovojinou jinými poskytovateli cloudu pro replikaci dat. Tento server je "hlavní" v replikaci datového připojení.


1. Než budete pokračovat, zkontrolujte [požadavky hlavního serveru.](concepts-data-in-replication.md#requirements) 

   Například zajistěte, aby hlavní server povoloval příchozí i odchozí provoz na portu 3306 a aby hlavní server měl **veřejnou IP adresu**, dns je veřejně přístupný nebo má plně kvalifikovaný název domény (Plně kvalifikovaný název domény). 
   
   Otestujte připojení k hlavnímu serveru pokusem o připojení z nástroje, jako je příkazový řádek MySQL hostovaný na jiném počítači nebo z [prostředí Azure Cloud Shell,](https://docs.microsoft.com/azure/cloud-shell/overview) který je k dispozici na webu Azure Portal. 

2. Zapnutí binárního protokolování

   Spuštěním následujícího příkazu zkontrolujte, zda bylo v hlavním serveru povoleno binární protokolování: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Pokud je [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) proměnná vrácena s hodnotou "ON", binární protokolování je povoleno na serveru. 

   Pokud `log_bin` je vrácena s hodnotou "OFF", zapněte binární protokolování `log_bin=ON` úpravou souboru my.cnf tak, aby a restartujte server pro změnu projeví.

3. Nastavení hlavního serveru

   Replikace datového `lower_case_table_names` data vyžaduje, aby parametr byl konzistentní mezi hlavním a replikami serverů. Tento parametr je ve výchozím nastavení 1 v Azure Database for MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. Vytvoření nové role replikace a nastavení oprávnění

   Vytvořte uživatelský účet na hlavním serveru, který je nakonfigurován s oprávněními replikace. To lze provést pomocí příkazů SQL nebo nástroje, jako je MySQL Workbench. Zvažte, zda plánujete replikovat s SSL, protože to bude nutné zadat při vytváření uživatele. Informace o tom, jak [přidat uživatelské účty](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) na hlavní server, naleznete v dokumentaci k MySQL. 

   V následujících příkazech má nová vytvořená role replikace přístup k hlavnímu serveru z libovolného počítače, nikoli pouze z počítače, který hostuje samotný hlavní server. To se provádí zadáním "syncuser@'%'" v příkazu vytvořit uživatele. Další informace o [určení názvů účtů](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)naleznete v dokumentaci k MySQL .

   **Příkaz SQL**

   *Replikace pomocí ssl*

   Chcete-li vyžadovat ssl pro všechna uživatelská připojení, vytvořte uživatele pomocí následujícího příkazu: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikace bez ssl*

   Pokud není pro všechna připojení vyžadováno ssl, vytvořte uživatele pomocí následujícího příkazu:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Chcete-li vytvořit roli replikace v pracovní plochy MySQL, otevřete panel **Uživatelé a oprávnění** z panelu **Správa.** Poté klikněte na **Přidat účet**. 
 
   ![Uživatelé a oprávnění](./media/howto-data-in-replication/users_privileges.png)

   Do pole **Přihlašovací jméno** zadejte uživatelské jméno. 

   ![Synchronizovat uživatele](./media/howto-data-in-replication/syncuser.png)
 
   Klikněte na panel **Role pro správu** a ze seznamu **globálních oprávnění**vyberte **Položku Slave replikace** . Kliknutím na **Použít** vytvořte roli replikace.

   ![Slave replikace](./media/howto-data-in-replication/replicationslave.png)


5. Nastavení hlavního serveru na režim jen pro čtení

   Před zahájením výpisu z databáze, server musí být umístěn v režimu jen pro čtení. V režimu jen pro čtení nebude hlavní server schopen zpracovat žádné transakce zápisu. Vyhodnoťte dopad na vaši firmu a v případě potřeby naplánujte okno jen pro čtení v době mimo špičku.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. Získat název a posun binárního souboru protokolu

   Spusťte příkaz k [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) určení aktuálního názvu a posunu souboru binárního protokolu.
    
   ```sql
   show master status;
   ```
   Výsledky by měly být jako následující. Nezapomeňte si poznamenat název binárního souboru, protože bude použit v pozdějších krocích.

   ![Výsledky hlavního stavu](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Výpis a obnovení hlavního serveru

1. Vypácení všech databází z hlavního serveru

   Můžete použít mysqldump k výpisu databází z vašeho hlavního. Podrobnosti naleznete v části [Dump & Restore](concepts-migrate-dump-restore.md). Není nutné vypustit knihovnu MySQL a testovací knihovnu.

2. Nastavení hlavního serveru na režim čtení a zápisu

   Jakmile je databáze byla dumpingové, změnit hlavní MySQL server zpět do režimu čtení a zápisu.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Obnovit soubor výpisu na nový server

   Obnovte soubor výpisu stavu na server vytvořený ve službě Azure Database for MySQL. Naleznete [dump & obnovení,](concepts-migrate-dump-restore.md) jak obnovit soubor výpisu na server MySQL. Pokud je soubor s výpisem stavu paměti velký, nahrajte ho do virtuálního počítače v Azure ve stejné oblasti jako váš replikovací server. Obnovte ji do databáze Azure pro server MySQL z virtuálního počítače.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Propojení hlavních a replikových serverů pro spuštění replikace datového připojení

1. Nastavení hlavního serveru

   Všechny funkce replikace dat se provádějí pomocí uložených procedur. Všechny procedury najdete na [adrese Uložené procedury replikace dat](reference-data-in-stored-procedures.md). Uložené procedury lze spustit v prostředí MySQL nebo MySQL Workbench. 

   Chcete-li propojit dva servery a zahájit replikaci, přihlaste se k cílovému serveru replik ve službě Azure DB for MySQL a nastavte externí instanci jako hlavní server. To se provádí `mysql.az_replication_change_master` pomocí uložené procedury na serveru Azure DB for MySQL.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: název hostitele hlavního serveru
   - master_user: uživatelské jméno hlavního serveru
   - master_password: heslo pro hlavní server
   - master_log_file: název binárního souboru protokolu ze spuštění`show master status`
   - master_log_pos: binární pozice protokolu ze spuštění`show master status`
   - master_ssl_ca: Kontext certifikátu certifikační autority. Pokud nepoužíváte SSL, předajte prázdný řetězec.
       - Doporučuje se předat tento parametr jako proměnnou. Další informace naleznete v následujících příkladech.

> [!NOTE]
> Pokud je hlavní server hostovaný v virtuálním počítači Azure, nastavte "Povolit přístup ke službám Azure" na "ON", aby hlavní servery a repliky mohly vzájemně komunikovat. Toto nastavení lze změnit z možností **zabezpečení připojení.** Další informace naleznete v [části Správa pravidel brány firewall pomocí portálu.](howto-manage-firewall-using-portal.md)

   **Příklady**

   *Replikace pomocí ssl*

   Proměnná `@cert` je vytvořena spuštěním následujících příkazů MySQL: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Replikace pomocí ssl je nastavena mezi hlavním serverem hostovaným v doméně "companya.com" a replikami serveru hostovaného v Azure Database for MySQL. Tato uložená procedura je spuštěna v replice. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Replikace bez ssl*

   Replikace bez ssl je nastavena mezi hlavním serverem hostovaným v doméně "companya.com" a replikaserverem hostovaným v Azure Database for MySQL. Tato uložená procedura je spuštěna v replice.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. Zahájit replikaci

   Volání `mysql.az_replication_start` uložené procedury zahájit replikaci.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Zkontrolovat stav replikace

   Volání [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) příkazu na serveru repliky zobrazíte stav replikace.
    
   ```sql
   show slave status;
   ```

   Pokud stav `Slave_IO_Running` a `Slave_SQL_Running` jsou "ano" a `Seconds_Behind_Master` hodnota je "0", replikace funguje dobře. `Seconds_Behind_Master`označuje, jak pozdě je replika. Pokud hodnota není "0", znamená to, že replika zpracovává aktualizace. 

## <a name="other-stored-procedures"></a>Další uložené procedury

### <a name="stop-replication"></a>Zastavení replikace

Chcete-li zastavit replikaci mezi hlavním serverem a replikami, použijte následující uloženou proceduru:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Odebrání relace replikace

Chcete-li odebrat vztah mezi hlavním serverem a serverem repliky, použijte následující uloženou proceduru:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Přeskočit chybu replikace

Chcete-li přeskočit chybu replikace a povolit pokračování replikace, použijte následující uloženou proceduru:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Další kroky
- Další informace o [replikaci dat](concepts-data-in-replication.md) pro databázi Azure pro MySQL. 
