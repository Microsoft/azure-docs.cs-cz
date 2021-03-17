---
title: Migrace úloh podregistru Azure HDInsight 3,6 do HDInsight 4,0
description: Naučte se migrovat Apache Hive úlohy ve službě HDInsight 3,6 do HDInsight 4,0.
author: kevxmsft
ms.author: kevx
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: b13e8e088eff95071247a53ad1a4a18879f94053
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742190"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrace úloh podregistru Azure HDInsight 3,6 do HDInsight 4,0

HDInsight 4,0 má několik výhod oproti službě HDInsight 3,6. Tady je [Přehled toho, co je nového ve službě HDInsight 4,0](../hdinsight-version-release.md).

Tento článek popisuje kroky migrace úloh podregistru z HDInsight 3,6 do 4,0, včetně

* metastore Hive kopírování a aktualizace schématu
* Bezpečná migrace pro kompatibilitu kyselin
* Zachování zásad zabezpečení pro podregistr

Nové a staré clustery HDInsight musí mít přístup ke stejným účtům úložiště.

Migrace tabulek podregistru do nového účtu úložiště se musí udělat v samostatném kroku. Viz [migrace podregistru mezi účty úložiště](./hive-migration-across-storage-accounts.md).

## <a name="steps-to-upgrade"></a>Postup upgradu

### <a name="1-prepare-the-data"></a>1. Příprava dat

* HDInsight 3,6 standardně nepodporuje tabulky KYSELosti. Pokud jsou k dispozici tabulky s KYSELou, na nich se ale spustí "hlavní" komprimace. Podrobnosti o komprimaci najdete v [příručce k jazyku pro podregistr](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) .

