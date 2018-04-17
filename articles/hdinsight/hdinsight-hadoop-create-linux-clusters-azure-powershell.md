---
title: Vytváření clusterů systému Hadoop pomocí prostředí PowerShell – Azure HDInsight | Microsoft Docs
description: Naučte se vytvořit clustery Hadoop, HBase, Storm a Spark pro HDInsight pomocí prostředí Azure PowerShell v systému Linux.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4208deca-d64a-45e1-8948-2673d5d7678c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 57ae7c3cab6c0084bb43585f65d79ae6cc265356
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Vytvořit clustery se systémem Linux v HDInsight pomocí Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Prostředí Azure PowerShell je výkonná skriptovací prostředí, které můžete řídit a automatizovat nasazení a správu vašich úloh ve službě Microsoft Azure. Tento dokument obsahuje informace o tom, jak vytvořit cluster HDInsight se systémem Linux pomocí prostředí Azure PowerShell. Zahrnuje také ukázkový skript.

> [!NOTE]
> Prostředí Azure PowerShell je dostupná pouze na klienty se systémem Windows. Pokud používáte klienta Linux, Unix nebo Mac OS X, přečtěte si téma [vytvořit cluster HDInsight se systémem Linux pomocí příkazového řádku Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) informace o používání rozhraní příkazového řádku Azure k vytvoření clusteru.

## <a name="prerequisites"></a>Požadavky
Musí mít před zahájením tohoto postupu následující:

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-azurerm-ps)

    > [!IMPORTANT]
    > Podpora prostředí Azure PowerShell pro správu prostředků služby HDInsight pomocí Azure Service Manageru je **zastaralá** a k 1. lednu 2017 jsme ji odebrali. Kroky v tomto dokumentu používají nové rutiny služby HDInsight, které pracují s Azure Resource Managerem.
    >
    > Postupujte podle kroků v [nainstalovat Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) nainstalovat nejnovější verzi prostředí Azure PowerShell. Pokud máte skripty, které je potřeba upravit tak, aby používaly nové rutiny, které pracují s nástrojem Azure Resource Manager, najdete další informace v tématu [Migrace na vývojové nástroje založené na Azure Resource Manageru pro clustery služby HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="create-cluster"></a>Vytvoření clusteru

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

K vytvoření clusteru HDInsight pomocí prostředí Azure PowerShell, musíte provést následující postupy:

* Vytvoření skupiny prostředků Azure.
* Vytvoření účtu služby Azure Storage
* Vytvořit kontejner objektů Blob v Azure
* Vytvoření clusteru HDInsight

Následující skript ukazuje postup vytvoření nového clusteru:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Hodnoty, které zadáte pro přihlášení clusteru se používají k vytvoření uživatelského účtu Hadoop pro cluster. Tento účet slouží k připojení ke službám hostovaným v clusteru, jako jsou webová uživatelská rozhraní nebo rozhraní REST API.

Hodnoty, které zadáte pro uživatele SSH se používají k vytvoření uživatele SSH pro cluster. Tento účet použijte ke spuštění vzdálené relace SSH v clusteru a spouštění úloh. Další informace najdete v dokumentu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Pokud máte v plánu používat více než 32 uzlů pracovního procesu (buď při vytváření clusteru, nebo po vytvoření škálování clusteru), musíte také zadat velikost hlavního uzlu s alespoň s 8 jádry a 14 GB paměti RAM.
>
> Další informace o velikosti uzlu a souvisejících nákladů, najdete v části [HDInsight ceny](https://azure.microsoft.com/pricing/details/hdinsight/).

To může trvat až 20 minut pro vytvoření clusteru.

## <a name="create-cluster-configuration-object"></a>Vytvoření clusteru: objekt konfigurace

Můžete taky vytvořit objekt konfigurace HDInsight pomocí `New-AzureRmHDInsightClusterConfig` rutiny. Poté můžete upravit tento objekt konfigurace povolit další možnosti konfigurace pro váš cluster. Nakonec použijte `-Config` parametr `New-AzureRmHDInsightCluster` rutiny používají konfiguraci.

Tento skript vytvoří objekt konfigurace ke konfiguraci serveru R na typ clusteru HDInsight. Konfigurace umožňuje hraniční uzel, Rstudia a účet úložiště.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-98)]

> [!WARNING]
> Použití účtu úložiště v jiném umístění než HDInsight cluster není podporováno. Při použití v tomto příkladu, vytvořte účet úložiště ve stejném umístění jako server.

## <a name="customize-clusters"></a>Přizpůsobení clusterů

* V tématu [clusterů HDInsight přizpůsobit pomocí Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* V tématu [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Další postup

Teď, když jste úspěšně vytvořili clusteru služby HDInsight, použijte v následujících zdrojích informací se dozvíte, jak pracovat s vašeho clusteru.

### <a name="hadoop-clusters"></a>Clustery Hadoop

* [Použití Hivu se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](hadoop/hdinsight-use-pig.md)
* [Používání nástroje MapReduce s HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clustery HBase

* [Začínáme s HBase ve službě HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Vývoj aplikací v jazyce Java pro HBase v HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clustery Storm

* [Vývoj topologie Java pro Storm v HDInsight](storm/apache-storm-develop-java-topology.md)
* [Použití komponent, Python v Storm v HDInsight](storm/apache-storm-develop-python-topology.md)
* [Nasazení a monitorování topologie se Storm v HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Clustery Spark

* [Vytvoření samostatné aplikace pomocí Scala](spark/apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](spark/apache-spark-livy-rest-interface.md)
* [Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](spark/apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](spark/apache-spark-machine-learning-mllib-ipython.md)

