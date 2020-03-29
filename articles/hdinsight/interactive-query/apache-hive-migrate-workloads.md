---
title: Migrace úloh Azure HDInsight 3.6 Hive do HDInsight 4.0
description: Přečtěte si, jak migrovat úlohy Apache Hive na HDInsight 3.6 na HDInsight 4.0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: ec96189185a06c1fcbd95eed6216ade47f3089c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214647"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrace úloh Azure HDInsight 3.6 Hive do HDInsight 4.0

Tento dokument ukazuje, jak migrovat úlohy Apache Hive a LLAP na HDInsight 3.6 na HDInsight 4.0. HDInsight 4.0 poskytuje novější funkce Hive a LLAP, jako jsou materializovaná zobrazení a ukládání výsledků dotazů do mezipaměti. Když migrujete své úlohy do HDInsight 4.0, můžete použít mnoho novějších funkcí Hive 3, které nejsou k dispozici na HDInsight 3.6.

Tento článek se zabývá následujícími tématy:

* Migrace metadat Hive do HDInsight 4.0
* Bezpečná migrace tabulek ACID a non-ACID
* Zachování zásad zabezpečení Hive ve verzích HDInsight
* Provádění dotazů a ladění z HDInsight 3.6 na HDInsight 4.0

Jednou z výhod Hive je možnost exportovat metadata do externí databáze (označované jako Hive Metastore). **Metastore hive** je zodpovědný za ukládání statistik tabulky, včetně umístění úložiště tabulky, názvy sloupců a informace o indexu tabulky. Schéma databáze metaúložiště se liší mezi verzemi Hive. Doporučený způsob, jak bezpečně upgradovat metastore Hive, je vytvořit kopii a inovovat kopii namísto aktuálního produkčního prostředí.

## <a name="copy-metastore"></a>Kopírovat metastore

HDInsight 3.6 a HDInsight 4.0 vyžadují různá schémata metastore a nemohou sdílet jediné metastore.

### <a name="external-metastore"></a>Externí metastore

Vytvořte novou kopii externího metaúložiště. Pokud používáte externí metastore, jedním z bezpečných a snadných způsobů, jak vytvořit kopii metastore je [obnovit databázi](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) s jiným názvem pomocí funkce obnovení databáze SQL.  Další informace o připojení externího metaúložiště k clusteru HDInsight najdete [v tématu Použití externích úložišť metadat v Azure HDInsight.](../hdinsight-use-external-metadata-stores.md)

### <a name="internal-metastore"></a>Interní metastore

Pokud používáte interní metastore, můžete použít dotazy k exportu definic objektů v metastore Hive a importovat je do nové databáze.

Po dokončení tohoto skriptu se předpokládá, že starý cluster již nebude použit pro přístup k žádné z tabulek nebo databází uvedených ve skriptu.

> [!NOTE]
> V případě tabulek ACID bude vytvořena nová kopie dat pod tabulkou.

1. Připojte se ke clusteru HDInsight pomocí [klienta Secure Shell (SSH).](../hdinsight-hadoop-linux-use-ssh-unix.md)

1. Připojte se k HiveServer2 s [klientem Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) z otevřené relace SSH zadáním následujícího příkazu:

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

    Tento příkaz generuje soubor s názvem **alltables.hql**.

