---
title: Migrace úloh podregistru Azure HDInsight 3,6 do HDInsight 4,0
description: Naučte se migrovat Apache Hive úlohy ve službě HDInsight 3,6 do HDInsight 4,0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/13/2019
ms.openlocfilehash: 26dfe8d134f9f38d8272895583ba2eff614d78e4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308380"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrace úloh podregistru Azure HDInsight 3,6 do HDInsight 4,0

Tento dokument ukazuje, jak migrovat úlohy Apache Hive a LLAP v HDInsight 3,6 do HDInsight 4,0. HDInsight 4,0 poskytuje novější podregistr a LLAP funkce, jako například materializovaná zobrazení a ukládání výsledků dotazu do mezipaměti. Při migraci úloh do HDInsight 4,0 můžete použít spoustu nových funkcí podregistru 3, které nejsou k dispozici ve službě HDInsight 3,6.

Tento článek se zabývá následujícími tématy:

* Migrace metadat podregistru do HDInsight 4,0
* Bezpečná migrace tabulek kyselin a non-KYSELých
* Zachování zásad zabezpečení podregistru napříč verzemi HDInsight
* Provádění dotazů a ladění z HDInsight 3,6 na HDInsight 4,0

Jednou z výhod podregistru je schopnost exportovat metadata do externí databáze (označované jako metastore podregistru). **Podregistr metastore** zodpovídá za ukládání statistik tabulky, včetně umístění úložiště tabulky, názvů sloupců a informací o indexu tabulky. HDInsight 3,6 a HDInsight 4,0 vyžadují různá schémata metastore a nemůžou sdílet jeden metastore. Doporučeným způsobem, jak bezpečně upgradovat metastore Hive, je upgradovat kopii místo originálu v aktuálním provozním prostředí. Tento dokument vyžaduje, aby měl původní a nový cluster přístup ke stejnému účtu úložiště. Proto nepokrývá migraci dat do jiné oblasti.

## <a name="migrate-from-external-metastore"></a>Migrace z externích metastore

### <a name="1-run-major-compaction-on-acid-tables-in-hdinsight-36"></a>1. spuštění hlavní komprimace v tabulkách KYSELosti ve službě HDInsight 3,6

Tabulky kyselin HDInsight 3,6 a HDInsight 4,0 rozumějí rozdíly v KYSELosti odlišně. Jediná akce požadovaná před migrací je spuštění hlavní komprimace proti každé tabulce KYSELosti v clusteru 3,6. Podrobnosti o komprimaci najdete v [příručce k jazyku pro podregistr](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) .

### <a name="2-copy-sql-database"></a>2. kopírování SQL Database
Vytvořte novou kopii vašich externích metastore. Pokud používáte externí metastore, je jedním z bezpečných a snadných způsobů, jak vytvořit kopii metastore, je [obnovit databázi](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore) s jiným názvem pomocí `RESTORE` funkce.  Další informace o připojení externích metastore ke clusteru HDInsight najdete v tématu [použití externích úložišť metadat ve službě Azure HDInsight](../hdinsight-use-external-metadata-stores.md) .

### <a name="3-upgrade-metastore-schema"></a>3. upgrade schématu metastore
Po dokončení **kopírování** metastore spusťte skript upgradu schématu v [akci skriptu](../hdinsight-hadoop-customize-cluster-linux.md) v existujícím clusteru HDInsight 3,6 a upgradujte nový metastore na schéma pro podregistr 3. (Tento krok nevyžaduje připojení nového metastore ke clusteru.) To umožňuje připojení databáze jako HDInsight 4,0 metastore.

Použijte hodnoty v tabulce níže. Nahraďte `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` odpovídajícími hodnotami pro metastore Hive **kopie**oddělené mezerami. Při zadávání názvu SQL serveru nezahrnujte ". database.windows.net".

|Vlastnost | Hodnota |
|---|---|
|Typ skriptu|– Vlastní|
|Name|Upgrade podregistru|
|Identifikátor URI skriptu bash|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Typ (typy) uzlů|Head|
|Parametry|SQLSERVER HESLO K UŽIVATELSKÉMU JMÉNU \ DATABASENAME|

> [!Warning]  
> Upgrade, který převede schéma metadat HDInsight 3,6 na schéma HDInsight 4,0, nejde vrátit zpět.

Upgrade můžete ověřit spuštěním následujícího dotazu SQL na databázi:

```sql
select * from dbo.version
```

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. nasazení nového clusteru HDInsight 4,0

1. Zadejte upgradované metastore jako metastore Hive nového clusteru.

