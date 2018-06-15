---
title: Použití Windows PC s Hadoop v HDInsight - Azure | Microsoft Docs
description: Pracovat z Windows PC v Hadoop v HDInsight. Spravovat a dotaz clusterů pomocí nástroje PowerShell, sady Visual Studio a Linux. Vývoj řešení pro velká data pomocí rozhraní .NET.
services: hdinsight
keywords: hadoop v systému windows, hadoop pro windows
author: cjgronlund
manager: jhubbard
ms.author: cgronlun
ms.date: 05/17/2017
ms.topic: conceptual
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 2067e36c931e62e25d0c8dca69a6a81b2afe3f87
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31404975"
---
# <a name="work-in-the-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Práce v ekosystému Hadoop v HDInsight z počítačů s Windows

Další informace o vývoj a možnosti správy na počítač s Windows pro práci v ekosystému Hadoop v HDInsight. 

HDInsight je založena na Apache Hadoop a komponent systému Hadoop, technologie open source vyvinuté v systému Linux. HDInsight verze 3.4 a vyšší používá distribuční Ubuntu Linux jako základní operační systém pro cluster. Však můžete pracovat s HDInsight z klienta Windows nebo vývojového prostředí systému Windows.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Pomocí prostředí PowerShell pro nasazení a Správa úloh
Prostředí Azure PowerShell je skriptovací prostředí, které můžete řídit a automatizovat nasazení a úlohy správy v HDInsight ze systému Windows.

Příklady úloh, které můžete provést pomocí prostředí PowerShell:

* [Vytvoření clusterů pomocí prostředí PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Spouštění dotazů Hive pomocí prostředí PowerShell](hadoop/apache-hadoop-use-hive-powershell.md)
* [Správa clusterů pomocí prostředí PowerShell](hdinsight-administer-use-powershell.md)

Postupujte podle kroků [instalace a konfigurace prostředí Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) získat nejnovější verzi. Pokud máte skripty, které je potřeba upravit tak, aby používaly nové rutiny pro Azure Resource Manager, najdete v části [migrace do nástroje pro vývoj na základě Azure Resource Manageru pro clustery služby HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="utilities-you-can-run-in-a-browser"></a>Nástroje můžete spustit v prohlížeči
Následující nástroje mají webové uživatelské rozhraní, které běží v prohlížeči:
* **[Cloudové prostředí Azure (preview)](https://docs.microsoft.com/azure/cloud-shell/quickstart)**  je prostředí interaktivní, příkazového řádku, který běží v prohlížeči a z portálu Azure.
* **[Webové uživatelské rozhraní Ambari](hdinsight-hadoop-manage-ambari.md)**  správy a monitorování nástroje, které jsou k dispozici na portálu Azure, který slouží ke správě různých druhů úlohy, jako například:
    * [Pomocí Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Zobrazení Ambari Hive](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Tez zobrazení Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Nástroje data Lake (Hadoop) pro Visual Studio
Pomocí nástrojů Data Lake pro Visual Studio k nasazení a správě topologie Storm. Nástroje data Lake nainstaluje taky SCP.NET SDK, která umožňuje vyvíjet topologie C# Storm pomocí sady Visual Studio.

Před přechodem na následující příklady [nainstalujte a nástrojů Data Lake pro Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md). 

Příklady úloh, které můžete provést pomocí sady Visual Studio a nástrojů Data Lake pro Visual Studio:
* [Nasazení a správa topologií Storm ze sady Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Vývoj C# topologií pro Storm pomocí sady Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). Službu bits zahrnují příklad šablony pro topologie Storm, se můžete připojit do databáze, například Azure Cosmos databáze a databáze SQL.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio a .NET SDK 

Visual Studio pomocí .NET SDK služby slouží k clustery spravovat a vývoji velkých objemů dat aplikací. Můžete použít jiné integrovaného vývojového prostředí pro následující úlohy, ale příklady jsou uvedeny v sadě Visual Studio.

Příklady úloh, které můžete provést pomocí .NET SDK v sadě Visual Studio:
* [Vytváření clusterů a práci v HDInsight z aplikace rozhraní .NET Framework](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [Spouštění dotazů Hive pomocí sady .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)
* [Uživatelem definované funkce jazyka C# pomocí Hive a Pig streamování na Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

> TIP Pokud používáte rozhraní .NET řešení s clustery HDInsight se systémem Windows, je vhodná doba pro plánování migrace do clusterech se systémem Linux. Další informace najdete v tématu [migrovat .NET řešení pro HDInsight se systémem Windows do HDInsight se systémem Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA a Eclipse IDE pro clustery Spark
Obě [Intellij IDEA](https://www.jetbrains.com/idea/download) a [Eclipse IDE](https://www.eclipse.org/downloads/) lze provádět:
* Vývoj a odesílání aplikací Scala Spark na clusteru HDInsight Spark.
* Přístup k prostředkům clusteru Spark.
* Vytvořte a spusťte aplikací Scala Spark místně.

Tyto články Zobrazit jak: 
* Intellij IDEA: [Spark vytvořit aplikace s použitím nástrojů Azure pro modul plug-in Intellij a Scala SDK.](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE nebo Scala IDE pro Eclipse: [Spark vytvořit aplikace a sady nástrojů Azure pro Eclipse](spark/apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>Poznámkové bloky na Spark pro datových vědců 
Clustery Apache Spark v HDInsight zahrnuje poznámkových bloků Zeppelin a jádra, které lze použít s poznámkovými bloky Jupyter. 

* [Další informace o použití jádra na clustery Spark s poznámkovými bloky Jupyter pro testování aplikací Spark](spark/apache-spark-zeppelin-notebook.md)
* [Další informace o použití poznámkových bloků Zeppelin na clustery Spark ke spuštění úloh Spark](spark/apache-spark-jupyter-notebook-kernels.md) 


## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Spuštění nástrojů systémem Linux a technologií v systému Windows

Pokud narazíte na situaci, kdy je nutné použít nástroj nebo technologie, která je k dispozici v systému Linux, zvažte následující možnosti:

* **Bash (beta) ve Windows 10** poskytuje subsystému Linux v systému Windows. Bash umožňuje přímo spouštět nástroje Linux bez nutnosti udržovat vyhrazené instalace systému Linux. [Nainstalujte a spusťte na používání beta verze Bash ve Windows 10](https://msdn.microsoft.com/commandline/wsl/install_guide)
* **Docker pro systém Windows** poskytuje přístup k celou řadu nástrojů se systémem Linux a můžete spustit přímo z Windows. Například můžete Docker spuštění klienta Beeline pro Hive přímo ze systému Windows. Můžete také použít ke spuštění místní poznámkového bloku Jupyter Docker a vzdáleně se připojovat k Spark v HDInsight. [Začínáme s Docker pro Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](http://mobaxterm.mobatek.net/)**  umožňuje graficky procházet clusteru systému souborů přes připojení SSH.

## <a name="next-steps"></a>Další postup
Pokud s k práci v clusterech se systémem Linux, najdete v článcích postupujte podle kroků:
* [Nastavit Hadoop, Kafka, Spark nebo jiné clustery](hdinsight-hadoop-provision-linux-clusters.md)
* [Tipy pro clustery služby HDInsight v Linuxu](hdinsight-hadoop-linux-information.md)
