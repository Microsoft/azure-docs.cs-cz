---
title: Migrace zatížení v podregistru do nového účtu v Azure Storage
description: Migrace zatížení v podregistru do nového účtu v Azure Storage
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/11/2020
ms.openlocfilehash: 0d62bebddb7751c168ba2e487b2391a40bbc6e67
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745084"
---
# <a name="hive-workload-migration-to-new-account-in-azure-storage"></a>Migrace zatížení v podregistru do nového účtu v Azure Storage

Naučte se používat akce skriptů ke kopírování tabulek podregistru mezi účty úložiště v HDInsight. To může být užitečné při migraci na [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) .

Postup ručního kopírování jednotlivých tabulek podregistru v HDInsight 4,0 najdete v tématu [Export/Import podregistru](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport).

## <a name="prerequisites"></a>Požadavky

* Nový cluster HDInsight s následujícími konfiguracemi:
  * Výchozí systém souborů je v cílovém účtu úložiště. Viz [použití Azure Storage s clustery Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md).
  * Verze jeho clusteru musí odpovídat zdrojovému clusteru.
  * Používá novou externí databázi metastore Hive. Viz [použití externích úložišť metadat](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation).
* Účet úložiště, který je přístupný jak pro původní, tak pro nové clustery. Další informace najdete v tématu [Přidání dalších účtů úložiště do služby HDInsight](../hdinsight-hadoop-add-storage.md) a [typů a funkcí úložiště](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features) pro povolené sekundární typy úložišť.

    Tady je několik možností:
  * Přidejte cílový účet úložiště do původního clusteru.
  * Přidejte původní účet úložiště do nového clusteru.
  * Přidejte účet zprostředkujícího úložiště do původního i nového clusteru.

## <a name="how-it-works"></a>Jak to funguje

Spustíme akci skriptu pro export tabulek podregistru z původního clusteru do zadaného adresáře HDFS. Viz [akce skriptu na běžícím clusteru](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

Potom spustíme další akci skriptu v novém clusteru, abyste importovali tabulky podregistru z adresáře HDFS.

Skript znovu vytvoří tabulky do výchozího systému souborů nového clusteru. Nativní tabulky budou také kopírovat data v úložišti. Jiné než nativní tabulky budou zkopírovány pouze podle definice. Podrobnosti o jiných než nativních tabulkách najdete v tématu [obslužné rutiny úložiště podregistru](https://cwiki.apache.org/confluence/display/Hive/StorageHandlers) .

Cesta externích tabulek, které nejsou v adresáři skladu podregistru, bude zachována. Ostatní tabulky budou zkopírovány do výchozí cesty k podregistru cílového clusteru. Podívejte se na vlastnosti podregistru `hive.metastore.warehouse.external.dir` a `hive.metastore.warehouse.dir` .

Skripty nebudou zachovávat oprávnění vlastních souborů v cílovém clusteru.

> [!NOTE]
>
> Tato příručka podporuje kopírování objektů metadat vztahujících se k databázím podregistru, tabulkám a oddílům. Další objekty metadat je třeba znovu vytvořit ručně.
>
> * `Views`V případě podporuje podregistr `SHOW VIEWS` příkaz jako podregistr 2.2.0 ve službě HDInsight 4,0. Použijte `SHOW CREATE TABLE` pro definici zobrazení. V případě starších verzí podregistru je nutné zadat dotaz na databázi SQL metastore a zobrazit zobrazení.
> * Pro `Materialized Views` použijte příkazy, `SHOW MATERIALIZED VIEWS` `DESCRIBE FORMATTED` a `CREATE MATERIALIZED VIEW` . Podrobnosti najdete v tématu [materializovaná zobrazení](https://cwiki.apache.org/confluence/display/Hive/Materialized+views) .
> * Pro `Constraints` podporuje 2.1.0 podregistru v HDInsight 4,0, použijte `DESCRIBE EXTENDED` k omezení seznamu pro tabulku a použijte `ALTER TABLE` k přidání omezení. Podrobnosti najdete v tématu [ALTER TABLE Constraints](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTableConstraints) .

## <a name="copy-hive-tables"></a>Kopírovat tabulky podregistru

1. Použijte akci skriptu exportovat v původním clusteru s následujícími poli.

    Tato akce vygeneruje a spustí zprostředkující skripty podregistru. Budou uloženy v zadaném `<hdfs-export-path>` .

    Volitelně můžete použít `--run-script=false` k jejich přizpůsobení před ručním spuštěním.

    |Vlastnost | Hodnota |
    |---|---|
    |Identifikátor URI skriptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/export-hive-data-v01.sh`|
    |Typ (typy) uzlů|Head|
    |Parametry|`<hdfs-export-path>` `--run-script`|

    ```sh
    usage: generate Hive export and import scripts and export Hive data to specified HDFS path
           [--run-script={true,false}]
           hdfs-export-path

    positional arguments:

        hdfs-export-path      remote HDFS directory to write export data to

    optional arguments:
        --run-script={true,false}
                            whether to execute the generated Hive export script
                            (default: true)
    ```

2. Po úspěšném dokončení exportu použijte akci skriptu import na novém clusteru s následujícími poli.

    |Vlastnost | Hodnota |
    |---|---|
    |Identifikátor URI skriptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/import-hive-data-v01.sh`|
    |Typ (typy) uzlů|Head|
    |Parametry|`<hdfs-export-path>`|

    ```sh
    usage: download Hive import script from specified HDFS path and execute it
           hdfs-export-path

    positional arguments:

      hdfs-export-path      remote HDFS directory to download Hive import script from

    ```

## <a name="verification"></a>Ověření

Stáhněte a spusťte skript jako uživatel root [`hive_contents.sh`](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_contents.sh) na primárním uzlu každého clusteru a porovnejte obsah výstupního souboru `/tmp/hive_contents.out` . Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="cleanup-additional-storage-usage"></a>Vyčištění dalšího využití úložiště

Po dokončení a ověření migrace úložiště můžete odstranit data v zadané cestě pro export HDFS.

Možnost slouží k použití příkazu HDFS `hdfs dfs -rm -R` .

## <a name="option-reduce-additional-storage-usage"></a>Možnost: Snižte využití dalšího úložiště.

Akce exportovat skript pravděpodobně zdvojnásobí využití úložiště z důvodu podregistru. Je ale možné omezit využití dalšího úložiště při migraci ručně, jedné databáze nebo tabulky.

1. Zadejte `--run-script=false` , chcete-li přeskočit spuštění vygenerovaného skriptu podregistru. Skripty exportu a importu podregistru se v cestě pro export pořád uloží.

2. Spustit fragmenty kódu databáze exportovat a importovat skripty – podle databáze nebo tabulkové tabulky, ručně vyčistit cestu exportu po každé migrované databázi nebo tabulce.

## <a name="next-steps"></a>Další kroky

* [Azure Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Použití externích úložišť metadat](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)
* [Typy a funkce úložiště](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features)
* [Akce skriptu na běžícím clusteru](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
