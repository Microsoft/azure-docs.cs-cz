---
title: Konfigurace replikace dat – Azure Database for MySQL
description: Tento článek popisuje, jak nastavit Replikace vstupních dat pro Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 8/7/2020
ms.openlocfilehash: 8ebb524a5297380fca575ce6849fe4c5f15507cb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903988"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Postup konfigurace Azure Database for MySQL Replikace vstupních dat

Tento článek popisuje, jak nastavit [replikace vstupních dat](concepts-data-in-replication.md) v Azure Database for MySQL konfigurací serverů hlavní servery a repliky. V tomto článku se předpokládá, že máte několik předchozích zkušeností se servery a databázemi MySQL.

> [!NOTE]
> Komunikace bez posunu
>
> Microsoft podporuje různé a zahrnuté prostředí. Tento článek obsahuje odkazy na _podřízený_text. [Průvodce stylem Microsoft pro komunikaci bez předplatných](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) se tímto způsobem rozpoznává jako vyloučené slovo. Toto slovo se v tomto článku používá kvůli konzistenci, protože je aktuálně slovo, které se zobrazuje v softwaru. Když se software aktualizuje, aby se odebralo slovo, aktualizuje se tento článek na zarovnání.
>

Pokud chcete vytvořit repliku ve službě Azure Database for MySQL, [replikace vstupních dat](concepts-data-in-replication.md)  synchronizuje data z hlavního serveru MySQL místně, na virtuálních počítačích (VM) nebo v cloudových databázových službách. Replikace vstupních dat je založená na replikaci na základě pozice v souboru binárního protokolu (binlog) nativní pro MySQL. Další informace o replikaci binlog najdete v tématu [Přehled replikace MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Před provedením kroků v tomto článku zkontrolujte [omezení a požadavky](concepts-data-in-replication.md#limitations-and-considerations) na replikaci dat.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Vytvoření serveru MySQL, který se bude používat jako replika

1. Vytvořit nový Azure Database for MySQL server

   Vytvořte nový server MySQL (např. "replica.mysql.database.azure.com"). Informace o [vytvoření serveru Azure Database for MySQL pomocí Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) pro vytvoření serveru. Tento server je server repliky v Replikace vstupních dat.

   > [!IMPORTANT]
   > Azure Database for MySQL server musí být vytvořený v cenové úrovni optimalizované pro Pro obecné účely nebo paměť.
   > 

1. Vytváření stejných uživatelských účtů a odpovídajících oprávnění

   Uživatelské účty se nereplikují z hlavního serveru na server repliky. Pokud plánujete poskytnout uživatelům přístup k serveru repliky, budete muset ručně vytvořit všechny účty a odpovídající oprávnění na tomto nově vytvořeném serveru Azure Database for MySQL.

1. Přidejte IP adresu hlavního serveru k pravidlům brány firewall repliky. 

   Pomocí webu [Azure Portal](howto-manage-firewall-using-portal.md) nebo [Azure CLI](howto-manage-firewall-using-cli.md) aktualizujte pravidla brány firewall.

## <a name="configure-the-master-server"></a>Konfigurace hlavního serveru
Následující kroky připravují a konfigurují hostovaný Server MySQL v místním prostředí, ve virtuálním počítači nebo databázové službě hostované jinými poskytovateli cloudu pro Replikace vstupních dat. Tento server je "hlavní" v rámci replikace dat.


1. Než budete pokračovat, zkontrolujte [požadavky hlavního serveru](concepts-data-in-replication.md#requirements) . 

   Ujistěte se například, že hlavní server umožňuje příchozí i odchozí provoz na portu 3306 a že má hlavní server **veřejnou IP adresu**, služba DNS je veřejně přístupná nebo má plně kvalifikovaný název domény (FQDN). 
   
   Otestujte připojení k hlavnímu serveru tím, že se pokusíte připojit z nástroje, jako je například příkaz MySQL hostovaný na jiném počítači nebo [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) k dispozici v Azure Portal.

1. Zapnout binární protokolování

   Spuštěním následujícího příkazu zkontrolujte, jestli je v hlavní části povolené binární protokolování. 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Pokud se proměnná [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) vrátí s hodnotou "on", binární protokolování je na vašem serveru povolené. 

   Pokud `log_bin` se vrátí s hodnotou "off" (vypnuto), zapněte binární protokolování úpravou souboru my. CNF tak, aby se změna projevila, `log_bin=ON` a restartujte server.

1. Nastavení hlavního serveru

   Replikace vstupních dat vyžaduje, `lower_case_table_names` aby byl parametr konzistentní mezi hlavním serverem a serverem repliky. Tento parametr ve výchozím nastavení ve Azure Database for MySQL je 1. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

1. Vytvoření nové role replikace a nastavení oprávnění

   Vytvořte uživatelský účet na hlavním serveru, který je nakonfigurovaný s oprávněními replikace. To se dá udělat prostřednictvím příkazů SQL nebo nástroje jako MySQL Workbench. Zvažte, jestli plánujete replikaci s protokolem SSL, protože při vytváření uživatele je potřeba zadat. V dokumentaci MySQL najdete informace o tom, jak [Přidat uživatelské účty](https://dev.mysql.com/doc/refman/5.7/en/user-names.html) na hlavní server. 

   V následujících příkazech je nově vytvořená role replikace schopná získat přístup k hlavnímu serveru z libovolného počítače, nikoli jenom z počítače, který hostuje hlavní server. To se provádí zadáním "syncuser@ '% ' v příkazu CREATE USER. Další informace o [zadávání názvů účtů](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)najdete v dokumentaci k MySQL.

   **Příkaz SQL**

   *Replikace s protokolem SSL*

   Chcete-li pro všechna připojení uživatelů vyžadovat protokol SSL, použijte následující příkaz k vytvoření uživatele: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikace bez SSL*

   Pokud pro všechna připojení není vyžadován protokol SSL, vytvořte uživatele pomocí následujícího příkazu:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Chcete-li vytvořit roli replikace v aplikaci MySQL Workbench, otevřete panel **Uživatelé a oprávnění** na panelu **Správa** . Pak klikněte na **Přidat účet**. 
 
   :::image type="content" source="./media/howto-data-in-replication/users_privileges.png" alt-text="Uživatelé a oprávnění":::

   Do pole **přihlašovací jméno** zadejte uživatelské jméno. 

   :::image type="content" source="./media/howto-data-in-replication/syncuser.png" alt-text="Synchronizovat uživatele":::
 
   Klikněte na panel **role pro správu** a potom v seznamu **globálních oprávnění**vyberte **replikace podřízená** . Pak kliknutím na **použít** vytvořte roli replikace.

   :::image type="content" source="./media/howto-data-in-replication/replicationslave.png" alt-text="Replikace podřízených":::

1. Nastavení hlavního serveru na režim jen pro čtení

   Než začnete s vypsáním paměti v databázi, musí být server umístěn v režimu jen pro čtení. V režimu jen pro čtení nebude hlavním serverem možné zpracovat žádné transakce zápisu. V případě potřeby vyhodnoťte dopad na vaši firmu a v případě potřeby Naplánujte okno jen pro čtení v době mimo špičku.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

1. Získat název a posun binárního souboru protokolu

   Spusťte [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) příkaz a určete aktuální binární název a posun souboru protokolu.
    
   ```sql
   show master status;
   ```
   Výsledky by měly vypadat podobně jako následující. Nezapomeňte si poznamenat název binárního souboru, jak bude použit v pozdějších krocích.

   :::image type="content" source="./media/howto-data-in-replication/masterstatus.png" alt-text="Výsledky hlavního stavu":::
 
## <a name="dump-and-restore-master-server"></a>Výpis a obnovení hlavního serveru

1. Určete databáze a tabulky, které chcete replikovat, do Azure Database for MySQL a proveďte výpis z hlavního serveru.
 
    Pomocí mysqldump můžete vypsat databáze z hlavní větve. Podrobnosti najdete v tématu věnovaném [výpisu & obnovení](concepts-migrate-dump-restore.md). Není nutné vypsat knihovnu MySQL a knihovnu testů.

1. Nastavení hlavního serveru na režim pro čtení a zápis

   Jakmile je databáze v režimu dumpingu, změňte hlavní server MySQL zpátky na režim pro čtení/zápis.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

1. Obnovit soubor s výpisem paměti na nový server

   Obnovte soubor s výpisem paměti na server vytvořený ve službě Azure Database for MySQL. Informace o tom, jak obnovit soubor s výpisem paměti na server MySQL, najdete v tématu [výpis & obnovení](concepts-migrate-dump-restore.md) . Pokud je soubor s výpisem paměti velký, nahrajte ho do virtuálního počítače v Azure ve stejné oblasti jako server repliky. Obnovte ji na Azure Database for MySQL server z virtuálního počítače.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Spustit Replikace vstupních dat hlavní servery a repliky

1. Nastavit hlavní server

   Všechny funkce Replikace vstupních dat jsou prováděny uloženými procedurami. Všechny postupy najdete v [replikace vstupních dat uložených procedurách](reference-data-in-stored-procedures.md). Uložené procedury lze spustit v prostředí MySQL nebo MySQL Workbench. 

   Pokud chcete propojit dva servery a spustit replikaci, přihlaste se k cílovému serveru repliky ve službě Azure DB for MySQL a nastavte externí instanci jako hlavní server. K tomu je potřeba použít `mysql.az_replication_change_master` uloženou proceduru na serveru Azure DB for MySQL.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: název hostitele hlavního serveru
   - master_user: uživatelské jméno pro hlavní server
   - master_password: heslo pro hlavní server
   - master_log_file: název souboru binárního protokolu se spustí. `show master status`
   - master_log_pos: binární umístění protokolu se spouští. `show master status`
   - master_ssl_ca: kontext certifikátu certifikační autority. Pokud nepoužíváte protokol SSL, předejte prázdný řetězec.
       - Doporučuje se tento parametr předat jako proměnnou. Další informace najdete v následujících příkladech.

   > [!NOTE]
   > Pokud je hlavní server hostovaný na virtuálním počítači Azure, nastavte "povolení přístupu ke službám Azure" na "ZAPNUTo", aby mohly servery hlavní a repliky komunikovat mezi sebou. Toto nastavení se dá změnit z možností **zabezpečení připojení** . Další informace najdete [v tématu Správa pravidel brány firewall pomocí portálu](howto-manage-firewall-using-portal.md) .
      
   **Příklady**
   
   *Replikace s protokolem SSL*
   
   Proměnná `@cert` je vytvořena spuštěním následujících příkazů MySQL: 
   
      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```
   
   Replikace s protokolem SSL je nastavená mezi hlavním serverem hostovaným v doméně "companya.com" a serverem repliky hostovaným v Azure Database for MySQL. Tato uložená procedura se spouští na replice. 
   
      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```
   *Replikace bez SSL*
   
   Replikace bez protokolu SSL je nastavená mezi hlavním serverem hostovaným v doméně "companya.com" a serverem repliky hostovaným v Azure Database for MySQL. Tato uložená procedura se spouští na replice.
   
      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```

1. Filtrování 
 
   Pokud chcete přeskočit replikaci některých tabulek z hlavní větve, aktualizujte `replicate_wild_ignore_table` parametr serveru na serveru repliky. Pomocí čárkami odděleného seznamu lze zadat více než jeden vzor tabulky.

   Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) . 
    
   Pokud chcete aktualizovat parametr, můžete použít [Azure Portal](howto-server-parameters.md) nebo [Azure CLI](howto-configure-server-parameters-using-cli.md).

1. Spustit replikaci

   `mysql.az_replication_start`Pro zahájení replikace zavolejte uloženou proceduru.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Zkontroluje stav replikace.

   Voláním [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) příkazu na serveru repliky zobrazte stav replikace.
    
   ```sql
   show slave status;
   ```

   Pokud je stav `Slave_IO_Running` a v `Slave_SQL_Running` hodnotě "Ano" a hodnota `Seconds_Behind_Master` je "0", replikace funguje dobře. `Seconds_Behind_Master` Určuje, jak pozdě je replika. Pokud hodnota není "0", znamená to, že replika zpracovává aktualizace. 

## <a name="other-stored-procedures"></a>Jiné uložené procedury

### <a name="stop-replication"></a>Zastavení replikace

Pokud chcete zastavit replikaci mezi hlavním serverem a serverem repliky, použijte následující uloženou proceduru:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Odebrat vztah replikace

Chcete-li odebrat vztah mezi hlavním serverem a serverem repliky, použijte následující uloženou proceduru:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Přeskočit chybu replikace

Pokud chcete přeskočit chybu replikace a pokračovat v replikaci, použijte následující uloženou proceduru:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [replikace vstupních dat](concepts-data-in-replication.md) pro Azure Database for MySQL. 