* Pokud používáte [Azure Data Lake Storage Gen1](../overview-data-lake-storage-gen1.md), umístění tabulek podregistru jsou nejspíš závislá na KONFIGURACÍch HDFS v clusteru. Spuštěním následující akce skriptu zajistěte, aby tato umístění byla v ostatních clusterech přenosná. Viz [akce skriptu na běžícím clusteru](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

    |Vlastnost | Hodnota |
    |---|---|
    |Identifikátor URI skriptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
    |Typ (typy) uzlů|Head|
    |Parametry||

### <a name="2-copy-the-sql-database"></a>2. Zkopírujte databázi SQL.

* Pokud cluster používá výchozí metastore Hive, podle tohoto [Průvodce](./hive-default-metastore-export-import.md) exportujte metadata do externí metastore. Pak vytvořte kopii externího metastore pro upgrade.

* Pokud cluster používá externí metastore Hive, vytvořte jeho kopii. Mezi možnosti patří [Export/Import](../../azure-sql/database/database-export.md) a [obnovení k bodu v čase](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore).

### <a name="3-upgrade-the-metastore-schema"></a>3. upgrade schématu metastore

Tento krok používá [`Hive Schema Tool`](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool) od HDInsight 4,0 k upgradu schématu metastore.

> [!Warning]
> Tento krok není vratný. Tuto rutinu spouštějte jenom na kopii metastore.

1. Vytvořte dočasný cluster HDInsight 4,0 pro přístup k podregistru 4,0 `schematool` . Pro tento krok můžete použít [výchozí metastore Hive](../hdinsight-use-external-metadata-stores.md#default-metastore) .

1. Z clusteru HDInsight 4,0 spusťte `schematool` upgrade cíle HDInsight 3,6 metastore:

    ```sh
    SERVER='servername.database.windows.net'  # replace with your SQL Server
    DATABASE='database'  # replace with your 3.6 metastore SQL Database
    USERNAME='username'  # replace with your 3.6 metastore username
    PASSWORD='password'  # replace with your 3.6 metastore password
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/schematool -upgradeSchema -url "jdbc:sqlserver://$SERVER;databaseName=$DATABASE;trustServerCertificate=false;encrypt=true;hostNameInCertificate=*.database.windows.net;" -userName "$USERNAME" -passWord "$PASSWORD" -dbType "mssql" --verbose
    ```

    > [!NOTE]
    > Tento nástroj používá klienta `beeline` ke spouštění skriptů SQL v `/usr/hdp/$STACK_VERSION/hive/scripts/metastore/upgrade/mssql/upgrade-*.mssql.sql` .
    >
    > Syntaxe SQL není v těchto skriptech nutně kompatibilní s jinými nástroji klienta. Například [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a [Editor dotazů na webu Azure Portal](../../azure-sql/database/connect-query-portal.md) vyžadují klíčové slovo `GO` za každým příkazem.
    >
    > Pokud se nějaký skript nepovede kvůli kapacitě prostředků nebo časovým limitům transakce, nahorizontální navýšení kapacity SQL Database.

1. Ověřte konečnou verzi s dotazem `select schema_version from dbo.version` .

    Výstup by měl odpovídat následujícímu příkazu bash z clusteru HDInsight 4,0.

    ```bash
    grep . /usr/hdp/$(hdp-select --version)/hive/scripts/metastore/upgrade/mssql/upgrade.order.mssql | tail -n1 | rev | cut -d'-' -f1 | rev
    ```

1. Odstraňte dočasný cluster HDInsight 4,0.

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. nasazení nového clusteru HDInsight 4,0

Vytvořte nový cluster HDInsight 4,0 a [Vyberte upgradovaný metastore Hive](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation) a stejné účty úložiště.

* Nový cluster nepožaduje stejný výchozí systém souborů.

* Pokud metastore obsahuje tabulky nacházející se v několika účtech úložiště, je nutné přidat tyto účty úložiště do nového clusteru pro přístup k těmto tabulkám. Viz [Přidání dalších účtů úložiště do služby HDInsight](../hdinsight-hadoop-add-storage.md).

* Pokud se úlohy podregistru nezdaří kvůli nedostupnosti úložiště, ověřte, že je umístění tabulky v účtu úložiště přidaném do clusteru.

    Pro identifikaci umístění v tabulce použijte následující příkaz podregistru:

    ```sql
    SHOW CREATE TABLE ([db_name.]table_name|view_name);
    ```

### <a name="5-convert-tables-for-acid-compliance"></a>5. Převod tabulek na dodržování předpisů pro KYSELINu

Spravované tabulky musí být kompatibilní s KYSELINou ve službě HDInsight 4,0. Spusťte `strictmanagedmigration` v HDInsight 4,0 a převeďte všechny spravované tabulky bez kyselosti na externí tabulky s vlastností `'external.table.purge'='true'` . Spustit z hlavnímu uzlu:

```bash
sudo su - hive
STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
/usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

## <a name="secure-hive-across-hdinsight-versions"></a>Zabezpečení podregistru napříč verzemi HDInsight

HDInsight se volitelně integruje s Azure Active Directory pomocí HDInsight Balíček zabezpečení podniku (ESP). ESP používá protokol Kerberos a Apache Ranger ke správě oprávnění konkrétních prostředků v rámci clusteru. Zásady Ranger nasazené pro podregistr v HDInsight 3,6 se dají migrovat do HDInsight 4,0 pomocí následujících kroků:

1. V clusteru HDInsight 3,6 přejděte na panel Service Manager Ranger.
2. Přejděte do zásady s názvem **podregistr** a exportujte zásadu do souboru JSON.
3. Zajistěte, aby všichni uživatelé uvedení v seznamu JSON exportovaných zásad existovali v novém clusteru. Pokud se na uživatele odkazuje v zásadách JSON, ale v novém clusteru neexistuje, přidejte ho do nového clusteru nebo odeberte odkaz ze zásady.
4. V clusteru HDInsight 4,0 přejděte na panel **Service Manager Ranger** .
5. Přejděte do části s názvem **podregistr** a importujte JSON zásad Ranger z kroku 2.

## <a name="hive-changes-in-hdinsight-40-that-may-require-application-changes"></a>Změny v podregistru ve službě HDInsight 4,0, které mohou vyžadovat změny aplikace

* V části [Další konfigurace s použitím konektoru datového skladu](./apache-hive-warehouse-connector.md) pro sdílení metastore mezi Sparkem a podregistrum pro tabulky kyselin.

* HDInsight 4,0 používá [autorizaci založenou na úložišti](https://cwiki.apache.org/confluence/display/Hive/Storage+Based+Authorization+in+the+Metastore+Server). Pokud upravíte oprávnění k souboru nebo vytvoříte složky jako jiný uživatel než podregistr, pravděpodobně dojde k chybám v podregistru na základě oprávnění pro úložiště. Chcete-li opravit, udělte `rw-` uživateli přístup. Viz [Příručka oprávnění HDFS](https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

* `HiveCLI` je nahrazen `Beeline` .

Další změny najdete v [oznámení HDInsight 4,0](../hdinsight-version-release.md) .

## <a name="further-reading"></a>Další čtení

* [Oznámení HDInsight 4,0](../hdinsight-version-release.md)
* [Podrobně HDInsight 4,0 s hloubkou](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tabulky s KYSELINou v podregistru 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
