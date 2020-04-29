---
title: Migrace úloh podregistru Azure HDInsight 3,6 do HDInsight 4,0
description: Naučte se migrovat Apache Hive úlohy ve službě HDInsight 3,6 do HDInsight 4,0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: ec96189185a06c1fcbd95eed6216ade47f3089c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79214647"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrace úloh podregistru Azure HDInsight 3,6 do HDInsight 4,0

V tomto dokumentu se dozvíte, jak migrovat úlohy Apache Hive a LLAP v HDInsight 3,6 do HDInsight 4,0. HDInsight 4,0 poskytuje novější podregistr a LLAP funkce, jako například materializovaná zobrazení a ukládání výsledků dotazu do mezipaměti. Při migraci úloh do HDInsight 4,0 můžete použít spoustu nových funkcí podregistru 3, které nejsou k dispozici ve službě HDInsight 3,6.

Tento článek se zabývá následujícími tématy:

* Migrace metadat podregistru do HDInsight 4,0
* Bezpečná migrace tabulek kyselin a non-KYSELých
* Zachování zásad zabezpečení podregistru napříč verzemi HDInsight
* Provádění dotazů a ladění z HDInsight 3,6 na HDInsight 4,0

Jednou z výhod podregistru je schopnost exportovat metadata do externí databáze (označované jako metastore podregistru). **Podregistr metastore** zodpovídá za ukládání statistik tabulky, včetně umístění úložiště tabulky, názvů sloupců a informací o indexu tabulky. Schéma databáze metastore se mezi verzemi podregistru liší. Doporučeným způsobem, jak bezpečně upgradovat metastore Hive, je vytvořit kopii a upgradovat kopii místo aktuálního provozního prostředí.

## <a name="copy-metastore"></a>Kopírovat metastore

HDInsight 3,6 a HDInsight 4,0 vyžadují různá schémata metastore a nemůžou sdílet jeden metastore.

### <a name="external-metastore"></a>Externí metastore

Vytvořte novou kopii vašich externích metastore. Pokud používáte externí metastore, je jedním z bezpečných a snadných způsobů, jak vytvořit kopii metastore, je [obnovit databázi](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) s jiným názvem pomocí funkce SQL Database obnovení.  Další informace o připojení externích metastore ke clusteru HDInsight najdete v tématu [použití externích úložišť metadat ve službě Azure HDInsight](../hdinsight-use-external-metadata-stores.md) .

### <a name="internal-metastore"></a>Interní metastore

Pokud používáte interní metastore, můžete pomocí dotazů exportovat definice objektů v metastore Hive a importovat je do nové databáze.

Po dokončení tohoto skriptu se předpokládá, že původní cluster se už nebude používat pro přístup ke kterékoli z tabulek nebo databází, na které se odkazuje ve skriptu.

> [!NOTE]
> V případě tabulek pro KYSELost se vytvoří nová kopie dat pod tabulkou.

