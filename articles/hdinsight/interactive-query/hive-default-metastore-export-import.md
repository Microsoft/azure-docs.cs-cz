---
title: Migrace výchozích metastore Hive do externích metastore ve službě Azure HDInsight
description: Migrace výchozích metastore Hive do externích metastore ve službě Azure HDInsight
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 4a0258d5e448c59baa1cd63e98058fe7116a8485
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566111"
---
# <a name="migrate-default-hive-metastore-db-to-external-metastore-db"></a>Migrovat výchozí metastore Hive DB do externí databáze metastore

V tomto článku se dozvíte, jak migrovat metadata z [výchozí METASTORE DB](../hdinsight-use-external-metadata-stores.md#default-metastore) pro podregistr na externí SQL Database v HDInsight. 

## <a name="why-migrate-to-external-metastore-db"></a>Proč migrovat do externí databáze metastore

* Výchozí metastore DB je omezená na Basic SKU a nemůže zpracovávat úlohy produkčního škálování.

* Externí metastore DB umožňuje zákazníkům horizontální škálování výpočetních prostředků podregistru přidáním nových clusterů HDInsight, které sdílejí stejnou databázi metastore.

* V případě migrace HDInsight 3,6 až 4,0 je nutné před upgradem verze schématu podregistru migrovat metadata do externí služby metastore DB. Viz [migrace úloh z HDInsight 3,6 do hdinsight 4,0](./apache-hive-migrate-workloads.md).

Vzhledem k tomu, že výchozí metastore DB má omezené výpočetní kapacitu, doporučujeme při migraci metadat nepoužívat z dalších úloh v clusteru nízké využití.

Zdrojové a cílové databáze musí používat stejnou verzi HDInsight a stejné účty úložiště. Pokud upgradujete verze HDInsight z 3,6 na 4,0, proveďte nejprve kroky v tomto článku. Pak [postupujte podle oficiálních kroků upgradu](./apache-hive-migrate-workloads.md).

## <a name="prerequisites"></a>Požadavky

Pokud používáte [Azure Data Lake Storage Gen1](../overview-data-lake-storage-gen1.md), umístění tabulek podregistru jsou nejspíš závislá na konfiguracích HDFS clusteru pro Azure Data Lake Storage Gen1. Spuštěním následující akce skriptu zajistěte, aby tato umístění byla v ostatních clusterech přenosná. Viz [akce skriptu na běžícím clusteru](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

Akce je podobná nahrazení symbolických odkazů pomocí jejich úplných cest.

|Vlastnost | Hodnota |
|---|---|
|Identifikátor URI skriptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
|Typ (typy) uzlů|Head|
|Parametry|""|

## <a name="migrate-with-exportimport-using-sqlpackage"></a>Migrace pomocí SqlPackage pomocí exportu a importu

An HDInsight cluster byl vytvořen až poté, co 2020-10-15 podporuje export/import SQL pro podregistr default metastore DB pomocí `sqlpackage` .

1. Nainstalujte [SqlPackage](/sql/tools/sqlpackage-download#get-sqlpackage-net-core-for-linux) do clusteru.

2. Exportujte výchozí metastore DB do souboru BACPAC spuštěním následujícího příkazu.

    ```bash
    wget "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_metastore_tool.py"
    SQLPACKAGE_FILE='/home/sshuser/sqlpackage/sqlpackage'  # replace with sqlpackage location
    TARGET_FILE='hive.bacpac'
    sudo python hive_metastore_tool.py --sqlpackagefile $SQLPACKAGE_FILE --targetfile $TARGET_FILE
    ```

3. Uložte soubor BACPAC. Níže je uvedená možnost.

    ```bash
    hdfs dfs -mkdir -p /bacpacs
    hdfs dfs -put $TARGET_FILE /bacpacs/
    ```

4. Importujte soubor BACPAC do nové databáze se [zde](../../azure-sql/database/database-import.md)uvedenými kroky.

5. Nová databáze je připravená na [nový cluster HDInsight nakonfigurovaná jako externí METASTORE DB](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation).

## <a name="migrate-using-hive-script"></a>Migrace pomocí skriptu podregistru

Clustery vytvořené před 2020-10-15 nepodporují export a import výchozí metastore DB.

U takových clusterů postupujte podle pokynů průvodce [kopírováním podregistrů napříč účty úložiště](./hive-migration-across-storage-accounts.md)a použijte druhý cluster s [externí metastore Hive DB](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation). Druhý cluster může používat stejný účet úložiště, ale musí používat nový výchozí systém souborů.

### <a name="option-to-shallow-copy"></a>Možnost "omezené" kopírování
Spotřeba úložiště by byla dvojitě v případě, že jsou tabulky "hluboko zkopírovány" pomocí výše uvedeného průvodce. Data ve zdrojovém kontejneru úložiště musíte ručně vyčistit.
Místo toho můžeme tabulky "bez transakčního kopírování" zkopírovat. Všechny tabulky podregistru ve službě HDInsight 3,6 nejsou ve výchozím nastavení jiné než transakční, ale v HDInsight 4,0 jsou netransakční, ale jenom externí tabulky. Transakční tabulky musí být hluboko zkopírované. Při netransakčních tabulkách s omezeným kopírováním použijte tento postup:

1. Spusťte skript [Hive-ddls.sh](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-ddls.sh) na primárním hlavnímu uzlu zdrojového clusteru a vygenerujte DDL pro každou tabulku podregistru.
2. Skript DDL je zapsán do skriptu místního podregistru s názvem `/tmp/hdi_hive_ddls.hql` . Tuto akci proveďte v cílovém clusteru, který používá externí metastore Hive DB.

## <a name="verify-that-all-hive-tables-are-imported"></a>Ověřte, zda jsou importovány všechny tabulky podregistru.

Následující příkaz používá dotaz SQL v metastore DB k tisku všech tabulek podregistru a jejich umístění dat. Porovnejte výstupy mezi novými a starými clustery a ověřte, že v nové databázi metastore DB chybí žádné tabulky.

```bash
SCRIPT_FNAME='hive_metastore_tool.py'
SCRIPT="/tmp/$SCRIPT_FNAME"
wget -O "$SCRIPT" "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/$SCRIPT_FNAME"
OUTPUT_FILE='/tmp/hivetables.csv'
QUERY="SELECT DBS.NAME, TBLS.TBL_NAME, SDS.LOCATION FROM SDS, TBLS, DBS WHERE TBLS.SD_ID = SDS.SD_ID AND TBLS.DB_ID = DBS.DB_ID ORDER BY DBS.NAME, TBLS.TBL_NAME ASC;"
sudo python "$SCRIPT" --query "$QUERY" > $OUTPUT_FILE
```

## <a name="further-reading"></a>Další čtení

* [Migrace úloh z HDInsight 3,6 na 4,0](./apache-hive-migrate-workloads.md)
* [Migrace zatížení z podregistru mezi účty úložiště](./hive-migration-across-storage-accounts.md)
* [Připojení k Beeline ve službě HDInsight](../hadoop/connect-install-beeline.md)
* [Řešení potíží s vytvořením tabulky Chyba oprávnění](./interactive-query-troubleshoot-permission-error-create-table.md)