1. Ukončete relaci SSH. Poté zadejte příkaz scp a stáhněte soubor **alltables.hql** místně.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Nahrajte **soubor alltables.hql** do *nového* clusteru HDInsight.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Pak se pomocí SSH připojte k *novému* clusteru HDInsight. Z relace SSH spusťte následující kód:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.hql
    ```


## <a name="upgrade-metastore"></a>Upgrade metastore

Po dokončení **kopie** metaúložiště spusťte skript pro upgrade schématu v [akci skriptu](../hdinsight-hadoop-customize-cluster-linux.md) v existujícím clusteru HDInsight 3.6 a upgradujte nové metastore na schéma Hive 3. To umožňuje databázi připojit jako HDInsight 4.0 metastore.

Použijte hodnoty v tabulce níže. Nahraďte `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` příslušné hodnoty pro **zkopírované** metaúložiště Hive, oddělené mezerami. Při zadávání názvu serveru SQL nezahrnte soubor ".database.windows.net".

|Vlastnost | Hodnota |
|---|---|
|Typ skriptu|- Vlastní|
|Name (Název)|Upgrade úlu|
|Bash skript URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Typ uzlu|Head|
|Parametry|SQLSERVERNAME NÁZEV_UŽIVATELSKÉ JMÉNO HESLO|

> [!Warning]  
> Upgrade, který převádí schéma metadat HDInsight 3.6 na schéma HDInsight 4.0, nelze vrátit zpět.

Upgrade můžete ověřit spuštěním následujícího dotazu SQL v databázi:

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrace tabulek Hive do HDInsight 4.0

Po dokončení předchozí sady kroků pro migraci Hive Metastore na HDInsight 4.0, tabulky a databáze zaznamenané v metastore bude `show tables` viditelný `show databases` z v rámci clusteru HDInsight 4.0 spuštěním nebo z clusteru. Informace o spouštění dotazů v clusterech HDInsight 4.0 najdete v [tématu Spouštění dotazů napříč verzemi HDInsightu.](#query-execution-across-hdinsight-versions)

Skutečná data z tabulek však nejsou přístupná, dokud cluster nemá přístup k nezbytným účtům úložiště. Chcete-li zajistit, aby váš cluster HDInsight 4.0 měl přístup ke stejným datům jako starý cluster HDInsight 3.6, proveďte následující kroky:

1. Určete účet úložiště Azure v tabulce nebo databázi.

1. Pokud váš cluster HDInsight 4.0 už běží, připojte účet úložiště Azure ke clusteru přes Ambari. Pokud jste ještě nevytvořili cluster HDInsight 4.0, ujistěte se, že účet úložiště Azure je určen jako primární nebo sekundární účet úložiště clusteru. Další informace o přidávání účtů úložiště do clusterů HDInsight najdete v tématu [Přidání dalších účtů úložiště do HDInsightu](../hdinsight-hadoop-add-storage.md).

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>Nasazení nového HDInsightu 4.0 a připojení k novému metastore

Po dokončení upgradu schématu nasaďte nový cluster HDInsight 4.0 a připojte upgradované metastore. Pokud jste již nasadili 4.0, nastavte jej tak, aby se můžete připojit k metastore z Ambari.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>Spuštění skriptu pro migraci schématu z hdinsightu 4.0

S tabulkami se v HDInsightu 3.6 a HDInsightu 4.0 zachází odlišně. Z tohoto důvodu nelze sdílet stejné tabulky pro clustery různých verzí. Pokud chcete používat HDInsight 3.6 současně s HDInsight 4.0, musíte mít samostatné kopie dat pro každou verzi.

Úloha Hive může obsahovat kombinaci tabulek ACID a non-ACID. Jeden klíčový rozdíl mezi Hive na HDInsight 3.6 (Hive 2) a Hive na HDInsight 4.0 (Hive 3) je acid-compliance pro tabulky. V HDInsight 3.6 povolení hive acid dodržování vyžaduje další konfiguraci, ale v HDInsight 4.0 tabulky jsou standardně acid kompatibilní. Jedinou akcí, která je před migrací vyžadována, je spuštění hlavního zhutnění proti tabulce ACID v clusteru 3.6. V zobrazení Hive nebo z čáry beeline spusťte následující dotaz:

```sql
alter table myacidtable compact 'major';
```

Toto zhutnění je vyžadováno, protože tabulky HDInsight 3.6 a HDInsight 4.0 ACID chápou delta acid odlišně. Komprimace vynucuje čistý štít, který zaručuje konzistenci. Část 4 [dokumentace k migraci Hive](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) obsahuje pokyny pro hromadné zhutnění tabulek HDInsight 3.6 ACID.

Po dokončení kroků migrace a zhutnění metastore můžete migrovat skutečný sklad. Po dokončení migrace skladu Hive bude mít sklad HDInsight 4.0 následující vlastnosti:

|3.6 |4.0 |
|---|---|
|Externí tabulky|Externí tabulky|
|Netransakční spravované tabulky|Externí tabulky|
|Transakční spravované tabulky|Spravované tabulky|

Před provedením migrace může být nutné upravit vlastnosti skladu. Například pokud očekáváte, že některé tabulky budou přístupné třetí stranou (například HDInsight 3.6 clusteru), tato tabulka musí být externí po dokončení migrace. V HDInsight 4.0 jsou všechny spravované tabulky transakční. Spravované tabulky v hdinsightu 4.0 by proto měly být přístupné pouze clusterům HDInsight 4.0.

Jakmile jsou vlastnosti tabulky správně nastaveny, spusťte nástroj pro migraci do skladu Hive z jednoho z hlavových uzlů clusteru pomocí prostředí SSH:

1. Připojte se k hlavnímu uzlu clusteru pomocí SSH. Pokyny najdete [v tématu Připojení k HDInsight pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Spuštění masképrostředí přihlášení jako uživatel Eve spuštěním`sudo su - hive`
1. Určete verzi zásobníku datové `ls /usr/hdp`platformy spuštěním . Zobrazí se řetězec verze, který byste měli použít v dalším příkazu.
1. Proveďte následující příkaz z prostředí. Nahraďte `STACK_VERSION` řetězec verze z předchozího kroku:

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

Po dokončení nástroje pro migraci bude váš sklad Hive připraven pro HDInsight 4.0.

> [!Important]  
> Spravované tabulky v HDInsight 4.0 (včetně tabulek migrovaných z 3.6) by neměly být přístupné jiným službám nebo aplikacím, včetně clusterů HDInsight 3.6.

## <a name="secure-hive-across-hdinsight-versions"></a>Zabezpečit hive ve verzích HDInsight

Vzhledem k tomu, HDInsight 3.6, HDInsight integruje s Azure Active Directory pomocí HDInsight Enterprise Security Package (ESP). ESP používá protokolKerberos a Apache Ranger ke správě oprávnění konkrétních prostředků v rámci clusteru. Zásady rangerů nasazené proti Hive v HDInsightu 3.6 lze migrovat na HDInsight 4.0 pomocí následujících kroků:

1. Přejděte na panel Správce služeb rangerů v clusteru HDInsight 3.6.
2. Přejděte na zásadu s názvem **HIVE** a exportujte ji do souboru json.
3. Ujistěte se, že v novém clusteru existují všichni uživatelé uvedení v json exportované zásady. Pokud je uživatel uveden v json zásady, ale neexistuje v novém clusteru, přidejte uživatele do nového clusteru nebo odeberte odkaz ze zásady.
4. Přejděte na panel **Správce služeb rangerů** v clusteru HDInsight 4.0.
5. Přejděte na zásadu s názvem **HIVE** a importujte zásadu ranger json z kroku 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Kontrola kompatibility a úpravy kódů podle potřeby v testovací aplikaci

Při migraci úloh, jako jsou existující programy a dotazy, zkontrolujte změny v poznámkách k verzi a v dokumentaci a podle potřeby použijte změny. Pokud váš cluster HDInsight 3.6 používá sdílené metastore Spark a Hive, je vyžadována [další konfigurace pomocí konektoru Hive Warehouse Connector.](./apache-hive-warehouse-connector.md)

## <a name="deploy-new-app-for-production"></a>Nasazení nové aplikace pro produkční prostředí

Chcete-li přepnout do nového clusteru, můžete například nainstalovat novou klientskou aplikaci a použít ji jako nové produkční prostředí, nebo můžete upgradovat stávající klientskou aplikaci a přepnout na HDInsight 4.0.

## <a name="switch-hdinsight-40-to-the-production"></a>Přepnutí HDInsight 4.0 do produkčního prostředí

Pokud byly během testování vytvořeny v metaúložišti rozdíly, budete muset změny aktualizovat těsně před přepnutím. V takovém případě můžete exportovat & importovat metastore a potom znovu inovovat.

## <a name="remove-the-old-production"></a>Odebrat starou výrobu

Jakmile potvrdíte, že vydání je kompletní a plně funkční, můžete odebrat verzi 3.6 a předchozí metastore. Před odstraněním prostředí se ujistěte, že je vše migrováno.

## <a name="query-execution-across-hdinsight-versions"></a>Spuštění dotazu ve verzích HDInsight

Existují dva způsoby spouštění a ladění dotazů Hive/LLAP v clusteru HDInsight 3.6. HiveCLI poskytuje prostředí příkazového řádku a zobrazení Tez view/Hive poskytuje pracovní postup založený na grafickém uživatelském rozhraní.

V HDInsight 4.0 byl HiveCLI nahrazen Beeline. HiveCLI je šetrný klient pro Hiveserver 1 a Beeline je klient JDBC, který poskytuje přístup k Hiveserver 2. Beeline lze také použít k připojení k libovolnému jinému koncovému bodu databáze kompatibilnímu s JDBC. Beeline je k dispozici i-of-box na HDInsight 4.0 bez nutnosti instalace.

V HDInsight 3.6 je klientem gui pro interakci se serverem Hive zobrazení Ambari Hive. HDInsight 4.0 není dodáván s Ambari View. Našim zákazníkům jsme poskytli způsob, jak používat Studio Data Analytics Studio (DAS), což není základní služba HDInsight. DAS není dodáván s HDInsight clustery out-of-the-box a není oficiálně podporovaný balíček. DAS však lze nainstalovat do clusteru pomocí [akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md) následujícím způsobem:

|Vlastnost | Hodnota |
|---|---|
|Typ skriptu|- Vlastní|
|Name (Název)|Das|
|Bash skript URI|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Typ uzlu|Head|

Počkejte 10 až 15 minut a potom spusťte studio Data Analytics pomocí této adresy URL: `https://CLUSTERNAME.azurehdinsight.net/das/`.

Před přístupem k das může být vyžadována aktualizace uj.

Pokud po instalaci DAS nevidíte dotazy, které jste spouštěli v prohlížeči dotazů, postupujte takto:

1. Nastavte konfigurace pro Hive, Tez a DAS, jak je popsáno v [této příručce pro řešení potíží s instalací DAS](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Ujistěte se, že následující konfigurace adresáře úložiště Azure jsou objekty `fs.azure.page.blob.dirs`BLOB stránky a že jsou uvedené v části :
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Restartujte hdfs, hive, tez a das na obou hlavových uzlech.

## <a name="next-steps"></a>Další kroky

* [Oznámení HDInsight 4.0](../hdinsight-version-release.md)
* [HDInsight 4.0 hluboký ponor](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 KYSELÉ tabulky](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