1. Připojte se ke clusteru HDInsight pomocí [klienta Secure Shell (SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Připojte se k HiveServer2 pomocí [klienta Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) z otevřené relace SSH zadáním následujícího příkazu:

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; 
    do
        echo "Scanning Database: $d"
        echo "create database if not exists $d; use $d;" >> alltables.hql; 
        for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"`;
        do
            echo "Copying Table: $t"
            ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`;

            echo "$ddl;" >> alltables.hql;
            lowerddl=$(echo $ddl | awk '{print tolower($0)}')
            if [[ $lowerddl == *"'transactional'='true'"* ]]; then
                if [[ $lowerddl == *"partitioned by"* ]]; then
                    # partitioned
                    raw_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "CREATE TABLE .*" | cut -d"(" -f2- | cut -f1 -d")" | sed 's/`//g');
                    ptn_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "PARTITIONED BY .*" | cut -f1 -d")" | cut -d"(" -f2- | sed 's/`//g');
                    final_cols=$(echo "(" $raw_cols "," $ptn_cols ")")

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t $final_cols TBLPROPERTIES ('transactional'='false');";
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    parsed_ptn_cols=$(echo $ptn_cols| sed 's/ [a-z]*,/,/g' | sed '$s/\w*$//g');
                    echo "create table flattened_$t $final_cols;" >> alltables.hql;
                    echo "load data inpath '$dir' into table flattened_$t;" >> alltables.hql;
                    echo "insert into $t partition($parsed_ptn_cols) select * from flattened_$t;" >> alltables.hql;
                    echo "drop table flattened_$t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                else
                    # not partitioned
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t like $t TBLPROPERTIES ('transactional'='false');";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    echo "load data inpath '$dir' into table $t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                fi
            fi
            echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t;" >> alltables.hql;
        done;
    done
    ```

    Tento příkaz vygeneruje soubor s názvem **alltables. HQL**.

1. Ukončete relaci SSH. Pak zadejte příkaz SCP pro stažení **alltables. HQL** lokálně.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Nahrajte **alltables. HQL** do *nového* clusteru HDInsight.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Pak se pomocí SSH připojte k *novému* clusteru HDInsight. Z relace SSH spusťte následující kód:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.hql
    ```


## <a name="upgrade-metastore"></a>Upgradovat metastore

Po dokončení **kopírování** metastore spusťte skript upgradu schématu v [akci skriptu](../hdinsight-hadoop-customize-cluster-linux.md) v existujícím clusteru HDInsight 3,6 a upgradujte nový metastore na schéma pro podregistr 3. To umožňuje připojení databáze jako HDInsight 4,0 metastore.

Použijte hodnoty v tabulce níže. Nahraďte `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` odpovídajícími hodnotami **kopírovaných** metastore Hive oddělených mezerami. Při zadávání názvu SQL serveru nezahrnujte ". database.windows.net".

|Vlastnost | Hodnota |
|---|---|
|Typ skriptu|– Vlastní|
|Název|Upgrade podregistru|
|Identifikátor URI skriptu bash|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Typ (typy) uzlů|Head|
|Parametry|SQLSERVER HESLO K UŽIVATELSKÉMU JMÉNU \ DATABASENAME|

> [!Warning]  
> Upgrade, který převede schéma metadat HDInsight 3,6 na schéma HDInsight 4,0, nejde vrátit zpět.

Upgrade můžete ověřit spuštěním následujícího dotazu SQL na databázi:

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrace tabulek podregistru do HDInsight 4,0

Po dokončení předchozí sady kroků pro migraci metastore do prostředí HDInsight 4,0 budou tabulky a databáze zaznamenané v metastore viditelné v rámci clusteru HDInsight 4,0 spuštěním `show tables` nebo `show databases` z clusteru. Informace o spuštění dotazů v clusterech HDInsight 4,0 najdete v tématu [provádění dotazů napříč verzemi HDInsight](#query-execution-across-hdinsight-versions) .

Skutečná data z tabulek však nejsou přístupná, dokud cluster nemá přístup k potřebným účtům úložiště. Abyste se ujistili, že cluster HDInsight 4,0 má přístup ke stejným datům jako starý cluster HDInsight 3,6, proveďte následující kroky:

1. Určete účet úložiště Azure pro tabulku nebo databázi.

1. Pokud je už cluster HDInsight 4,0 spuštěný, připojte účet Azure Storage ke clusteru přes Ambari. Pokud jste ještě nevytvořili cluster HDInsight 4,0, ujistěte se, že je účet úložiště Azure zadaný jako primární nebo sekundární účet úložiště clusteru. Další informace o přidávání účtů úložiště do clusterů HDInsight najdete v tématu [Přidání dalších účtů úložiště do služby HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>Nasazení nové HDInsight 4,0 a připojení k novému metastore

Po dokončení upgradu schématu nasaďte nový cluster HDInsight 4,0 a propojte upgradovanou metastore. Pokud jste už nasadili 4,0, nastavte ho tak, abyste se mohli připojit k metastore z Ambari.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>Spuštění skriptu migrace schématu z HDInsight 4,0

Tabulky jsou v HDInsight 3,6 a HDInsight 4,0 zpracovávané jinak. Z tohoto důvodu nemůžete sdílet stejné tabulky pro clustery s různými verzemi. Pokud chcete použít HDInsight 3,6 ve stejnou dobu jako HDInsight 4,0, musíte mít samostatné kopie dat pro každou verzi.

Zatížení vašeho podregistru může zahrnovat kombinaci KYSELých a nekyselých tabulek. Jeden klíčový rozdíl mezi podregistrem v HDInsight 3,6 (podregistr 2) a podregistr v HDInsight 4,0 (podregistr 3) je dodržování předpisů pro tabulky v KYSELINě. Ve službě HDInsight 3,6, povolení dodržování předpisů v podregistru vyžaduje další konfiguraci, ale ve výchozím nastavení jsou v tabulkách HDInsight 4,0 kompatibilní s KYSELINou. Jediná akce nutná před migrací je spuštění hlavní komprimace proti tabulce kyselin v clusteru 3,6. V zobrazení podregistru nebo v Beeline spusťte následující dotaz:

```sql
alter table myacidtable compact 'major';
```

Tato komprimace je povinná, protože tabulky v HDInsight 3,6 a HDInsight 4,0 porozumět rozdílům v KYSELosti odlišně. Při komprimaci se vynutila čistá břidlica, která zaručuje konzistenci. Část 4 [dokumentace k migraci podregistru](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) obsahuje pokyny pro hromadné komprimaci tabulek pro kyselinu HDInsight 3,6.

Po dokončení kroků migrace a komprimace metastore můžete aktuální datový sklad migrovat. Po dokončení migrace datového skladu pro podregistr bude mít datový sklad HDInsight 4,0 následující vlastnosti:

|3,6 |4.0 |
|---|---|
|Externí tabulky|Externí tabulky|
|Netransakční spravované tabulky|Externí tabulky|
|Transakční spravované tabulky|Spravované tabulky|

Před provedením migrace možná budete muset upravit vlastnosti datového skladu. Například pokud očekáváte, že k některé tabulce budou mít přistup třetí strana (například cluster HDInsight 3,6), musí být tato tabulka po dokončení migrace externě externí. Ve službě HDInsight 4,0 jsou všechny spravované tabulky transakční. Proto jsou spravované tabulky v HDInsight 4,0 k dispozici pouze clusterům HDInsight 4,0.

Po správném nastavení vlastností tabulky spusťte nástroj pro migraci datového skladu z jednoho z hlavních clusteru pomocí prostředí SSH:

1. Připojte se ke clusteru hlavnímu uzlu pomocí SSH. Pokyny najdete v tématu [připojení ke službě HDInsight pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) .
1. Spusťte prostředí přihlášení jako uživatel podregistru spuštěním`sudo su - hive`
1. Pomocí příkazu `ls /usr/hdp`určete verzi zásobníku datové platformy. Tím se zobrazí řetězec verze, který byste měli použít v příkazu Next.
1. Z prostředí spusťte následující příkaz. Nahraďte `STACK_VERSION` řetězcem verze z předchozího kroku:

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

Po dokončení nástroje pro migraci bude váš sklad podregistru připravený pro HDInsight 4,0.

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

Existují dva způsoby, jak spustit a ladit dotazy na podregistry/LLAP v clusteru HDInsight 3,6. HiveCLI poskytuje prostředí příkazového řádku a zobrazení tez/zobrazení podregistru nabízí pracovní postup založený na grafickém uživatelském rozhraní.

V HDInsight 4,0 byl HiveCLI nahrazen Beeline. HiveCLI je klient Thrift pro Hiveserver 1 a Beeline je JDBC klient, který poskytuje přístup k Hiveserver 2. Beeline je možné použít také pro připojení k jakémukoli jinému koncovému bodu databáze kompatibilního s JDBC. Beeline je k dispozici předem v HDInsight 4,0 bez nutnosti instalace.

V HDInsight 3,6 je klient grafického uživatelského rozhraní pro interakci se serverem podregistru Ambari zobrazením podregistru. HDInsight 4,0 se nedodává s Ambari zobrazením. Zákazníkům jsme poskytli způsob, jak používat data Analytics Studio (DAS), což není základní služba HDInsight. DAS se nedodává s clustery HDInsight předem a není oficiálně podporovaný balíček. DAS se ale na cluster dá nainstalovat pomocí [akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md) takto:

|Vlastnost | Hodnota |
|---|---|
|Typ skriptu|– Vlastní|
|Název|DAS|
|Identifikátor URI skriptu bash|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Typ (typy) uzlů|Head|

Počkejte 10 až 15 minut a pak spusťte data Analytics Studio pomocí této adresy URL: `https://CLUSTERNAME.azurehdinsight.net/das/`.

Před přístupem DAS může být potřeba aktualizovat uživatelské rozhraní Ambari nebo restartovat všechny součásti Ambari.

Pokud se po instalaci DAS nezobrazí dotazy, které jste spustili v prohlížeči dotazů, proveďte následující kroky:

1. Nastavte konfigurace pro podregistr, tez a DAS, jak je popsáno v [této příručce pro řešení potíží s instalací Das](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Ujistěte se, že následující konfigurace adresáře úložiště Azure jsou objekty blob stránky a že jsou uvedené v části `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. V obou hlavních restartujte HDFS, podregistr, tez a DAS.

## <a name="next-steps"></a>Další kroky

* [Oznámení HDInsight 4,0](../hdinsight-version-release.md)
* [Podrobně HDInsight 4,0 s hloubkou](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tabulky s KYSELINou v podregistru 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
