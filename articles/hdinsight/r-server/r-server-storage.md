---
title: Řešení Azure Storage pro služby ML ve službě HDInsight – Azure
description: Seznamte se s různými možnostmi úložiště dostupnými pro služby ML v HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/02/2020
ms.openlocfilehash: ddc48025de164ff68fb539a293e06bae09171742
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943903"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Řešení Azure Storage pro služby ML ve službě Azure HDInsight

Služby ML Services v HDInsight můžou používat různá řešení úložiště k uchovávání dat, kódu nebo objektů, které obsahují výsledky z analýz. Tato řešení zahrnují následující možnosti:

- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage Gen1](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Azure File Storage](https://azure.microsoft.com/services/storage/files/)

Máte také možnost získat přístup k více účtům nebo kontejnerům Azure Storage s clusterem HDInsight. Služba Azure File Storage je pohodlný způsob ukládání dat pro použití na hraničním uzlu, který umožňuje připojit sdílenou složku úložiště Azure, například systém souborů Linux. Sdílené složky Azure je ale možné připojit a používat jakýmkoli systémem, který má podporovaný operační systém, jako je Windows nebo Linux.

Když vytvoříte cluster Apache Hadoop v HDInsight, zadáte buď účet služby **Azure Blob Storage** , nebo **Data Lake Storage Gen1**. Konkrétní kontejner úložiště z tohoto účtu obsahuje systém souborů pro cluster, který vytvoříte (například systém souborů DFS (Distributed File System) Hadoop). Další informace a pokyny najdete v těchto tématech:

- [Použití úložiště objektů BLOB v Azure se službou HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Použití Data Lake Storage Gen1 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Použití účtů úložiště Azure BLOB v clusteru služby ML

Pokud jste při vytváření clusteru služby ML zadali více než jeden účet úložiště, následující pokyny vysvětlují, jak použít sekundární účet pro přístup k datům a operace v clusteru služby ML. Předpokládejme následující účty úložiště a kontejner: **storage1** a výchozí kontejner s názvem **container1** a **storage2** s **container2**.

> [!WARNING]  
> Pro účely výkonu se cluster HDInsight vytvoří ve stejném datovém centru jako primární účet úložiště, který zadáte. Použití účtu úložiště v jiném umístění, než je cluster HDInsight, se nepodporuje.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Použití výchozího úložiště se službami ML v HDInsight

1. Pomocí klienta SSH se připojte k hraničnímu uzlu clusteru. Informace o použití SSH s clustery HDInsight najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Zkopírujte ukázkový soubor mysamplefile.csv do adresáře/Share.

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. Přepněte na R Studio nebo jinou konzolu R a napište kód R, abyste nastavili uzel název na **výchozí** a umístění souboru, ke kterému chcete získat přístup.  

    ```R
    myNameNode <- "default"
    myPort <- 0

    #Location of the data:  
    bigDataDirRoot <- "/share"  

    #Define Spark compute context:
    mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define the Hadoop Distributed File System (HDFS) file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
    ```

Všechny odkazy adresáře a souborů odkazují na účet úložiště `wasbs://container1@storage1.blob.core.windows.net` . Toto je **výchozí účet úložiště** , který je přidružený ke clusteru HDInsight.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Použití dalšího úložiště se službami ML v HDInsight

Nyní předpokládejme, že chcete zpracovat soubor s názvem mysamplefile1.csv, který je umístěn v adresáři/Private **container2** v **storage2**.

V kódu R nastavte odkaz na uzel název na účet úložiště **storage2** .

```R
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
myPort <- 0

#Location of the data:
bigDataDirRoot <- "/private"

#Define Spark compute context:
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

#Set compute context:
rxSetComputeContext(mySparkCluster)

#Define HDFS file system:
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

#Specify the input file to analyze in HDFS:
inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")
```

Všechny odkazy adresáře a souborů teď odkazují na účet úložiště `wasbs://container2@storage2.blob.core.windows.net` . Toto je **název uzlu** , který jste zadali.

Nakonfigurujte `/user/RevoShare/<SSH username>` adresář v **storage2** následujícím způsobem:

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-gen1-with-ml-services-cluster"></a>Použití Azure Data Lake Storage Gen1 s clusterem služeb ML

Pokud chcete použít Data Lake Storage Gen1 s clusterem HDInsight, musíte vašemu clusteru udělit přístup ke každému Azure Data Lake Storage Gen1, který chcete použít. Pokyny, jak pomocí Azure Portal vytvořit cluster HDInsight s Azure Data Lake Storage Gen1 jako výchozí úložiště nebo jako další úložiště, najdete v tématu [Vytvoření clusteru HDInsight s Data Lake Storage Gen1 pomocí Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Pak použijete úložiště ve skriptu R podobně jako u sekundárního účtu úložiště Azure, jak je popsáno v předchozím postupu.

### <a name="add-cluster-access-to-your-azure-data-lake-storage-gen1"></a>Přidání přístupu k clusteru do Azure Data Lake Storage Gen1

K Data Lake Storage Gen1 přistupujete pomocí instančního objektu služby Azure Active Directory (Azure AD), který je přidružený k vašemu clusteru HDInsight.

1. Když vytváříte cluster HDInsight, vyberte z karty **zdroj dat** **cluster Azure AD identity** .

2. V dialogovém okně **cluster Azure AD identity** vyberte v části **Vybrat instanční objekt služby Active Directory** možnost **vytvořit nový**.

Když zadáte název instančního objektu a vytvoříte pro něj heslo, klikněte na **spravovat přístup adls** a přidružte objekt služby k vašemu Data Lake Storage.

Při vytváření clusteru je také možné přidat přístup clusteru k jednomu nebo více Data Lakech účtů Gen1 úložiště. Otevřete položku Azure Portal pro Data Lake Storage Gen1 a přejděte na **Průzkumník dat > přístup > přidat**.

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Přístup k Data Lake Storage Gen1 ze služby ML Services ve službě HDInsight

Jakmile budete mít přístup k Data Lake Storage Gen1, můžete použít cluster úložiště v ML služby v HDInsight tak, jak byste měli sekundární účet Azure Storage. Jediným rozdílem je, že předpona **wasbs://** změny **ADL://** následujícím způsobem:

```R
# Point to the ADL Storage (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
```

Následující příkazy se používají ke konfiguraci Data Lake Storage Gen1 v adresáři RevoShare a přidání ukázkového souboru. CSV z předchozího příkladu:

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Použití služby Azure File Storage se službami ML v HDInsight

K dispozici je také vhodná možnost úložiště dat pro použití na hraničním uzlu s názvem [soubory Azure](https://azure.microsoft.com/services/storage/files/). Umožňuje připojení sdílené složky souborů Azure Storage k systému souborů Linux. Tato možnost může být užitečná pro ukládání datových souborů, skriptů R a objektů výsledků, které může být potřeba později, zejména v případě, kdy je vhodné použít nativní souborový systém na hraničním uzlu místo HDFS.

Hlavní výhodou souborů Azure je, že sdílené složky je možné připojit a používat v jakémkoli systému, který má podporovaný operační systém, jako je Windows nebo Linux. Můžete ho například použít v jiném clusteru HDInsight, který vy nebo někdo z týmu používá virtuální počítač Azure, nebo dokonce místní systém. Další informace naleznete v tématu:

- [Jak používat Azure File Storage s Linuxem](../../storage/files/storage-how-to-use-files-linux.md)
- [Jak používat službu Azure File Storage ve Windows](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Další kroky

- [Přehled clusteru služeb ML v HDInsight](r-server-overview.md)
- [Možnosti výpočetního kontextu pro cluster služby ML Services v HDInsight](r-server-compute-contexts.md)
- [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
