---
title: Nahrávání dat pro úlohy Apache Hadoop v HDInsight
description: Naučte se nahrávat a přistupovat k datům pro úlohy Apache Hadoop v HDInsight. Použijte Azure Classic CLI, Průzkumník služby Azure Storage, Azure PowerShell, příkazový řádek Hadoop nebo Sqoop.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdiseo17may2017,seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: a8d21b88298b6092c869b89d48fe4c259c2365b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98931343"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Nahrávání dat pro úlohy Apache Hadoop v HDInsight

HDInsight poskytuje systém Hadoop Distributed File System (HDFS) po Azure Storage a Azure Data Lake Storage. Toto úložiště zahrnuje Gen1 a Gen2. Azure Storage a Data Lake Storage Gen1 a Gen2 jsou navržené jako HDFS Extensions. Umožňují v prostředí Hadoop pracovat přímo s daty, která spravuje. Azure Storage, Data Lake Storage Gen1 a Gen2 jsou odlišné systémy souborů. Systémy jsou optimalizované pro ukládání dat a výpočtů na těchto datech. Informace o výhodách použití Azure Storage najdete v tématu [použití Azure Storage se službou HDInsight](hdinsight-hadoop-use-blob-storage.md). Přečtěte si také téma [použití Data Lake Storage Gen1 se službou HDInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)a [použití Data Lake Storage Gen2 se službou HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, vezměte na vědomí následující požadavky:

* Cluster Azure HDInsight. Pokyny najdete v tématu Začínáme [se službou Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Znalost následujících článků:
    * [Použití Azure Storage se službou HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Použití Data Lake Storage Gen1 se službou HDInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)
    * [Použití Data Lake Storage Gen2 se službou HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Nahrání dat do Azure Storage

### <a name="utilities"></a>Nástroje

Společnost Microsoft poskytuje následující nástroje pro práci s Azure Storage:

| Nástroj | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Příkaz Hadoop](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> Příkaz Hadoop je k dispozici pouze v clusteru HDInsight. Příkaz umožňuje načíst data z místního systému souborů do Azure Storage.  

### <a name="hadoop-command-line"></a>Příkazový řádek Hadoop

Příkazový řádek Hadoop je užitečný jenom pro ukládání dat do objektu BLOB služby Azure Storage, když jsou data v hlavním uzlu clusteru již přítomna.

Pokud chcete použít příkaz Hadoop, musíte se nejdřív připojit k hlavnímu uzlu pomocí protokolu [SSH nebo výstupu](hdinsight-hadoop-linux-use-ssh-unix.md).

Po připojení můžete k nahrání souboru do úložiště použít následující syntaxi.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Například `hadoop fs -copyFromLocal data.txt /example/data/data.txt`.

Vzhledem k tomu, že výchozí systém souborů pro HDInsight je ve Azure Storage, je data.txt/example/data/ve skutečnosti v Azure Storage. Můžete také odkazovat na soubor jako:

`wasbs:///example/data/data.txt`

nebo

`wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt`

Seznam dalších příkazů Hadoop, které pracují se soubory, najdete v tématu. [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> V clusterech Apache HBA je výchozí velikost bloku použitá při zápisu dat 256 KB. I když při použití rozhraní API HBA nebo rozhraní REST API funguje bez použití `hadoop` příkazů nebo `hdfs dfs` k zápisu dat větších než ~ 12 GB, dojde k chybě. Další informace najdete v tématu [výjimka úložiště pro zápis do objektu BLOB](hdinsight-troubleshoot-hdfs.md#storage-exception-for-write-on-blob).

### <a name="graphical-clients"></a>Grafické klienty

K dispozici je také několik aplikací, které poskytují grafické rozhraní pro práci s Azure Storage. V následující tabulce jsou uvedeny některé z těchto aplikací:

| Klient | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio nástroje pro HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer pro Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Připojit Azure Storage jako místní disk

Viz možnost [připojit Azure Storage jako místní disk](/archive/blogs/bigdatasupport/mount-azure-blob-storage-as-local-drive).

## <a name="upload-using-services"></a>Nahrávání pomocí služeb

### <a name="azure-data-factory"></a>Azure Data Factory

Služba Azure Data Factory je plně spravovaná služba pro vytváření dat: úložiště, zpracování a přesun služeb do zjednodušených, přizpůsobitelných a spolehlivých produkčních kanálů.

|Typ úložiště|Dokumentace|
|----|----|
|Azure Blob Storage|[Kopírování dat do nebo z úložiště Azure Blob Storage pomocí služby Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Kopírování dat do nebo z Azure Data Lake Storage Gen1 pomocí Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Načtení dat do Azure Data Lake Storage Gen2 s využitím Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop je nástroj určený k přenosu dat mezi Hadoop a relačními databázemi. Slouží k importu dat ze systému pro správu relačních databází (RDBMS), například SQL Server, MySQL nebo Oracle. Pak do systému Hadoop Distributed File System (HDFS). Transformujte data v Hadoop pomocí MapReduce nebo podregistru a potom data exportujte zpátky do RDBMS.

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

## <a name="next-steps"></a>Další kroky

Teď, když rozumíte tomu, jak získat data do HDInsight, si přečtěte následující články, které vám pomohou analyzovat tyto informace:

* [Začínáme se službou Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Programové odeslání Apache Hadoop úloh](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Použití Apache Hive se službou HDInsight](hadoop/hdinsight-use-hive.md)