---
title: Vytvoření clusterů Hadoop pomocí prostředí PowerShell – Azure HDInsight
description: Zjistěte, jak vytvářet clustery Hadoop, HBase, Storm nebo Spark na Linuxu pro HDInsight pomocí Azure Powershellu.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: jasonh
ms.openlocfilehash: 4d2649a98ea7dfe971a32d014aebc789705b4f9d
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592104"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Vytvoření clusterů se systémem Linux v HDInsight pomocí Azure Powershellu

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Prostředí Azure PowerShell je výkonný skriptovací prostředí, které můžete použít k řízení a automatizaci nasazení a správu vašich úloh v Microsoft Azure. Tento dokument obsahuje informace o tom, jak vytvořit cluster HDInsight se systémem Linux pomocí Azure Powershellu. Také obsahuje ukázkový skript.

> [!NOTE]
> Prostředí Azure PowerShell je dostupná pouze na klienty Windows. Pokud používáte klienta systému Linux, Unix nebo Mac OS X, přečtěte si téma [vytvořit cluster HDInsight se systémem Linux pomocí příkazového řádku Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) informace o použití Azure CLI vytvořit cluster.

## <a name="prerequisites"></a>Požadavky
Musíte mít následující před zahájením tohoto postupu:

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-azurerm-ps)

    > [!IMPORTANT]
    > Podpora prostředí Azure PowerShell pro správu prostředků služby HDInsight pomocí Azure Service Manageru je **zastaralá** a k 1. lednu 2017 jsme ji odebrali. Kroky v tomto dokumentu používají nové rutiny služby HDInsight, které pracují s Azure Resource Managerem.
    >
    > Postupujte podle kroků v [instalace Azure Powershellu](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) nainstalovat nejnovější verzi Azure Powershellu. Pokud máte skripty, které je potřeba upravit tak, aby používaly nové rutiny, které pracují s nástrojem Azure Resource Manager, najdete další informace v tématu [Migrace na vývojové nástroje založené na Azure Resource Manageru pro clustery služby HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="create-cluster"></a>Vytvoření clusteru

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Vytvoření clusteru HDInsight pomocí Azure Powershellu, je nutné provést následující postup:

* Vytvořte skupinu prostředků Azure
* Vytvoření účtu služby Azure Storage
* Vytvoření kontejneru objektů Blob v Azure
* Vytvoření clusteru HDInsight

Tento skript ukazuje, jak vytvořit nový cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Hodnoty, které zadáte pro přihlášení ke clusteru se používají k vytvoření uživatelského účtu systému Hadoop pro cluster. Tento účet slouží k připojení ke službám hostovaným v clusteru, jako jsou webová uživatelská rozhraní nebo rozhraní REST API.

Hodnoty, které zadáte pro uživatele SSH slouží k vytvoření uživatele SSH pro cluster. Pomocí tohoto účtu spustit vzdálenou relaci SSH v clusteru a spouštění úloh. Další informace najdete v dokumentu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Pokud budete chtít použít více než 32 uzly pracovního procesu (buď při vytváření clusteru nebo škálování clusteru po vytvoření), musíte zadat také velikost hlavního uzlu s alespoň s 8 jádry a 14 GB paměti RAM.
>
> Další informace o velikostech uzlů a souvisejících nákladech najdete v [cenách pro HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Může trvat až 20 minut vytvořit cluster.

## <a name="create-cluster-configuration-object"></a>Vytvoření clusteru: objekt konfigurace

Můžete také vytvořit objekt konfigurace HDInsight pomocí `New-AzureRmHDInsightClusterConfig` rutiny. Poté můžete upravit tento objekt konfigurace k zapnout možnosti rozšířené konfigurace pro váš cluster. Nakonec použijte `-Config` parametr `New-AzureRmHDInsightCluster` rutiny použít konfiguraci.

Tento skript vytvoří objekt konfigurace ke konfiguraci R serveru v clusteru typu HDInsight. Konfigurace umožňuje hraniční uzel, RStudio a dalšího účtu úložiště.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-98)]

> [!WARNING]
> Použití účtu úložiště v jiném umístění než HDInsight cluster se nepodporuje. Při použití v tomto příkladu, vytvořte účet úložiště ve stejném umístění jako server.

## <a name="customize-clusters"></a>Přizpůsobení clusterů

* Zobrazit [HDInsight přizpůsobení clusterů pomocí Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Zobrazit [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Další postup

Teď, když úspěšně vytvoříte HDInsight cluster, použijte následující prostředky se naučíte pracovat s vaším clusterem.

### <a name="hadoop-clusters"></a>Clustery Hadoop

* [Použití Hivu se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](hadoop/hdinsight-use-pig.md)
* [Použití MapReduce se službou HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clustery HBase

* [Začínáme s HBase ve službě HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Vývoj aplikací v Javě pro HBase v HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clustery Storm

* [Vývoj topologie Java pro Storm v HDInsight](storm/apache-storm-develop-java-topology.md)
* [Použití komponent v Pythonu v Storm v HDInsight](storm/apache-storm-develop-python-topology.md)
* [Nasazení a monitorování topologií se Stormem v HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Clustery Spark

* [Vytvoření samostatné aplikace pomocí Scala](spark/apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](spark/apache-spark-livy-rest-interface.md)
* [Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](spark/apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](spark/apache-spark-machine-learning-mllib-ipython.md)

