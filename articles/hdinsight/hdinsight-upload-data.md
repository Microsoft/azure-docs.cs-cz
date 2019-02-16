---
title: Nahrání dat pro úlohy Apache Hadoop v HDInsight
description: Zjistěte, jak nahrát a přístup k datům pro úlohy Apache Hadoop v HDInsight pomocí Azure classic CLI, Průzkumníka služby Azure Storage, Azure Powershellu, příkazového řádku Hadoopu nebo Sqoopu.
keywords: ETL hadoop, přesun dat do služby hadoop, hadoop načítání dat
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/08/2019
ms.openlocfilehash: 513cc1f0155c5e5499d0bf076d21aff46756d769
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312170"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Nahrání dat pro úlohy Apache Hadoop v HDInsight

Azure HDInsight nabízí v porovnání s Azure Storage a Azure Data Lake Storage (Gen1 a Gen2) plně funkční distribuovaného systému souborů Hadoop (HDFS). Azure Storage a Data Lake Storage Gen1 a Gen2 jsou navrženy jako rozšíření HDFS poskytuje bezproblémové prostředí pro zákazníky. Umožňují celá sada komponent v ekosystému Hadoop pracovat přímo na data, která spravuje. Azure Storage, Data Lake Storage Gen1 a Gen2 jsou systémy různých souborů, které jsou optimalizované pro úložiště dat a výpočty na těchto datech. Informace o výhodách používání služby Azure Storage najdete v tématu [použití služby Azure Storage s HDInsight][hdinsight-storage], [použití Data Lake Storage Gen1 s HDInsight](hdinsight-hadoop-use-data-lake-store.md), a [ Pomocí HDInsight v Data Lake Storage Gen2](../storage/blobs/data-lake-storage-use-hdi-cluster.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, vezměte na vědomí následující požadavky:

* Cluster Azure HDInsight. Pokyny najdete v tématu [Začínáme s Azure HDInsight] [ hdinsight-get-started] nebo [clusterů HDInsight vytvořit](hdinsight-hadoop-provision-linux-clusters.md).
* Znalost následujících článcích:

    - [Použití služby Azure Storage s HDInsight][hdinsight-storage]
    - [Data Lake Storage Gen1 pomocí služby HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    - [Data Lake Storage Gen2 pomocí služby HDInsight](../storage/blobs/data-lake-storage-use-hdi-cluster.md)  

## <a name="upload-data-to-azure-storage"></a>Nahrání dat do služby Azure Storage

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
> Příkaz Hadoop je dostupná pouze na clusteru HDInsight. Tento příkaz umožňuje pouze načítání dat z místního systému souborů do služby Azure Storage.  


## <a id="commandline"></a>Hadoop příkazového řádku
Příkazový řádek systému Hadoop je platný pouze pro ukládání dat do úložiště Azure blob, pokud data se již nachází hlavního uzlu clusteru.

Chcete-li použít příkaz systému Hadoop, musíte nejdřív připojit k hlavnímu uzlu pomocí jedné z následujících metod:

* **HDInsight se systémem Windows**: [Připojit pomocí vzdálené plochy](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **HDInsight se systémem Linux**: Připojte se pomocí [SSH nebo PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

Jakmile budete připojeni, můžete k nahrání souboru do úložiště následující syntaxi.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Například `hadoop fs -copyFromLocal data.txt /example/data/data.txt`.

Protože výchozí systém souborů pro HDInsight je ve službě Azure Storage, /example/data.txt ve skutečnosti je ve službě Azure Storage. Můžete také odkazovat na soubor jako:

    wasb:///example/data/data.txt

nebo

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Seznam dalších příkazech Hadoop, které pracují se soubory naleznete v tématu [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Na clusterech Apache HBase zablokuje výchozí velikost použitou při zápisu dat je 256 KB. I když to funguje správně při použití rozhraní API HBase nebo rozhraní REST API, používat `hadoop` nebo `hdfs dfs` příkazy k zápisu dat větší než ~ 12 GB za následek chyby. Další informace najdete v tématu [výjimce úložiště pro zápis u objektu blob](#storageexception) části v tomto článku.

## <a name="graphical-clients"></a>Grafické klientů
Existuje také několik aplikací, které poskytuje grafické rozhraní pro práci s Azure Storage. V následující tabulce je seznam některé z těchto aplikací:

| Klient | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools pro HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer for Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |


## <a name="mount-azure-storage-as-local-drive"></a>Připojení Azure Storage jako místní disk
Zobrazit [připojení Azure Storage jako místní jednotku](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Nahrát pomocí služby
### <a name="azure-data-factory"></a>Azure Data Factory
Služba Azure Data Factory je plně spravovaná služba pro vytváření dat úložiště, zpracování dat a dat přesun služeb efektivní, škálovatelné a spolehlivé datové kanály, produkčního prostředí.

|Typ úložiště|Dokumentace|
|----|----|
|Azure Blob Storage|[Kopírování dat do nebo z úložiště objektů Blob v Azure pomocí Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Kopírování dat do nebo z Azure Data Lake Storage Gen1 pomocí služby Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Načtení dat do Azure Data Lake Storage Gen2 s Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop
Sqoop je nástroj určený pro přenos dat mezi platformou Hadoop a relačními databázemi. Můžete ho použít pro import dat ze systému správy relačních databází (RDBMS), jako jsou SQL Server, MySQL nebo Oracle do distribuovaného systému souborů Hadoop (HDFS), transformujte data v Hadoop MapReduce nebo Hive a pak exportovat data zpět do relační databázový systém.

Další informace najdete v tématu [Sqoop použití s HDInsight][hdinsight-use-sqoop].

### <a name="development-sdks"></a>Sady SDK pro vývoj
Úložiště Azure lze rovněž přistupovat pomocí sady Azure SDK z těchto programovacích jazycích:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Další informace o instalaci sady Azure SDK najdete v tématu [Azure, soubory ke stažení](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Řešení potíží
### <a id="storageexception"></a>Výjimka úložiště pro zápis u objektu blob
**Příznaky**: Při použití `hadoop` nebo `hdfs dfs` příkazy k zápisu souborů, které jsou přibližně 12 GB nebo více v clusteru služby HBase můžete setkat s následující chybu:

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

**Příčina:** HBase v HDInsight clustery výchozí velikost bloku 256 kB při zápisu do úložiště Azure. Zatímco funguje pro rozhraní API HBase nebo rozhraní REST API, je výsledkem k chybě při použití `hadoop` nebo `hdfs dfs` nástroje příkazového řádku.

**Řešení:** Použití `fs.azure.write.request.size` k určení větší velikost bloku. Můžete to provést na základě za použití pomocí `-D` parametru. Následující příkaz je uvedený příklad používající tento parametr se `hadoop` příkaz:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Můžete taky zvýšit hodnotu `fs.azure.write.request.size` globálně pomocí Apache Ambari. Následující postup slouží ke změně hodnoty v webovému uživatelskému rozhraní Ambari:

1. V prohlížeči přejděte na uživatelské rozhraní Ambari Web pro váš cluster. Toto je https://CLUSTERNAME.azurehdinsight.net, kde **CLUSTERNAME** je název vašeho clusteru.

    Po zobrazení výzvy zadejte uživatelské jméno admin a heslo pro cluster.
2. Na levé straně obrazovky vyberte **HDFS**a pak vyberte **Configs** kartu.
3. V **filtr...**  zadejte `fs.azure.write.request.size`. Zobrazí se pole a aktuální hodnota uprostřed stránky.
4. Změňte hodnotu z 262144 (256 KB) na novou hodnotu. Například 4194304 (4 MB).

![Obrázek změna hodnoty prostřednictvím webového uživatelského rozhraní Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Další informace o použití Ambari, naleznete v tématu [HDInsight Správa clusterů pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Další postup
Teď, když chápete, jak načíst data do HDInsight, přečtěte si, jak provádět analýzu následujících článcích:

* [Začínáme se službou Azure HDInsight][hdinsight-get-started]
* [Odeslání úloh systému Apache Hadoop prostřednictvím kódu programu][hdinsight-submit-jobs]
* [Použití Apache Hivu se službou HDInsight][hdinsight-use-hive]
* [Použití Apache Pig s HDInsight][hdinsight-use-pig]

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
