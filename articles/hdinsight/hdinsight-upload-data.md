---
title: Nahrání dat pro úlohy Apache Hadoop v HDInsightu
description: Přečtěte si, jak nahrát a získat přístup k datům pro úlohy Apache Hadoop v HDInsightu. Použijte klasické rozhraní příkazového řádku Azure, Průzkumník a ukládání Azure, Azure PowerShell, příkazový řádek Hadoop nebo Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdiseo17may2017
ms.date: 04/07/2020
ms.openlocfilehash: c862633245e75613f9e4f9956486f872b96239f8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875006"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Nahrání dat pro úlohy Apache Hadoop v HDInsightu

HDInsight poskytuje hadoop distribuovaný souborový systém (HDFS) přes Azure Storage a Azure Data Lake Storage. Toto úložiště zahrnuje Gen1 a Gen2. Azure Storage a Data Lake Storage Gen1 a Gen2 jsou navržené jako rozšíření HDFS. Umožňují úplnou sadu komponent v prostředí Hadoop pracovat přímo na data, která spravuje. Azure Storage, Data Lake Storage Gen1 a Gen2 jsou odlišné systémy souborů. Systémy jsou optimalizovány pro ukládání dat a výpočtů na těchto datech. Informace o výhodách používání Azure Storage najdete v [tématu Použití Azure Storage s HDInsight](hdinsight-hadoop-use-blob-storage.md). Viz také [Použití Data Lake Storage Gen1 s HDInsight](hdinsight-hadoop-use-data-lake-store.md)a Použití Data Lake Storage [Gen2 s HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, poznamenejte si následující požadavky:

* Cluster Azure HDInsight. Pokyny najdete [v tématu Začínáme s Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) nebo [Vytvoření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Znalost těchto článků:
    * [Použití azure storage s HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Použití funkce Data Lake Storage Gen1 s HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    * [Použití funkce Data Lake Storage Gen2 s HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Nahrání dat do Azure Storage

## <a name="utilities"></a>Nástroje

Společnost Microsoft poskytuje následující nástroje pro práci s Azure Storage:

| Nástroj | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Portál Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop, příkaz](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> Příkaz Hadoop je k dispozici pouze v clusteru HDInsight. Příkaz umožňuje jenom načítání dat z místního systému souborů do služby Azure Storage.  

## <a name="hadoop-command-line"></a>Příkazový řádek Hadoop

Příkazový řádek Hadoop je užitečný jenom pro ukládání dat do objektu blob úložiště Azure, když data už jsou v hlavním uzlu clusteru.

Chcete-li použít příkaz Hadoop, musíte se nejprve připojit k headnode pomocí [SSH nebo PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

Po připojení můžete k nahrání souboru do úložiště použít následující syntaxi.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Například `hadoop fs -copyFromLocal data.txt /example/data/data.txt`.

Vzhledem k tomu, že výchozí systém souborů pro HDInsight je ve službě Azure Storage, /example/data/data.txt je ve skutečnosti ve službě Azure Storage. Soubor můžete také označovat jako:

    wasbs:///example/data/data.txt

– nebo –

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Seznam dalších příkazů Hadoopu, které pracují se soubory, naleznete v tématu[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> V clusterech Apache HBase je výchozí velikost bloku použitá při zápisu dat 256 kB. I když to funguje dobře při použití HBase `hadoop` ROZHRANÍ `hdfs dfs` API nebo REST API, pomocí příkazy nebo pro zápis dat větší než ~12 GB má za následek chybu. Další informace najdete v tématu [výjimku úložiště pro zápis na část objektu blob](#storage-exception-for-write-on-blob) v tomto článku.

## <a name="graphical-clients"></a>Grafickí klienti

Existuje také několik aplikací, které poskytují grafické rozhraní pro práci s Azure Storage. V následující tabulce je uveden seznam několika z těchto aplikací:

| Klient | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Nástroje Microsoft Visual Studia pro HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Průzkumník CloudBerry pro Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Připojení úložiště Azure jako místní hodu

Viz [Mount Azure Storage jako místní disk](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Nahrávání pomocí služeb

### <a name="azure-data-factory"></a>Azure Data Factory

Služba Azure Data Factory je plně spravovaná služba pro vytváření dat: služby úložiště, zpracování a přesundo zjednodušených, adaptabilních a spolehlivých kanálů pro tvorbu dat.

|Typ úložiště|Dokumentace|
|----|----|
|Azure Blob Storage|[Kopírování dat do úložiště objektů blob Azure nebo z nich pomocí Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Kopírování dat do nebo z Azure Data Lake Storage Gen1 pomocí Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Načtení dat do Azure Data Lake Storage Gen2 s Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apačský sqoop

Sqoop je nástroj určený k přenosu dat mezi Hadoopem a relačními databázemi. Slouží k importu dat ze systému správy relačních databází (RDBMS), jako je SQL Server, MySQL nebo Oracle. Pak do distribuovaného souborového systému Hadoop (HDFS). Transformujte data v Hadoopu pomocí MapReduce nebo Hive a pak exportujte data zpět do RDBMS.

Další informace naleznete [v tématu Použití sqoop u HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>Vývojové sady SDK

K Azure Storage se taky dá přistupovat pomocí sady Azure SDK z následujících programovacích jazyků:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Další informace o instalaci sad Azure SDK najdete v tématu [Soubory ke stažení Azure](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Řešení potíží

### <a name="storage-exception-for-write-on-blob"></a>Výjimka úložiště pro zápis do objektu blob

**Příznaky**: Při `hadoop` `hdfs dfs` použití příkazů nebo k zápisu souborů, které jsou ~12 GB nebo větší v clusteru HBase, může dojít k následující chybě:

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

**Příčina:** HBase na hdinsight clustery výchozí velikost bloku 256 KB při zápisu do úložiště Azure. Zatímco funguje pro rozhraní API HBase nebo rozhraní REST API, `hadoop` `hdfs dfs` má za následek chybu při použití nástrojů nebo příkazového řádku.

**Rozlišení**: `fs.azure.write.request.size` Slouží k určení větší velikosti bloku. Tuto změnu můžete provést na základě použití `-D` pomocí parametru. Následující příkaz je příkladem pomocí tohoto `hadoop` parametru s příkazem:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Můžete také zvýšit hodnotu `fs.azure.write.request.size` globálně pomocí Apache Ambari. Následující kroky lze změnit hodnotu v uživatelském uživatelském panelu Ambari:

1. V prohlížeči přejděte do webového uživatelského uživatelského prostředí Ambari pro váš cluster. Adresa URL `https://CLUSTERNAME.azurehdinsight.net`je `CLUSTERNAME` , kde je název clusteru. Po zobrazení výzvy zadejte název správce a heslo clusteru.
2. Na levé straně obrazovky vyberte **HDFS**a pak vyberte kartu **Configs.**
3. Do pole **Filtr...** `fs.azure.write.request.size`zadejte .
4. Změňte hodnotu z 262144 (256 KB) na novou hodnotu. Například 4194304 (4 MB).

    ![Obrázek změny hodnoty prostřednictvím webového uživatelského uživatelského uživatelského tlačítka Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Další informace o používání Ambari najdete v [tématu Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Další kroky

Nyní, když chápete, jak získat data do HDInsight, přečtěte si následující články, abyste se dozvěděli analýzu:

* [Začínáme se službou Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Odeslat pracovní příležitosti Apache Hadoop programově](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Použití Apache Hive s HDInsight](hadoop/hdinsight-use-hive.md)
* [Použijte Apache Pig s HDInsight](./use-pig.md)
