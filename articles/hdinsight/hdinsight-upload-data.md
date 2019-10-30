---
title: Nahrávání dat pro úlohy Apache Hadoop v HDInsight
description: Naučte se nahrávat a přistupovat k datům pro úlohy Apache Hadoop v HDInsight pomocí Azure Classic CLI, Průzkumník služby Azure Storage, Azure PowerShell, příkazového řádku Hadoop nebo Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdiseo17may2017
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 7eb1f7e1ce02a30f84cb520438f60fcbcfa3a965
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100133"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Nahrávání dat pro úlohy Apache Hadoop v HDInsight

Azure HDInsight poskytuje plně vybavený systém souborů Hadoop (Hadoop Distributed File System) nad Azure Storage a Azure Data Lake Storage (Gen1 a Gen2). Azure Storage a Data Lake Storage Gen1 a Gen2 jsou navržené jako HDFS Extensions pro zajištění bezproblémového prostředí pro zákazníky. Umožňují kompletní práci s daty, která spravuje, v ekosystému Hadoop. Azure Storage, Data Lake Storage Gen1 a Gen2 jsou odlišné systémy souborů, které jsou optimalizované pro ukládání dat a výpočtů na těchto datech. Informace o výhodách použití Azure Storage najdete v tématu [použití Azure Storage se službou HDInsight](hdinsight-hadoop-use-blob-storage.md), [použití Data Lake Storage Gen1 se službou hdinsight](hdinsight-hadoop-use-data-lake-store.md)a [použití Data Lake Storage Gen2 se službou HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Předpoklady

Než začnete, vezměte na vědomí následující požadavky:

* Cluster Azure HDInsight. Pokyny najdete v tématu Začínáme [se službou Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) nebo [vytváření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Znalost následujících článků:
    * [Použití Azure Storage se službou HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Použití Data Lake Storage Gen1 se službou HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    * [Použití Data Lake Storage Gen2 se službou HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Nahrání dat do Azure Storage

## <a name="utilities"></a>Veřejné služby

Společnost Microsoft poskytuje následující nástroje pro práci s Azure Storage:

| Nástroj | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Příkaz Hadoop](#commandline) |✔ |✔ |✔ |

> [!NOTE]  
> Příkaz Hadoop je k dispozici pouze v clusteru HDInsight. Příkaz umožňuje načíst data z místního systému souborů do Azure Storage.  

## <a id="commandline"></a>Příkazový řádek Hadoop

Příkazový řádek Hadoop je užitečný jenom pro ukládání dat do objektu BLOB služby Azure Storage, když jsou data v hlavním uzlu clusteru již přítomna.

Aby bylo možné použít příkaz Hadoop, musíte se nejprve připojit k hlavnímu uzlu pomocí protokolu [SSH nebo výstupu](hdinsight-hadoop-linux-use-ssh-unix.md).

Po připojení můžete k nahrání souboru do úložiště použít následující syntaxi.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Například `hadoop fs -copyFromLocal data.txt /example/data/data.txt`.

Vzhledem k tomu, že výchozí systém souborů pro HDInsight je v Azure Storage, je/example/data/data.txt ve skutečnosti Azure Storage. Můžete také odkazovat na soubor jako:

    wasbs:///example/data/data.txt

nebo

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Seznam dalších příkazů Hadoop, které pracují se soubory, najdete v tématu [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> V clusterech Apache HBA je výchozí velikost bloku použitá při zápisu dat 256 KB. I když se to při použití rozhraní API HBA nebo rozhraní REST API funguje správně, při použití příkazů `hadoop` nebo `hdfs dfs` k zápisu dat větších než ~ 12 GB dojde k chybě. Další informace najdete v části [výjimka úložiště pro zápis do objektu BLOB](#storageexception) v tomto článku.

## <a name="graphical-clients"></a>Grafické klienty

K dispozici je také několik aplikací, které poskytují grafické rozhraní pro práci s Azure Storage. V následující tabulce jsou uvedeny některé z těchto aplikací:

| Klient | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio nástroje pro HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer pro Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Připojit Azure Storage jako místní disk

Viz možnost [připojit Azure Storage jako místní disk](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Nahrávání pomocí služeb

### <a name="azure-data-factory"></a>Azure Data Factory

Služba Azure Data Factory je plně spravovaná služba pro vytváření úložiště dat, zpracování dat a služby pro přesun dat do zjednodušených, škálovatelných a spolehlivých produkčních kanálů.

|Typ úložiště|Dokumentace|
|----|----|
|Azure Blob Storage|[Kopírování dat do nebo z úložiště objektů BLOB v Azure pomocí Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Kopírování dat do nebo z Azure Data Lake Storage Gen1 pomocí Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Načtení dat do Azure Data Lake Storage Gen2 s využitím Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop

Sqoop je nástroj určený k přenosu dat mezi Hadoop a relačními databázemi. Můžete ji použít k importu dat ze systému pro správu relačních databází (RDBMS), jako je SQL Server, MySQL nebo Oracle, do systému Hadoop Distributed File System (HDFS), transformovat data v Hadoop pomocí MapReduce nebo podregistru a následně data exportovat zpátky do RDBMS.

Další informace najdete v tématu [použití Sqoop se službou HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>Vývojové sady SDK

K Azure Storage je taky možné přistupovat pomocí sady Azure SDK z následujících programovacích jazyků:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Další informace o instalaci sad Azure SDK najdete v tématu [Azure downloads](https://azure.microsoft.com/downloads/) .

## <a name="troubleshooting"></a>Řešení potíží

### <a id="storageexception"></a>Výjimka úložiště pro zápis objektů BLOB

**Příznaky**: při použití příkazů `hadoop` nebo `hdfs dfs` k zápisu souborů, které jsou na clusteru HBA na 12 GB nebo větší, může dojít k následující chybě:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Příčina**: při zápisu do Azure Storage se ve výchozím nastavení clustery HBA v clusterech HDInsight zablokuje na velikost 256 KB. I když funguje pro adaptéry HBA rozhraní API nebo rozhraní REST API, při použití `hadoop` nebo `hdfs dfs` nástrojů příkazového řádku dojde k chybě.

**Řešení**: použijte `fs.azure.write.request.size` k určení větší velikosti bloku. To můžete provést na základě jednotlivých použití pomocí parametru `-D`. Následující příkaz je příkladem použití tohoto parametru s příkazem `hadoop`:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Hodnotu `fs.azure.write.request.size` můžete také globálně zvýšit pomocí Apache Ambari. Pomocí následujících kroků můžete změnit hodnotu ve webovém uživatelském rozhraní Ambari:

1. V prohlížeči přejdete do webového uživatelského rozhraní Ambari pro váš cluster. Toto je `https://CLUSTERNAME.azurehdinsight.net`, kde `CLUSTERNAME` je název vašeho clusteru.

    Po zobrazení výzvy zadejte jméno správce a heslo pro cluster.
2. Na levé straně obrazovky vyberte **HDFS**a pak vyberte kartu **Konfigurace** .
3. Do pole **Filter...** zadejte `fs.azure.write.request.size`. Tím se zobrazí pole a aktuální hodnota uprostřed stránky.
4. Změňte hodnotu z 262144 (256 KB) na novou hodnotu. Například 4194304 (4 MB).

    ![Obrázek změny hodnoty prostřednictvím webového uživatelského rozhraní Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Další informace o použití Ambari najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Další kroky

Teď, když rozumíte tomu, jak získat data do HDInsight, si přečtěte následující články, kde se dozvíte, jak provést analýzu:

* [Začínáme se službou Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Programové odeslání Apache Hadoop úloh](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Použití Apache Hive se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Apache prasete se službou HDInsight](hadoop/hdinsight-use-pig.md)
