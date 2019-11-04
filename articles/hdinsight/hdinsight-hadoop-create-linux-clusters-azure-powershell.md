---
title: Vytváření clusterů Apache Hadoop pomocí prostředí PowerShell – Azure HDInsight
description: Naučte se vytvářet Apache Hadoop, Apache HBA, Apache Storm nebo Apache Spark clustery na platformě Linux pro HDInsight pomocí Azure PowerShell.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: hrasheed
ms.openlocfilehash: a6847e75a0a6dcf944b033054ac466841294d28b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494791"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Vytváření clusterů se systémem Linux v HDInsight pomocí Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell je výkonné skriptovací prostředí, které můžete použít k řízení a automatizaci nasazení a správy úloh v Microsoft Azure. Tento dokument poskytuje informace o tom, jak vytvořit cluster HDInsight se systémem Linux pomocí Azure PowerShell. Obsahuje také ukázkový skript.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před zahájením tohoto postupu musíte mít následující:

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-Az-ps)

    > [!IMPORTANT]  
    > Podpora prostředí Azure PowerShell pro správu prostředků služby HDInsight pomocí Azure Service Manageru je **zastaralá** a k 1. lednu 2017 jsme ji odebrali. Kroky v tomto dokumentu používají nové rutiny služby HDInsight, které pracují s Azure Resource Managerem.
    >
    > Chcete-li nainstalovat nejnovější verzi Azure PowerShell, postupujte podle kroků v části [install Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) . Pokud máte skripty, které je potřeba upravit tak, aby používaly nové rutiny, které pracují s nástrojem Azure Resource Manager, najdete další informace v tématu [Migrace na vývojové nástroje založené na Azure Resource Manageru pro clustery služby HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="create-cluster"></a>Vytvoření clusteru

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Chcete-li vytvořit cluster HDInsight pomocí Azure PowerShell, je nutné provést následující postupy:

* Vytvoření skupiny prostředků Azure
* Vytvoření účtu služby Azure Storage
* Vytvoření kontejneru objektů BLOB v Azure
* Vytvoření clusteru HDInsight

Následující skript ukazuje, jak vytvořit nový cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Hodnoty, které zadáte pro přihlášení clusteru, se použijí k vytvoření uživatelského účtu Hadoop pro cluster. Tento účet použijte pro připojení ke službám hostovaným v clusteru, jako je například webová uživatelská rozhraní nebo rozhraní REST API.

Hodnoty, které zadáte pro uživatele SSH, se použijí k vytvoření uživatele SSH pro daný cluster. Pomocí tohoto účtu můžete spustit vzdálenou relaci SSH v clusteru a spustit úlohy. Další informace najdete v dokumentu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]  
> Pokud plánujete použít více než 32 pracovních uzlů (buď při vytváření clusteru, nebo po vytvoření škálování clusteru), je nutné zadat také velikost hlavního uzlu s alespoň 8 jádry a 14 GB paměti RAM.
>
> Další informace o velikostech uzlů a souvisejících nákladech najdete v [cenách pro HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Vytvoření clusteru může trvat až 20 minut.

## <a name="create-cluster-configuration-object"></a>Vytvořit cluster: objekt konfigurace

Objekt konfigurace služby HDInsight můžete vytvořit také pomocí rutiny `New-AzHDInsightClusterConfig`. Pak můžete tento objekt konfigurace upravit a povolit pro svůj cluster další možnosti konfigurace. Nakonec použijte parametr `-Config` rutiny `New-AzHDInsightCluster` pro použití konfigurace.

Následující skript vytvoří objekt konfigurace pro konfiguraci R Server v typu clusteru HDInsight. Tato konfigurace umožňuje hraniční uzel, RStudio a další účet úložiště.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> Použití účtu úložiště v jiném umístění, než je cluster HDInsight, se nepodporuje. Při použití tohoto příkladu vytvořte další účet úložiště ve stejném umístění jako server aplikace.

## <a name="customize-clusters"></a>Přizpůsobení clusterů

* Viz [Přizpůsobení clusterů HDInsight pomocí Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Viz [Přizpůsobení clusterů HDInsight pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Další kroky

Teď, když jste úspěšně vytvořili cluster HDInsight, se dozvíte, jak pracovat s clusterem pomocí následujících zdrojů.

### <a name="apache-hadoop-clusters"></a>Clustery Apache Hadoop

* [Použití Apache Hive se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Apache prasete se službou HDInsight](hadoop/hdinsight-use-pig.md)
* [Použití MapReduce se službou HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clustery Apache HBA

* [Začínáme s Apache HBA v HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Vývoj aplikací Java pro Apache HBA v HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clustery pro více systémů

* [Vývoj topologií v jazyce Java pro práci v HDInsight](storm/apache-storm-develop-java-topology.md)
* [Používání komponent Pythonu v systému HDInsight v HDInsight](storm/apache-storm-develop-python-topology.md)
* [Nasazení a monitorování topologií se zaplavou v HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Clustery Apache Spark

* [Vytvoření samostatné aplikace pomocí Scala](spark/apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI.](spark/apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight předpovídat výsledky kontroly potravin](spark/apache-spark-machine-learning-mllib-ipython.md)