1. Skutečná data z tabulek však nejsou přístupná, dokud cluster nemá přístup k potřebným účtům úložiště.
Ujistěte se, že účty úložiště tabulek podregistru v clusteru HDInsight 3,6 jsou určené jako primární nebo sekundární účty úložiště nového clusteru HDInsight 4,0.
Další informace o přidávání účtů úložiště do clusterů HDInsight najdete v tématu [Přidání dalších účtů úložiště do služby HDInsight](../hdinsight-hadoop-add-storage.md).

### <a name="5-complete-migration-with-a-post-upgrade-tool-in-hdinsight-40"></a>5. dokončení migrace pomocí nástroje po upgradu ve službě HDInsight 4,0

Ve výchozím nastavení musí být spravované tabulky v HDInsight 4,0 standardně kompatibilní s KYSELINou. Po dokončení migrace metastore spusťte nástroj po upgradu a zpřístupněte dříve nekompatibilní tabulky, které nejsou v KYSELINě kompatibilní s clusterem HDInsight 4,0. Tento nástroj bude platit následující převod:

|3,6 |4,0 |
|---|---|
|Externí tabulky|Externí tabulky|
|Spravované tabulky bez KYSELosti|Externí tabulky s vlastností external. Table. vyprázdnění = true|
|Tabulky spravované KYSELINou|Tabulky spravované KYSELINou|

Spusťte nástroj po upgradu podregistru z clusteru HDInsight 4,0 pomocí prostředí SSH:

1. Připojte se ke clusteru hlavnímu uzlu pomocí SSH. Pokyny najdete v tématu [připojení ke službě HDInsight pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) .
1. Spusťte prostředí přihlášení jako uživatel podregistru spuštěním `sudo su - hive`
1. Z prostředí spusťte následující příkaz.

    ```bash
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
    ```

Po dokončení nástroje bude Sklad v podregistru připraven pro HDInsight 4,0.

## <a name="migrate-from-internal-metastore"></a>Migrace z interního metastore

Pokud cluster HDInsight 3,6 používá interní metastore Hive, postupujte podle následujících kroků a spusťte skript, který generuje dotazy na podregistr pro export definic objektů z metastore.

Clustery HDInsight 3,6 a 4,0 musí používat stejný účet úložiště.

> [!NOTE]
>
> * V případě tabulek pro KYSELost se vytvoří nová kopie dat pod tabulkou.
>
> * Tento skript podporuje migraci databází podregistru, tabulek a oddílů pouze. Očekává se, že se zkopírují další objekty metadat, jako jsou zobrazení, UDF a omezení tabulek.
>
> * Po dokončení tohoto skriptu se předpokládá, že původní cluster se už nebude používat pro přístup ke kterékoli z tabulek nebo databází, na které se odkazuje ve skriptu.
>
> * Všechny spravované tabulky se stanou transakčními v HDInsight 4,0. Volitelně můžete zachovat netransakční tabulku tak, že data exportujete do externí tabulky pomocí vlastnosti external. Table. vyprázdnění = true. Příklad:
>
>    ```SQL
>    create table tablename_backup like tablename;
>    insert overwrite table tablename_backup select * from tablename;
>    create external table tablename_tmp like tablename;
>    insert overwrite table tablename_tmp select * from tablename;
>    alter table tablename_tmp set tblproperties('external.table.purge'='true');
>    drop table tablename;
>    alter table tablename_tmp rename to tablename;
>    ```

