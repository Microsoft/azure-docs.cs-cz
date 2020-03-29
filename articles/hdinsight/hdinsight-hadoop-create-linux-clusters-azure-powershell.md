---
title: Vytváření clusterů Apache Hadoop pomocí PowerShellu – Azure HDInsight
description: Naučte se vytvářet clustery Apache Hadoop, Apache HBase, Apache Storm nebo Apache Spark na Linuxu pro HDInsight pomocí Azure PowerShellu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 3c93eca493275612ac14a995140b2e91cc40fe98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644660"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Vytváření clusterů založených na Linuxu ve HDInsightu pomocí Azure PowerShellu

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell je výkonné skriptovací prostředí, které můžete použít k řízení a automatizaci nasazení a správy vašich úloh v Microsoft Azure. Tento dokument obsahuje informace o tom, jak vytvořit cluster HDInsight založený na Linuxu pomocí Azure PowerShellu. Obsahuje také ukázkový skript.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/install-Az-ps) Az modul.

## <a name="create-cluster"></a>Vytvoření clusteru

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Pokud chcete vytvořit cluster HDInsight pomocí Azure PowerShellu, musíte provést následující postupy:

* Vytvoření skupiny prostředků Azure
* Vytvoření účtu služby Azure Storage
* Vytvoření kontejneru objektů Blob Azure
* Vytvoření clusteru HDInsight

> [!NOTE]
> Použití PowerShellu k vytvoření clusteru HDInsight pomocí Azure Data Lake Storage Gen2 není momentálně podporované.

Následující skript ukazuje, jak vytvořit nový cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Hodnoty zadané pro přihlášení clusteru se používají k vytvoření uživatelského účtu Hadoop pro cluster. Tento účet slouží k připojení ke službám hostovaným v clusteru, jako jsou webová uživatelské prostředí nebo rest API.

Hodnoty, které zadáte pro uživatele SSH, se používají k vytvoření uživatele SSH pro cluster. Tento účet slouží ke spuštění vzdálené relace SSH v clusteru a spuštění úloh. Další informace najdete v dokumentu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]  
> Pokud plánujete použít více než 32 pracovních uzlů (při vytváření clusteru nebo škálováním clusteru po vytvoření), musíte také zadat velikost hlavního uzlu s alespoň 8 jádry a 14 GB paměti RAM.
>
> Další informace o velikostech uzlů a souvisejících nákladech najdete v [cenách pro HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Vytvoření clusteru může trvat až 20 minut.

## <a name="create-cluster-configuration-object"></a>Vytvořit cluster: Konfigurační objekt

Můžete také vytvořit objekt konfigurace [`New-AzHDInsightClusterConfig`](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) HDInsight pomocí rutiny. Tento konfigurační objekt pak můžete upravit a povolit další možnosti konfigurace pro váš cluster. Nakonec použijte `-Config` parametr rutiny [`New-AzHDInsightCluster`](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) k použití konfigurace.

Následující skript vytvoří konfigurační objekt pro konfiguraci typu clusteru R Server na serveru HDInsight. Konfigurace umožňuje hraniční uzel, RStudio a další účet úložiště.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> Použití účtu úložiště v jiném umístění než cluster HDInsight není podporováno. Při použití tohoto příkladu vytvořte další účet úložiště ve stejném umístění jako server.

## <a name="customize-clusters"></a>Přizpůsobení clusterů

* Viz [Přizpůsobení clusterů HDInsight pomocí Bootstrapu](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Viz [Přizpůsobení clusterů HDInsight pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Další kroky

Teď, když jste úspěšně vytvořili cluster HDInsight, použijte následující prostředky, abyste se dozvěděli, jak s clusterem pracovat.

### <a name="apache-hadoop-clusters"></a>Clustery Apache Hadoop

* [Použití Apache Hive s HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití mapreduce s HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clustery Apache HBase

* [Začínáme s Apache HBase na HDInsightu](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Vývoj java aplikací pro Apache HBase na HDInsightu](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Bouřkové klastry

* [Vývoj java topologie pro Storm na HDInsight](storm/apache-storm-develop-java-topology.md)
* [Použití komponent Pythonu v Stormu na HDInsightu](storm/apache-storm-develop-python-topology.md)
* [Nasazení a sledování topologie pomocí Storm u HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Clustery Apache Spark

* [Vytvoření samostatné aplikace pomocí Scala](spark/apache-spark-create-standalone-application.md)
* [Spouštění úloh na dálku v clusteru Apache Spark pomocí Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark s BI: Provádění interaktivní analýzy dat pomocí Spark v HDInsightu pomocí nástrojů BI](spark/apache-spark-use-bi-tools.md)
* [Apache Spark s machine learningem: Využijte Spark v HDInsightu k předvídání výsledků kontroly potravin](spark/apache-spark-machine-learning-mllib-ipython.md)