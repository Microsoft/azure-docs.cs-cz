---
title: Dotazy na data z úložiště Azure kompatibilního se systémem HDFS – Azure HDInsight
description: Zjistěte, jak dotazovat data z Azure Storage a Azure Data Lake Storage a ukládat výsledky analýzy.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 6a4ae2932f8d294ecf71de0ae405204a1f4d7b4d
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436948"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Použití úložiště Azure s clustery Azure HDInsight

Chcete-li analyzovat data v clusteru HDInsight, můžete je uložit buď do [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)nebo v kombinaci. Tyto možnosti úložiště umožňují bezpečně odstranit clustery HDInsight, které se používají pro výpočty bez ztráty uživatelských dat.

Apache Hadoop podporuje představu o výchozím souborovém systému. Výchozí systém souborů znamená výchozí schéma a autoritu. Lze ho také použít k vyřešení relativní cesty. Během procesu vytváření clusteru HDInsight můžete zadat kontejner objektů blob ve službě Azure Jako výchozí souborový systém nebo s HDInsight 3.6 můžete vybrat buď Azure Storage nebo Azure Data Lake Storage Gen 1/ Azure Data Lake Storage Gen 2 jako výchozí systém souborů s několika výjimkami. Možnost i možnosti použití úložiště datového jezera Gen 1 jako výchozího i propojeného úložiště najdete v [tématu Dostupnost pro cluster HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

V tomto článku se dozvíte, jak služba Azure Storage pracuje s clustery HDInsight. Informace o tom, jak úložiště datových jezer Gen 1 funguje s clustery HDInsight, najdete [v tématu Použití Azure Data Lake Storage s clustery Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md). Další informace o vytvoření clusteru HDInsight najdete [v tématu Vytvoření clusterů Apache Hadoop v HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!IMPORTANT]  
> Typ účtu úložiště **BlobStorage** lze použít pouze jako sekundární úložiště pro clustery HDInsight.

| Druh účtu úložiště | Podporované služby | Podporované úrovně výkonu |Nepodporované úrovně výkonu| Podporované úrovně přístupu |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2 (obecné účely v2)  | Objekt blob     | Standard                    |Premium| Horké, Cool, Archiv\*   |
| Úložiště (obecné účely v1)   | Objekt blob     | Standard                    |Premium| Není dostupné.                    |
| Úložiště objektů BlobStorage                    | Objekt blob     | Standard                    |Premium| Horké, Cool, Archiv\*   |

Nedoporučujeme používat výchozí kontejner objektů blob pro ukládání obchodních dat. Ideální postup je výchozí kontejner objektů blob po každém použití odstranit a snížit tak náklady na úložiště. Výchozí kontejner obsahuje protokoly aplikací a systému. Než odstraníte kontejner, nezapomeňte tyto protokoly načíst.

Sdílení jednoho kontejneru objektů blob jako výchozího systému souborů pro více clusterů není podporováno.

> [!NOTE]  
> Úroveň přístupu archivu je offline vrstva, která má latenci načítání několika hodin a nedoporučuje se pro použití s HDInsight. Další informace naleznete v tématu [Archivní úroveň přístupu](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

## <a name="access-files-from-within-cluster"></a>Přístup k souborům z clusteru

Existuje několik způsobů, jak získat přístup k souborům v úložišti Data Lake Storage z clusteru HDInsight. Schéma URI poskytuje nešifrovaný přístup (s *wasb:* prefix) a TLS šifrovaný přístup (s *wasbs*). Doporučujeme používat *wasbs* kdykoli je to možné, i v případě přístupu k datům, umístěným uvnitř stejné oblasti v Azure.

* **Pomocí plně kvalifikovaného názvu**. S tímto přístupem zadáváte úplnou cestu k souboru, ke kterému chcete získat přístup.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **Pomocí zkráceného formátu cesty**. Pomocí tohoto přístupu nahradíte cestu až ke kořenovému adresáři clusteru:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **Pomocí relativní cesty**. S tímto přístupem zadáváte pouze relativní cestu k souboru, ke kterému chcete získat přístup.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Příklady přístupu k datům

Příklady jsou založeny na [ssh připojení](./hdinsight-hadoop-linux-use-ssh-unix.md) k hlavnímu uzlu clusteru. Příklady používají všechna tři schémata URI. Nahraďte `CONTAINERNAME` a `STORAGEACCOUNT` s příslušnými hodnotami

#### <a name="a-few-hdfs-commands"></a>Několik příkazů hdfs

1. Vytvořte jednoduchý soubor v místním úložišti.

    ```bash
    touch testFile.txt
    ```

1. Vytvořte adresáře v clusterovém úložišti.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Zkopírujte data z místního úložiště do clusterového úložiště.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Seznam obsahu adresáře v úložišti clusteru.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> Při práci s objekty blob mimo HDInsight většina nástrojů nerozpozná formát WASB a místo toho očekávají základní formát cesty, jako je například `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Vytvoření tabulky Podregistru

Pro ilustrativní účely jsou zobrazena tři umístění souborů. Pro skutečné spuštění použijte pouze `LOCATION` jednu z položek.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="access-files-from-outside-cluster"></a>Přístup k souborům z externího clusteru

Microsoft poskytuje následující nástroje pro práci s Azure Storage:

| Nástroj | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [portál Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>Identifikace cesty úložiště z Ambari

* Chcete-li identifikovat úplnou cestu k nakonfigurovanému výchozímu úložišti, přejděte na:

    **HDFS** > **configs** `fs.defaultFS` a zadejte do vstupního pole filtru.

* Chcete-li zkontrolovat, zda je úložiště wasb nakonfigurováno jako sekundární úložiště, přejděte na:

    **HDFS** > **configs** `blob.core.windows.net` a zadejte do vstupního pole filtru.

Chcete-li získat cestu pomocí rozhraní REST API Ambari, přečtěte si informace [o získání výchozího úložiště](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage).

## <a name="blob-containers"></a>Kontejnery s objekty blob

K použití objektů blob je třeba nejprve vytvořit [Účet služby Azure Storage](../storage/common/storage-create-storage-account.md). V rámci tohoto procesu zadáte oblast Azure, ve které se účet úložiště vytvoří. Účet úložiště a clusteru musí být uloženy ve stejné oblasti. Databáze hive metastore SQL Server a apache oozie metastore SQL Server databáze musí být také umístěn ve stejné oblasti.

Bez ohledu na svoje umístění patří každý objekt blob, který vytvoříte, do kontejneru v účtu úložiště Azure. Tento kontejner může být existující objekt blob, který se vytvořil mimo HDInsight, nebo to může být kontejner, který se vytvořil pro cluster služby HDInsight.

Výchozí kontejner objektu blob ukládá konkrétní informace, jako je historie úlohy a protokoly. Výchozí kontejner objektu Blob nesdílejte s více clustery služby HDInsight. Může dojít k poškození historie úlohy. Doporučujeme použít jiný kontejner pro každý cluster a umístit sdílená data na propojený účet úložiště zadaný v nasazení všech relevantních clusterů, nikoli výchozí účet úložiště. Další informace o konfiguraci propojených účtů úložiště najdete v tématu [Tvorba clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Nicméně, po odstranění původního clusteru HDInsight můžete znovu použít výchozí kontejner úložiště. U clusterů HBase můžete ve skutečnosti zachovat schéma tabulky HBase a data vytvořením nového clusteru HBase pomocí výchozího kontejneru objektů blob, který používá cluster HBase, který byl odstraněn.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>Použití dalších účtů úložiště

Při vytváření clusteru HDInsight zadáváte účet služby Azure Storage, který k němu chcete přidružit. Kromě tohoto účtu úložiště můžete během procesu vytváření nebo až po vytvoření clusteru přidat další účty úložiště ze stejného předplatného Azure nebo různých předplatných Azure. Pokyny pro přidání dalších účtů úložiště najdete v tématu [Tvorba clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Použití dalšího účtu úložiště v jiném umístění, než je cluster HDInsight, není podporováno.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak používat HDFS kompatibilní úložiště Azure se službou HDInsight. To umožňuje vytvářet škálovatelná a dlouhodobá řešení pro získávání archivovaných dat a používat službu HDInsight k odemčení informací uvnitř uložených strukturovaných a nestrukturovaných dat.

Další informace naleznete v tématu:

* [Začínáme se službou Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Začínáme s Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Nahrání dat do služby HDInsight](hdinsight-upload-data.md)
* [Použití Apache Hive s HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití sdílených přístupových podpisů služby Azure Storage k omezení přístupu k datům pomocí HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Kurz: Extrahování, transformace a načítání dat pomocí interaktivního dotazu v Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
