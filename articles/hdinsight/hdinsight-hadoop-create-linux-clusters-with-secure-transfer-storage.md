---
title: Vytvoření clusteru Hadoop s účty úložiště s bezpečným přenosem ve službě Azure HDInsight
description: Naučte se vytvářet clustery HDInsight s účty úložiště Azure s povoleným zabezpečeným přístupem.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/24/2018
ms.openlocfilehash: f3c6eabeac74df8ea88cf1a0670af9624e98771a
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935177"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Vytvoření clusteru Apache Hadoop s účty úložiště zabezpečeného přenosu ve službě Azure HDInsight

Funkce [Vyžadovat zabezpečený přenos](../storage/common/storage-require-secure-transfer.md) zvyšuje zabezpečení účtu služby Azure Storage tím, že vynucuje přenos všech požadavků na účet přes zabezpečené připojení. Tuto funkci a schéma wasbs podporují pouze clustery HDInsight verze 3.6 nebo novější.

## <a name="prerequisites"></a>Požadavky
Než začnete tento článek, musíte mít:

* **Předplatné Azure**: Pokud chcete vytvořit bezplatný zkušební účet na jeden měsíc, přejděte na [Azure.Microsoft.com/Free](https://azure.microsoft.com/free).
* **Účet služby Azure Storage s povoleným zabezpečeným přenosem**. Pokyny najdete v tématech popisujících [vytvoření účtu úložiště](../storage/common/storage-quickstart-create-account.md) a funkci [Vyžadovat zabezpečený přenos](../storage/common/storage-require-secure-transfer.md).
* **Kontejner objektů blob v účtu úložiště**.

## <a name="create-cluster"></a>Vytvoření clusteru

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


V této části vytvoříte cluster Hadoop ve službě HDInsight pomocí [šablony Azure Resource Manageru](../azure-resource-manager/resource-group-template-deploy.md). Šablona se nachází v [GitHubu](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/). Zkušenosti se šablonami Resource Manageru se k postupu podle tohoto článku nevyžadují. Další metody vytváření clusterů a porozumění vlastnostem používaným v tomto článku najdete v tématu [Vytvoření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Klikněte na následující obrázek pro přihlášení do Azure a otevřete šablonu Resource Manageru na webu Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage/hdi-deploy-to-azure1.png" alt="Deploy to Azure"></a>

2. Postupujte podle pokynů a vytvořte cluster s následujícími specifikacemi: 

    - Zadejte verzi služby HDInsight 3.6. Vyžaduje se verze 3.6 nebo novější.
    - Zadejte účet úložiště s povoleným zabezpečeným přenosem.
    - Použijte krátký název účtu úložiště.
    - Účet úložiště i kontejner objektů blob je potřeba vytvořit předem.

      Pokyny najdete v tématu popisujícím [vytvoření clusteru](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

Pokud k zadání vlastních konfiguračních souborů použijete akci skriptu, musíte v následujících nastaveních použít wasbs:

- fs.defaultFS (základní web)
- spark.eventLog.dir 
- spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>Přidání dalších účtů úložiště

Existuje několik možností, jak přidat další účty úložiště s povoleným zabezpečeným přenosem:

- Úprava šablony Azure Resource Manageru v poslední části.
- Vytvoření clusteru pomocí webu [Azure Portal](https://portal.azure.com) a zadání propojeného účtu úložiště.
- Použití akce skriptu k přidání dalších účtů úložiště s povoleným zabezpečeným přenosem do existujícího clusteru HDInsight. Další informace najdete v tématu [Přidání dalších účtů úložiště do služby HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Další postup
V tomto článku jste se naučili, jak vytvořit cluster HDInsight a jak povolit zabezpečený přenos do účtů úložiště.

Další informace o analýze dat pomocí HDInsight najdete v následujících článcích:

* Další informace o použití [Apache Hive](https://hive.apache.org/) se službou HDInsight, včetně toho, jak provádět dotazy na podregistry ze sady Visual Studio, najdete v tématu [použití Apache Hive se službou HDInsight][hdinsight-use-hive].
* Další informace o [Apache vepřovém](https://pig.apache.org/)jazyce, který slouží k transformaci dat, najdete v tématu [použití nástroje Apache prasete se službou HDInsight][hdinsight-use-pig].
* Další informace o [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), způsobu psaní programů, které zpracovávají data v systému Hadoop, najdete v tématu [použití Apache Hadoop MapReduce se službou HDInsight][hdinsight-use-mapreduce].
* Další informace o používání nástrojů HDInsight pro Visual Studio k analýze dat v HDInsight najdete v tématu [Začínáme používat Visual Studio Apache Hadoop Tools for HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Další informace o způsobu, jakým služba HDInsight ukládá data, nebo jak přenést data do služby HDInsight, najdete v následujících článcích:

* Informace o tom, jak HDInsight používá Azure Storage, najdete v tématu [Používání Azure Storage s HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Informace o tom, jak nahrát data do služby HDInsight, najdete v tématu [Nahrání dat do služby HDInsight][hdinsight-upload-data].

Další informace o vytvoření a správě clusteru HDInsight najdete v následujících článcích:

* Další informace o správě clusteru HDInsight se systémem Linux najdete v tématu [Správa clusterů HDInsight pomocí Apache Ambari](hdinsight-hadoop-manage-ambari.md).
* Další informace o možnostech, které můžete vybrat při vytváření clusteru služby HDInsight, najdete v tématu [Vytváření HDInsight na Linuxu pomocí vlastních možností](hdinsight-hadoop-provision-linux-clusters.md).
* Pokud jste obeznámeni se systémem Linux a Apache Hadoop, ale chcete znát konkrétní informace o Hadoop v HDInsight, přečtěte si téma [práce se službou HDInsight v systému Linux](hdinsight-hadoop-linux-information.md). Tento článek obsahuje informace o:

  * Adresy URL pro služby hostované v clusteru, jako je [Apache Ambari](https://ambari.apache.org/) a [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)
  * Umístění souborů a příkladů [Apache Hadoop](https://hadoop.apache.org/) v místním systému souborů
  * Použití Azure Storage (WASB) místo [Apache HADOOP HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) jako výchozího úložiště dat

[1]: ../HDInsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
