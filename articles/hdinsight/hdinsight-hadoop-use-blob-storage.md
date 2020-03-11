---
title: Dotazy na data z úložiště Azure kompatibilního se systémem HDFS – Azure HDInsight
description: Naučte se, jak zadávat dotazy na data z Azure Storage a Azure Data Lake Storage ukládat výsledky analýzy.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: e62f6f8df51c6acf4e2ad8e28e431d264c2c99e8
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037243"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Použití úložiště Azure s clustery Azure HDInsight

Chcete-li analyzovat data v clusteru HDInsight, můžete ukládat data buď v [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)nebo kombinaci. Tyto možnosti úložiště umožňují bezpečně odstraňovat clustery HDInsight, které se používají pro výpočty, aniž by došlo ke ztrátě uživatelských dat.

Apache Hadoop podporuje pojem výchozího systému souborů. Výchozí systém souborů znamená výchozí schéma a autoritu. Lze ho také použít k vyřešení relativní cesty. Během procesu vytváření clusteru HDInsight můžete jako výchozí systém souborů zadat kontejner objektů blob Azure Storage, nebo pomocí HDInsight 3,6, můžete vybrat buď Azure Storage nebo Azure Data Lake Storage Gen 1/Azure Data Lake Storage Gen 2 jako výchozí systém souborů s několika výjimkami. Informace o podpoře použití Data Lake Storage Gen 1 jako výchozího i propojeného úložiště najdete v tématu [dostupnost pro cluster HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

V tomto článku se dozvíte, jak služba Azure Storage pracuje s clustery HDInsight. Informace o tom, jak Data Lake Storage Gen 1 pracuje s clustery HDInsight, najdete v tématu [použití Azure Data Lake Storage s clustery Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md). Další informace o vytvoření clusteru HDInsight najdete v tématu věnovaném [vytváření Apache Hadoop clusterů ve službě HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!IMPORTANT]  
> Druh účtu úložiště **BlobStorage** se dá použít jenom jako sekundární úložiště pro clustery HDInsight.

| Druh účtu úložiště | Podporované služby | Podporované úrovně výkonu |Nepodporované úrovně výkonu| Podporované úrovně přístupu |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2 (obecné účely v2)  | Objekt blob     | Standard                    |Premium| Horká, studená, archivní\*   |
| Storage (pro obecné účely V1)   | Objekt blob     | Standard                    |Premium| neuvedeno                    |
| BlobStorage                    | Objekt blob     | Standard                    |Premium| Horká, studená, archivní\*   |

Pro ukládání obchodních dat nedoporučujeme používat výchozí kontejner objektů BLOB. Ideální postup je výchozí kontejner objektů blob po každém použití odstranit a snížit tak náklady na úložiště. Výchozí kontejner obsahuje protokoly aplikací a systému. Než odstraníte kontejner, nezapomeňte tyto protokoly načíst.

Sdílení jednoho kontejneru objektů BLOB jako výchozího systému souborů pro více clusterů se nepodporuje.

> [!NOTE]  
> Úroveň přístupu archivu je offline vrstva s odezvou na několik hodin a nedoporučuje se pro použití se službou HDInsight. Další informace najdete v tématu [archivní úroveň přístupu](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

## <a name="access-files-from-within-cluster"></a>Přístup k souborům v rámci clusteru

Existuje několik způsobů, jak můžete přistupovat k souborům v Data Lake Storage z clusteru HDInsight. Schéma identifikátoru URI poskytuje nezašifrovaný přístup (s předponou *wasb:* ) a zašifrovaný přístup SSL (s *wasbs*). Doporučujeme používat *wasbs* kdykoli je to možné, i v případě přístupu k datům, umístěným uvnitř stejné oblasti v Azure.

* **Pomocí plně kvalifikovaného názvu**. S tímto přístupem zadáváte úplnou cestu k souboru, ke kterému chcete získat přístup.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **Pomocí zkráceného formátu cesty**. Pomocí tohoto přístupu nahradíte cestu až ke kořenu clusteru:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **Pomocí relativní cesty**. S tímto přístupem zadáváte pouze relativní cestu k souboru, ke kterému chcete získat přístup.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Příklady přístupu k datům

Příklady jsou založené na [připojení SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) k hlavnímu uzlu clusteru. V příkladech se používají všechna tři schémata identifikátoru URI. Nahradit `CONTAINERNAME` a `STORAGEACCOUNT` příslušnými hodnotami

#### <a name="a-few-hdfs-commands"></a>Několik příkazů HDFS

1. Vytvořte v místním úložišti jednoduchý soubor.

    ```bash
    touch testFile.txt
    ```

1. Vytvořte adresáře v úložišti clusteru.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopírovat data z místního úložiště do úložiště clusteru.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Vypíše obsah adresáře v úložišti clusteru.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> Při práci s objekty blob mimo HDInsight většina nástrojů nerozpozná formát WASB a místo toho očekávají základní formát cesty, jako je například `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Vytvoření tabulky podregistru

Pro ilustrativní účely se zobrazí tři umístění souborů. Pro skutečné provedení použijte jenom jednu z `LOCATION`ch položek.

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

## <a name="access-files-from-outside-cluster"></a>Přístup k souborům z vnějšího clusteru

Společnost Microsoft poskytuje následující nástroje pro práci s Azure Storage:

| Nástroj | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>Identifikujte cestu k úložišti z Ambari.

* Pokud chcete zjistit úplnou cestu k nakonfigurovanému výchozímu úložišti, přejděte na:

    **HDFS** > **config** a do pole filtru zadejte `fs.defaultFS`.

* Pokud chcete zjistit, jestli je úložiště wasb nakonfigurované jako sekundární úložiště, přejděte na:

    **HDFS** > **config** a do pole filtru zadejte `blob.core.windows.net`.

Pokud chcete získat cestu pomocí REST API Ambari, přečtěte si téma [získání výchozího úložiště](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage).

## <a name="blob-containers"></a>Kontejnery objektů BLOB

Pokud chcete použít bloby, musíte nejdřív vytvořit [účet Azure Storage](../storage/common/storage-create-storage-account.md). V rámci tohoto procesu zadáte oblast Azure, ve které se účet úložiště vytvoří. Účet úložiště a clusteru musí být uloženy ve stejné oblasti. Databáze metastore Hive SQL Server a databáze Apache Oozie SQL Server metastore musí být také umístěny ve stejné oblasti.

Bez ohledu na svoje umístění patří každý objekt blob, který vytvoříte, do kontejneru v účtu úložiště Azure. Tento kontejner může být existující objekt blob, který se vytvořil mimo HDInsight, nebo to může být kontejner, který se vytvořil pro cluster služby HDInsight.

Výchozí kontejner objektu blob ukládá konkrétní informace, jako je historie úlohy a protokoly. Výchozí kontejner objektu Blob nesdílejte s více clustery služby HDInsight. Může dojít k poškození historie úlohy. Doporučuje se použít pro každý cluster jiný kontejner a umístit sdílená data na propojený účet úložiště zadaný v nasazení všech relevantních clusterů, a ne jako výchozí účet úložiště. Další informace o konfiguraci propojených účtů úložiště najdete v tématu [Vytvoření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Nicméně, po odstranění původního clusteru HDInsight můžete znovu použít výchozí kontejner úložiště. U clusterů HBA můžete ve skutečnosti udržovat schéma a data v tabulce HBA vytvořením nového clusteru HBA pomocí výchozího kontejneru objektů blob, který se používá v clusteru HBA, který byl odstraněn.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>Použití dalších účtů úložiště

Při vytváření clusteru HDInsight zadáváte účet služby Azure Storage, který k němu chcete přidružit. Kromě tohoto účtu úložiště můžete během procesu vytváření nebo až po vytvoření clusteru přidat další účty úložiště ze stejného předplatného Azure nebo různých předplatných Azure. Pokyny pro přidání dalších účtů úložiště najdete v tématu [Vytváření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Použití dalšího účtu úložiště v jiném umístění, než je cluster HDInsight, není podporováno.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak používat HDFS kompatibilní úložiště Azure se službou HDInsight. To umožňuje vytvářet škálovatelná a dlouhodobá řešení pro získávání archivovaných dat a používat službu HDInsight k odemčení informací uvnitř uložených strukturovaných a nestrukturovaných dat.

Další informace naleznete v tématu:

* [Začínáme se službou Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Začínáme s Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Nahrání dat do HDInsight](hdinsight-upload-data.md)
* [Použití Apache Hive se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Omezení přístupu k datům pomocí HDInsight pomocí Azure Storage sdílených přístupových podpisů](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Kurz: extrakce, transformace a načtení dat pomocí interaktivního dotazu ve službě Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
