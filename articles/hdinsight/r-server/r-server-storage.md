---
title: Řešení úložiště Azure pro R serverem v HDInsight - Azure | Microsoft Docs
description: Další informace o možnostech jiného úložiště k dispozici s R serverem v HDInsight
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: nitinme
ms.openlocfilehash: 23e32a913fb73d2207f7cf37ce6230e428fbe95c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31409461"
---
# <a name="azure-storage-solutions-for-r-server-on-azure-hdinsight"></a>Řešení úložiště Azure pro R Server v Azure HDInsight

R serverem v HDInsight obsahuje celou řadu řešení úložiště k uchování dat, kódu nebo objektů, které obsahují výsledky z analýzy. Mezi ně patří následující možnosti:

- [Objekt Blob systému Azure](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/)
- [Úložiště Azure File](https://azure.microsoft.com/services/storage/files/)

Máte také možnost přístupu k několika účtům Azure storage nebo kontejnerů k vašemu clusteru HDInsight. Úložiště Azure File je možnost vhodná datová úložiště pro použití na hraniční uzel, který umožňuje připojení Azure úložné sdílené složky, například Linux systému souborů. Ale sdílené složky Azure File můžete připojit a používat libovolný systém, který nemá podporovaný operační systém, například Windows nebo Linux. 

Když vytvoříte Hadoop cluster v HDInsight, zadejte buď **úložiště Azure** účet nebo **úložiště Data Lake store**. Kontejner konkrétní úložiště z tohoto účtu obsahuje systému souborů pro cluster, který vytvoříte (například Hadoop Distributed File System). Další informace a pokyny najdete v tématu:

- [Používání Azure storage s HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Použití Data Lake Store s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-r-server-cluster"></a>Používat účty úložiště objektů Blob v Azure s clusterem s R Server

Pokud při vytváření clusteru R Server jste zadali více než jeden účet úložiště, následující pokyny popisují, jak používat sekundární účet pro přístup k datům a operace v R Server clusteru. Předpokládejme následující účty úložiště a kontejneru: **storage1** výchozí kontejner s názvem **container1**, a **storage2** s **container2**.

> [!WARNING]
> Z důvodů výkonu se HDInsight cluster vytvoří ve stejném datovém centru jako účet primárního úložiště, který určíte. Použití účtu úložiště v jiném umístění než HDInsight cluster není podporováno.

### <a name="use-the-default-storage-with-r-server-on-hdinsight"></a>Použít výchozí úložiště s R serverem v HDInsight

1. Pomocí klienta SSH, připojte k okrajovému uzlu clusteru. Informace o používání SSH s HDInsight clustery najdete v tématu [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Ukázkový soubor mysamplefile.csv, zkopírujte do adresáře/Share. 

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal mycsv.scv /share  

3. Přepněte do R Studio nebo jinou konzolu R a napsat kód R nastavit na název uzlu **výchozí** a umístění souboru, kterému chcete přistupovat.  

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

Všechny adresáře a souboru odkazy přejděte na účet úložiště `wasb://container1@storage1.blob.core.windows.net`. Toto je **výchozí účet úložiště** který je spojen s clusterem HDInsight.

### <a name="use-the-additional-storage-with-r-server-on-hdinsight"></a>Dodatečné úložiště pomocí R serverem v HDInsight

Nyní předpokládejme, že chcete zpracovat soubor s názvem mysamplefile1.csv, který je umístěný v /private adresář **container2** v **storage2**.

Ve vašem kódu R bodu název uzlu odkazu na **storage2** účet úložiště.

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

Všechny adresáře a souboru odkazy nyní přejděte na účet úložiště `wasb://container2@storage2.blob.core.windows.net`. Toto je **název uzlu** který jste zadali.

Budete muset nakonfigurovat User/RevoShare/<SSH username> v **storage2** následujícím způsobem:


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## <a name="use-an-azure-data-lake-store-with-r-server-cluster"></a>R Server clusteru pomocí Azure Data Lake Store 

Pokud chcete používat Data Lake Store k vašemu clusteru HDInsight, budete muset poskytnout vašeho clusteru přístup k každý Azure Data Lake Store, který chcete použít. Pokyny o tom, jak pomocí portálu Azure k vytvoření clusteru HDInsight pomocí účtu Azure Data Lake Store jako výchozí úložiště nebo jako další úložiště najdete v tématu [vytvoření clusteru HDInsight s Data Lake Store pomocí portálu Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Pak použijete úložišti ve vašem skriptu R mnohem stejně, jako jste účet sekundární úložiště Azure, jak je popsáno v předchozím postupu.

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Přístup ke clusteru přidat do vašeho úložiště Azure Data Lake
Máte přístup k úložiště Data Lake store pomocí objektu služby Azure Active Directory (Azure AD), který je spojen s clusteru HDInsight.

1. Při vytváření clusteru HDInsight, vyberte **identita AAD clusteru** z **zdroj dat** kartě.

2. V **identita AAD clusteru** dialogovém **vyberte objekt služby AD**, vyberte **vytvořit nový**.

Po zadejte název objektu služby a vytvořit heslo pro něj, klikněte na tlačítko **Správa přístupu ADLS** přidružit objekt služby Data Lake Store.

Je také možné přidat přístup ke clusteru na jeden nebo více účtů Data Lake Store, po vytvoření clusteru. Otevřete položku portálu Azure Data Lake Store a přejděte na **Průzkumníku dat > přístup > Přidat**. 

### <a name="how-to-access-the-data-lake-store-from-r-server-on-hdinsight"></a>Jak získat přístup k úložišti Data Lake z R serverem v HDInsight

Jakmile jste dali přístup do Data Lake Store, můžete použít úložiště v clusteru serveru R na HDInsight způsob, jakým byste účet sekundární úložiště Azure. Jediným rozdílem je, že předpona **wasb: / /** změny **adl: / /** následujícím způsobem:


    # Point to the ADL store (e.g. ADLtest)
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

Konfigurace účtu Data Lake Store s RevoShare adresáře a přidejte ukázkový soubor .csv z předchozího příkladu se používají následující příkazy:


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-r-server-on-hdinsight"></a>Používání Azure File storage s R serverem v HDInsight

Je také možnost vhodná datová úložiště pro použití v uzlu edge volat [soubory Azure] ((https://azure.microsoft.com/services/storage/files/). Umožňuje připojit Azure úložné sdílené složky systému souborů Linux. Tato možnost může být užitečný pro ukládání datové soubory, skripty R a objektů výsledků, které může být potřeba později, zejména v případě, že má smysl pro systém nativní souborů na uzlu edge namísto HDFS. 

Hlavní výhodou soubory Azure je, sdílené složky můžete připojit a používat systémem, který má podporovaný operační systém, například Windows nebo Linux. Například můžete použít jiný cluster HDInsight s někým ve vašem týmu, virtuální počítač Azure nebo i v místním systému. Další informace naleznete v tématu:

- [Jak používat Azure File Storage s Linuxem](../../storage/files/storage-how-to-use-files-linux.md)
- [Jak používat Azure File storage ve Windows](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Další postup

* [Přehled cluster R serverem v HDInsight](r-server-overview.md)
* [Začínáme s R Server clusteru v systému Hadoop](r-server-get-started.md)
* [Možnosti výpočetního kontextu pro cluster R Serveru v HDInsight](r-server-compute-contexts.md)

