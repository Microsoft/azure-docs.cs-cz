---
title: Řešení Azure Storage pro služby ML na HDInsight – Azure
description: Další informace o možnostech jiného úložiště k dispozici služby ML v HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 888cc9d894861c7d7b808500cd60bbed9faa05d7
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56309113"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Řešení Azure Storage pro ML služeb v Azure HDInsight

Služby ML na HDInsight využívat širokou škálu řešení úložišť se zachovat data, kód nebo objekty, které obsahují výsledky analýzy. Mezi ně patří následující možnosti:

- [Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Azure File storage](https://azure.microsoft.com/services/storage/files/)

Máte také možnost přístupu k více účtům úložiště Azure nebo kontejnery s vaším clusterem HDInsight. Azure File storage je možnost úložiště dat vhodné pro použití na hraničním uzlu, umožňující připojení sdílené složky Azure Storage, například systém souborů Linux. Ale sdílených složek Azure můžete připojit a používat libovolný systém, který je podporovaný operační systém, jako je například Windows nebo Linux. 

Když vytvoříte cluster Apache Hadoop v HDInsight, můžete zadat buď **služby Azure storage** účtu nebo **Data Lake Storage**. Kontejner konkrétní úložiště z tohoto účtu obsahuje systém souborů pro cluster, který vytvoříte (například Hadoop Distributed File System). Další informace a pokyny najdete v tématu:

- [Použití služby Azure storage s HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Použití Data Lake Storage s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Použít účty úložiště objektů Blob v Azure s clusterem služby ML

Pokud je zadán více než jeden účet úložiště při vytváření clusteru služby ML, následující pokyny vysvětlují, jak používat sekundární účet pro přístup k datům a operace v clusteru služby ML. Předpokládejme následující účty úložiště a kontejneru: **storage1** výchozí kontejner s názvem **container1**, a **storage2** s **container2**.

> [!WARNING]  
> Z důvodů výkonu se HDInsight cluster vytvoří ve stejném datovém centru jako primární účet úložiště, který zadáte. Použití účtu úložiště v jiném umístění než HDInsight cluster se nepodporuje.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Použít výchozí úložiště služby ML v HDInsight

1. Pomocí klienta SSH se připojte k hraničnímu uzlu clusteru. Informace o použití SSH s clustery HDInsight najdete v tématu [použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Ukázkový soubor mysamplefile.csv, zkopírujte do adresáře/Share. 

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal mycsv.scv /share  

3. Přepněte do R Studio nebo jinou konzolu R a psaní kódu jazyka R se nastavit název uzlu na **výchozí** a umístění souboru, které chcete získat přístup.  

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

Všechny adresáře a souboru odkazy přejděte na účet úložiště `wasb://container1@storage1.blob.core.windows.net`. Toto je **výchozí účet úložiště** , který je asociován s clusterem HDInsight.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Dodatečné úložiště pomocí služby ML v HDInsight

Nyní předpokládejme, že chcete zpracovat soubor s názvem mysamplefile1.csv, který se nachází v /private adresáři **container2** v **storage2**.

Ve vašem kódu R odkazovat název uzlu odkazu na **storage2** účtu úložiště.

    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
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

Všechny odkazy na adresáře a souboru teď přejděte na účet úložiště `wasb://container2@storage2.blob.core.windows.net`. Toto je **název uzlu** , které jste zadali.

Budete muset nakonfigurovat/User/RevoShare/<SSH username> adresáři **storage2** následujícím způsobem:


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## <a name="use-azure-data-lake-storage-with-ml-services-cluster"></a>Použití služby Azure Data Lake Storage s clusterem služby ML 

Použití Data Lake Storage s vaším clusterem HDInsight, budete muset poskytnout vašemu clusteru přístup k každý Azure Data Lake Storage, který chcete použít. Pokyny o tom, jak pomocí webu Azure portal k vytvoření clusteru HDInsight s účtem úložiště Azure Data Lake jako výchozí úložiště nebo jako další úložiště najdete v tématu [vytvoření clusteru HDInsight s Data Lake Storage pomocí webu Azure portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Pak použijete úložiště ve skriptu R mnohem stejně, jako jste účet sekundární úložiště Azure, jak je popsáno v předchozím postupu.

### <a name="add-cluster-access-to-your-azure-data-lake-storage"></a>Přidání clusteru přístup k službě Azure Data Lake Storage
Získáte přístup k Data Lake Storage pomocí instančního objektu Azure Active Directory (Azure AD), který je spojen s vaším clusterem HDInsight.

1. Při vytváření clusteru HDInsight, vyberte **Identity AAD clusteru** z **zdroj dat** kartu.

2. V **Identity AAD clusteru** dialogovém okně **vyberte instanční objekt služby AD**vyberte **vytvořit nový**.

Po pojmenujte instančního objektu a vytvoří se pro něj heslo, klikněte na tlačítko **spravovat přístup ADLS** přidružit vaše Data Lake Storage instanční objekt služby.

Je také možné přidat přístup ke clusteru na jeden nebo více účtů Data Lake Storage po vytvoření clusteru. Otevřete Azure portal položka pro Data Lake Storage a přejděte na **Průzkumník dat > přístup > Přidat**. 

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Jak získat přístup k Data Lake Storage Gen1 od služby ML v HDInsight

Jakmile jste dali přístup k Data Lake Storage Gen1, můžete použít úložiště v clusteru služby ML v HDInsight způsob, jakým byste to udělali účet sekundární úložiště Azure. Jediným rozdílem je, že předpona **wasb: / /** změny **adl: / /** následujícím způsobem:


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

Ke konfiguraci účtu Data Lake Storage Gen1 adresářem RevoShare a přidejte ukázkový soubor .csv z předchozího příkladu se používají následující příkazy:


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Azure File storage pomocí služby ML v HDInsight

Je také možnost vhodná datová úložiště pro použití na hraniční uzel s názvem [Azure Files](https://azure.microsoft.com/services/storage/files/). Umožňuje připojení sdílené složky Azure Storage k systému souborů v Linuxu. Tato možnost může být užitečná pro ukládání datových souborů, skripty jazyka R a objektech výsledků, které bude potřeba provést později, zejména v případě, že je vhodné použít systém nativní soubor na hraničním uzlu namísto HDFS. 

Hlavní výhodou souborů Azure je, že sdílené složky můžete připojit a používat jakýkoli systém, který je podporovaný operační systém, jako je například Windows nebo Linux. Například můžete použít jiný cluster HDInsight, který má vy nebo někdo z vašeho týmu, ve Virtuálním počítači Azure nebo dokonce místního systému. Další informace naleznete v tématu:

- [Jak používat Azure File Storage s Linuxem](../../storage/files/storage-how-to-use-files-linux.md)
- [Jak používat Azure File storage ve Windows](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Další postup

* [Přehled služby ML clusteru v HDInsight](r-server-overview.md)
* [Začínáme s clusterem služby ML na Apache Hadoopu](r-server-get-started.md)
* [Možnosti výpočetního kontextu pro cluster služby ML Services v HDInsight](r-server-compute-contexts.md)
* [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
