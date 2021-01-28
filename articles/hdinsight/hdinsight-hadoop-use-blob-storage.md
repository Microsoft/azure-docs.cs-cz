---
title: Dotazy na data z úložiště Azure kompatibilního se systémem HDFS – Azure HDInsight
description: Naučte se, jak zadávat dotazy na data z Azure Storage a Azure Data Lake Storage ukládat výsledky analýzy.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: cedc0ff1b3c2aa64f32445eabc800748a753981d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945423"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Použití úložiště Azure s clustery Azure HDInsight

Data můžete ukládat v [Azure Blob Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)nebo [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md). Nebo kombinaci těchto možností. Tyto možnosti úložiště umožňují bezpečně odstraňovat clustery HDInsight, které se používají pro výpočty, aniž by došlo ke ztrátě uživatelských dat.

Apache Hadoop podporuje pojem výchozího systému souborů. Výchozí systém souborů znamená výchozí schéma a autoritu. Lze ho také použít k vyřešení relativní cesty. Během procesu vytváření clusteru HDInsight můžete jako výchozí systém souborů zadat kontejner objektů BLOB v Azure Storage. Nebo se službou HDInsight 3,6 můžete jako výchozí systém souborů s několika výjimkami vybrat buď službu Azure Blob Storage, nebo Azure Data Lake Storage Gen1/Azure Data Lake Storage Gen2. Informace o podpoře použití Data Lake Storage Gen1 jako výchozího i propojeného úložiště najdete v tématu [dostupnost pro cluster HDInsight](./hdinsight-hadoop-use-data-lake-storage-gen1.md#availability-for-hdinsight-clusters).

V tomto článku se dozvíte, jak služba Azure Storage pracuje s clustery HDInsight. 
* Informace o tom, jak Data Lake Storage Gen1 pracuje s clustery HDInsight, najdete v tématu [použití Azure Data Lake Storage Gen1 s clustery Azure HDInsight](./hdinsight-hadoop-use-data-lake-storage-gen1.md).
* informace o tom, jak Data Lake Storage Gen2 pracuje s clustery HDInsight, najdete v tématu [použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](./hdinsight-hadoop-use-data-lake-storage-gen2.md).
* Další informace o vytvoření clusteru HDInsight najdete v tématu věnovaném [vytváření Apache Hadoop clusterů ve službě HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

> [!IMPORTANT]  
> Druh účtu úložiště **BlobStorage** se dá použít jenom jako sekundární úložiště pro clustery HDInsight.

| Druh účtu úložiště | Podporované služby | Podporované úrovně výkonu |Nepodporované úrovně výkonu| Podporované úrovně přístupu |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2 (obecné účely v2)  | Objekt blob     | Standard                    |Premium| Horká, studená, archivní\*   |
| Úložiště (obecné účely v1)   | Objekt blob     | Standard                    |Premium| –                    |
| Blob Storage                    | Objekt blob     | Standard                    |Premium| Horká, studená, archivní\*   |

Pro ukládání obchodních dat nedoporučujeme používat výchozí kontejner objektů BLOB. Ideální postup je výchozí kontejner objektů blob po každém použití odstranit a snížit tak náklady na úložiště. Výchozí kontejner obsahuje protokoly aplikací a systému. Než odstraníte kontejner, nezapomeňte tyto protokoly načíst.

Sdílení jednoho kontejneru objektů BLOB jako výchozího systému souborů pro více clusterů se nepodporuje.

> [!NOTE]  
> Úroveň přístupu archivu je offline vrstva s odezvou na několik hodin a nedoporučuje se pro použití se službou HDInsight. Další informace najdete v tématu [archivní úroveň přístupu](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

## <a name="access-files-from-within-cluster"></a>Přístup k souborům v rámci clusteru

Existuje několik způsobů, jak můžete přistupovat k souborům v Data Lake Storage z clusteru HDInsight. Schéma identifikátoru URI poskytuje nešifrovaný přístup (s *wasb:* prefix) a ŠIFROVANÝ přístup TLS (s *wasbs*). Doporučujeme používat *wasbs* kdykoli je to možné, i v případě přístupu k datům, umístěným uvnitř stejné oblasti v Azure.

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

Příklady jsou založené na [připojení SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) k hlavnímu uzlu clusteru. V příkladech se používají všechna tři schémata identifikátoru URI. Nahradit `CONTAINERNAME` a `STORAGEACCOUNT` za relevantní hodnoty

#### <a name="a-few-hdfs-commands"></a>Několik příkazů HDFS

1. Vytvoří soubor v místním úložišti.

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

Pro ilustrativní účely se zobrazí tři umístění souborů. Pro skutečné provedení použijte jenom jednu z `LOCATION` položek.

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

    **HDFS**  >  **Konfigurace** a zadejte `fs.defaultFS` do pole pro zadání filtru.

* Pokud chcete zjistit, jestli je úložiště wasb nakonfigurované jako sekundární úložiště, přejděte na:

    **HDFS**  >  **Konfigurace** a zadejte `blob.core.windows.net` do pole pro zadání filtru.

Pokud chcete získat cestu pomocí REST API Ambari, přečtěte si téma [získání výchozího úložiště](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage).

## <a name="blob-containers"></a>Kontejnery objektů blob

K použití objektů blob je třeba nejprve vytvořit [Účet služby Azure Storage](../storage/common/storage-account-create.md). V rámci tohoto kroku zadáte oblast Azure, ve které se účet úložiště vytvoří. Účet úložiště a clusteru musí být uloženy ve stejné oblasti. Databáze metastore Hive SQL Server a databáze Apache Oozie SQL Server metastore se musí nacházet ve stejné oblasti.

Bez ohledu na svoje umístění patří každý objekt blob, který vytvoříte, do kontejneru v účtu úložiště Azure. Tento kontejner může být existující objekt BLOB vytvořený mimo HDInsight. Může se jednat o kontejner, který se vytvoří pro cluster HDInsight.

Výchozí kontejner objektu blob ukládá konkrétní informace, jako je historie úlohy a protokoly. Výchozí kontejner objektu Blob nesdílejte s více clustery služby HDInsight. Tato akce může poškodit historii úlohy. Pro každý cluster se doporučuje použít jiný kontejner. Místo výchozího účtu úložiště umístěte sdílená data na propojený účet úložiště zadaný pro všechny příslušné clustery. Další informace o konfiguraci propojených účtů úložiště najdete v tématu [Tvorba clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Nicméně, po odstranění původního clusteru HDInsight můžete znovu použít výchozí kontejner úložiště. U clusterů HBA můžete ve skutečnosti udržovat schéma a data v tabulce HBA vytvořením nového clusteru HBA pomocí výchozího kontejneru objektů blob, který používá odstraněný cluster HBA.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>Použití dalších účtů úložiště

Při vytváření clusteru HDInsight zadáváte účet služby Azure Storage, který k němu chcete přidružit. Během procesu vytváření nebo po vytvoření clusteru můžete taky přidat další účty úložiště ze stejného předplatného Azure nebo různých předplatných Azure. Pokyny pro přidání dalších účtů úložiště najdete v tématu [Tvorba clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Použití dalšího účtu úložiště v jiném umístění, než je cluster HDInsight, není podporováno.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak používat HDFS kompatibilní úložiště Azure se službou HDInsight. Toto úložiště umožňuje vytvářet přizpůsobitelná a dlouhodobá řešení pro získávání archivovaných dat a používat HDInsight k odemčení informací uvnitř uložených strukturovaných a nestrukturovaných dat.

Další informace naleznete v tématu:

* [Rychlý Start: Vytvoření clusteru Apache Hadoop](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Kurz: vytváření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Nahrání dat do služby HDInsight](hdinsight-upload-data.md)
* [Kurz: extrakce, transformace a načtení dat pomocí interaktivního dotazu ve službě Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Použití sdílených přístupových podpisů služby Azure Storage k omezení přístupu k datům pomocí HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)