1. Připojte se ke clusteru HDInsight 3,6 pomocí [klienta Secure Shell (SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Z otevřené relace SSH stáhněte následující soubor skriptu, který vygeneruje soubor s názvem **alltables. HQL**.

    ```bash
    wget https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/exporthive_hdi_3_6.sh
    chmod 755 exporthive_hdi_3_6.sh
    ```

    * Pro běžný cluster HDInsight bez protokolu ESP stačí provést příkaz `exporthive_hdi_3_6.sh` .

    * Pro cluster s protokolem ESP, kinit a upravte argumenty na Beeline: spusťte následující příkaz definující uživatele a doménu pro uživatele Azure AD s úplnými oprávněními pro podregistr.

        ```bash
        USER="USER"  # replace USER
        DOMAIN="DOMAIN"  # replace DOMAIN
        DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
        kinit "$USER@$DOMAIN_UPPER"
        ```

        ```bash
        hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
        BEE_CMD="beeline -u 'jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http' -n "$USER@$DOMAIN" --showHeader=false --silent=true --outputformat=tsv2 -e"
        ./exporthive_hdi_3_6.sh "$BEE_CMD"
        ```

1. Ukončete relaci SSH. Pak zadejte příkaz SCP pro stažení **alltables. HQL** lokálně.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Nahrajte **alltables. HQL** do *nového* clusteru HDInsight.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Pak pomocí SSH se připojte k *novému* clusteru HDInsight 4,0. Spusťte následující kód z relace SSH do tohoto clusteru:

    Bez protokolu ESP:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -f alltables.hql
    ```

    S protokolem ESP:

    ```bash
    USER="USER"  # replace USER
    DOMAIN="DOMAIN"  # replace DOMAIN
    DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
    kinit "$USER@$DOMAIN_UPPER"
    ```

    ```bash
    hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
    beeline -u "jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http" -n "$USER@$DOMAIN" -f alltables.hql
    ```

Nástroj po upgradu pro externí migraci metastore se tady netýká, protože spravované tabulky bez KYSELosti ze služby HDInsight 3,6 se převádějí na tabulky spravované na základě kyseliny v HDInsight 4,0.

> [!Important]  
> Spravované tabulky v HDInsight 4,0 (včetně tabulek migrovaných z 3,6) nemají být dostupné pro jiné služby nebo aplikace, včetně clusterů HDInsight 3,6.

## <a name="secure-hive-across-hdinsight-versions"></a>Zabezpečení podregistru napříč verzemi HDInsight

Od HDInsight 3,6 se HDInsight integruje s Azure Active Directory pomocí HDInsight Balíček zabezpečení podniku (ESP). ESP používá protokol Kerberos a Apache Ranger ke správě oprávnění konkrétních prostředků v rámci clusteru. Zásady Ranger nasazené pro podregistr v HDInsight 3,6 se dají migrovat do HDInsight 4,0 pomocí následujících kroků:

1. V clusteru HDInsight 3,6 přejděte na panel Service Manager Ranger.
2. Přejděte do zásady s názvem **podregistr** a exportujte zásadu do souboru JSON.
3. Zajistěte, aby všichni uživatelé uvedení v seznamu JSON exportovaných zásad existovali v novém clusteru. Pokud se na uživatele odkazuje v zásadách JSON, ale v novém clusteru neexistuje, přidejte ho do nového clusteru nebo odeberte odkaz ze zásady.
4. V clusteru HDInsight 4,0 přejděte na panel **Service Manager Ranger** .
5. Přejděte do části s názvem **podregistr** a importujte JSON zásad Ranger z kroku 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Zkontrolujte kompatibilitu a upravte kódy podle potřeby v testovací aplikaci.

Při migraci úloh, jako jsou třeba existující programy a dotazy, si přečtěte poznámky k verzi a dokumentaci, kde najdete změny, a v případě potřeby použijte změny. Pokud cluster HDInsight 3,6 používá sdílenou technologii Spark a metastore Hive, je vyžadována [Další konfigurace pomocí konektoru datového skladu](./apache-hive-warehouse-connector.md) .

## <a name="deploy-new-app-for-production"></a>Nasazení nové aplikace pro produkční prostředí

Chcete-li přepnout na nový cluster, například můžete nainstalovat novou klientskou aplikaci a použít ji jako nové provozní prostředí, nebo můžete upgradovat existující klientskou aplikaci a přejít na HDInsight 4,0.

## <a name="switch-hdinsight-40-to-the-production"></a>Přepněte HDInsight 4,0 na produkční prostředí.

Pokud byly při testování v metastore vytvořeny rozdíly, budete muset změny před přepnutím aktualizovat. V takovém případě můžete exportovat & import metastore a pak upgradovat znovu.

## <a name="remove-the-old-production"></a>Odebrat starou produkci

Jakmile ověříte, že je vydání hotové a plně funkční, můžete odebrat verzi 3,6 a předchozí metastore. Před odstraněním prostředí se ujistěte, že je vše migrováno.

## <a name="query-execution-across-hdinsight-versions"></a>Provádění dotazů v rámci verzí HDInsight

Existují dva způsoby, jak spustit a ladit dotazy na podregistry/LLAP v clusteru HDInsight 3,6. HiveCLI poskytuje prostředí příkazového řádku a zobrazení [tez/zobrazení podregistru](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-use-hive-ambari-view) nabízí pracovní postup založený na grafickém uživatelském rozhraní.

V HDInsight 4,0 byl HiveCLI nahrazen Beeline. Zobrazení tez/podregistr poskytuje pracovní postup založený na grafickém uživatelském rozhraní. HiveCLI je klient Thrift pro Hiveserver 1 a Beeline je JDBC klient, který poskytuje přístup k Hiveserver 2. Beeline lze také použít pro připojení k jakémukoli jinému koncovému bodu databáze kompatibilního s JDBC. Beeline je k dispozici předem v HDInsight 4,0 bez nutnosti instalace.

## <a name="next-steps"></a>Další kroky

* [Oznámení HDInsight 4,0](../hdinsight-version-release.md)
* [Podrobně HDInsight 4,0 s hloubkou](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tabulky s KYSELINou v podregistru 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
