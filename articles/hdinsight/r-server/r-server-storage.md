---
title: Řešení úložišť Azure pro služby ML na HDInsight – Azure
description: Informace o různých možnostech úložiště dostupných ve službách ML na HDInsightu
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: 1c79d0390a80a1358ddb09707fbabf6a5a2affdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660235"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Řešení úložišť Azure pro služby ML ve službě Azure HDInsight

Ml Services na HDInsight můžete použít různá řešení úložiště k uchování dat, kódu nebo objektů, které obsahují výsledky analýzy. Mezi tato řešení patří následující možnosti:

- [Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Úložiště souborů Azure](https://azure.microsoft.com/services/storage/files/)

Máte také možnost přístupu k více účtům úložiště Azure nebo kontejnerům s vaším clusterem HDInsight. Azure File Storage je výhodná možnost ukládání dat pro použití na hraničním uzlu, která umožňuje připojit sdílenou složku úložiště Azure, například do souborového systému Linux. Sdílené složky Azure file však můžou být připojeny a používány libovolným systémem, který má podporovaný operační systém, jako je Windows nebo Linux.

Když vytvoříte cluster Apache Hadoop ve hdinsightu, určíte buď účet **Azure Storage,** nebo **úložiště datového jezera**. Konkrétní kontejner úložiště z tohoto účtu obsahuje systém souborů pro cluster, který vytvoříte (například Hadoop Distribuovaný systém souborů). Další informace a pokyny naleznete v těchto tématech:

- [Použití azure storage s HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Použití úložiště datových jezer s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Použití účtů úložiště objektů blob Azure s clusterem služeb ML

Pokud jste při vytváření clusteru služeb ML zadali více než jeden účet úložiště, následující pokyny vysvětlují, jak použít sekundární účet pro přístup k datům a operace v clusteru služeb ML. Předpokládejme následující účty úložiště a kontejner: **storage1** a výchozí kontejner s názvem **container1**a **storage2** s **container2**.

> [!WARNING]  
> Pro účely výkonu se cluster HDInsight vytvoří ve stejném datovém centru jako zadaný účet primárního úložiště. Použití účtu úložiště v jiném umístění než cluster HDInsight není podporováno.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Použití výchozího úložiště se službami ML ve službě HDInsight

1. Pomocí klienta SSH se připojte k hraničnímu uzlu clusteru. Informace o používání SSH v clusterech HDInsight najdete v tématu [Použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Zkopírujte ukázkový soubor mysamplefile.csv do adresáře /share.

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. Přepněte do aplikace R Studio nebo jiné konzoly R a napište kód R, který nastaví uzel názvu na **výchozí** a umístění souboru, ke kterého chcete získat přístup.  

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

Všechny odkazy na adresář a soubory `wasbs://container1@storage1.blob.core.windows.net`odkazují na účet úložiště . Toto je **výchozí účet úložiště,** který je přidružen ke clusteru HDInsight.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Použití dalšího úložiště se službami ML na HDInsightu

Nyní předpokládejme, že chcete zpracovat soubor s názvem mysamplefile1.csv, který je umístěn v /private **adresáři container2** v **storage2**.

V kódu R nasměrujte odkaz na uzel názvu na účet úložiště **storage2.**

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

Všechny odkazy na adresář a soubory nyní `wasbs://container2@storage2.blob.core.windows.net`odkazují na účet úložiště . Toto je **uzel názvu,** který jste zadali.

Nakonfigurujte adresář na `/user/RevoShare/<SSH username>` **úložišti2** následujícím způsobem:

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-with-ml-services-cluster"></a>Použití clusteru Azure Data Lake Storage se službami ML

Pokud chcete úložiště datových jezer používat s clusterem HDInsight, musíte svému clusteru udělit přístup ke každému úložišti Azure Data Lake Storage, které chcete použít. Pokyny, jak pomocí portálu Azure portal vytvořit cluster HDInsight s účtem Azure Data Lake Storage jako výchozíúložiště nebo jako další úložiště, najdete v [tématu Vytvoření clusteru HDInsight s úložištěm datových jezer pomocí portálu Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Potom použijte úložiště ve skriptu R podobně jako jste provedli sekundární účet úložiště Azure, jak je popsáno v předchozím postupu.

### <a name="add-cluster-access-to-your-azure-data-lake-storage"></a>Přidání přístupu k clusteru do úložiště Datových jezer Azure

Přístup k úložišti Data Lake Storage pomocí hlavního mocnina služby Azure Active Directory (Azure AD), který je přidružený k vašemu clusteru HDInsight.

1. Při vytváření clusteru HDInsight vyberte **identitu AAD clusteru** na kartě **Zdroj dat.**

2. V dialogovém okně **Identita služby AAD clusteru** vyberte v části **Vybrat zaregistrovaný objekt služby Služby AD** **položku Vytvořit nový**.

Po zadání názvu objektu služby a vytvoření hesla pro něj klikněte na **Spravovat přístup ADLS** a přidružte instanční objekt k úložišti datového jezera.

Je také možné přidat přístup clusteru k jednomu nebo více účtů úložiště datového jezera po vytvoření clusteru. Otevřete položku portálu Azure pro úložiště datových jezer a přejděte na **> > Přidat > Průzkumníka dat**.

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Jak získat přístup k datově jezeru Storage Gen1 ze služeb ML na HDInsightu

Jakmile uděláte přístup k Gen1 úložiště datového jezera, můžete použít úložiště v clusteru SLUŽEB ML na HDInsight stejně jako sekundární účet úložiště Azure. Jediným rozdílem je, že předpona **wasbs://** změní **na adl://** takto:

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

Následující příkazy slouží ke konfiguraci účtu Data Lake Storage Gen1 s adresářem RevoShare a k přidání ukázkového souboru CSV z předchozího příkladu:

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Použití úložiště souborů Azure se službami ML na HDInsightu

K dispozici je také možnost pohodlného ukládání dat pro použití na hraničním uzlu s názvem [Soubory Azure](https://azure.microsoft.com/services/storage/files/). Umožňuje připojení sdílené složky Azure Storage do systému souborů Linux. Tato možnost může být užitečná pro ukládání datových souborů, skriptů R a výsledných objektů, které mohou být potřebné později, zejména pokud má smysl používat nativní systém souborů na hraničním uzlu spíše než HDFS.

Hlavní výhodou souborů Azure je, že sdílené složky mohou být připojeny a používány libovolným systémem, který má podporovaný operační systém, jako je Windows nebo Linux. Například jej může použít jiný cluster HDInsight, který máte vy nebo někdo z vašeho týmu, virtuální počítač Azure nebo dokonce místní systém. Další informace naleznete v tématu:

- [Jak používat Azure File Storage s Linuxem](../../storage/files/storage-how-to-use-files-linux.md)
- [Jak používat úložiště souborů Azure ve Windows](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Další kroky

- [Přehled clusteru služeb ML na HDInsightu](r-server-overview.md)
- [Možnosti výpočetního kontextu pro cluster služby ML Services v HDInsight](r-server-compute-contexts.md)
- [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
