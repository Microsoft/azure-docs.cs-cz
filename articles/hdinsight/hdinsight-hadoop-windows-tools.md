---
title: Použití Windows PC se systémem Hadoop v HDInsight – Azure
description: Práce od Windows PC v Hadoop v HDInsight. Správa a dotaz clusterů pomocí nástroje PowerShell, sady Visual Studio a Linux. Vývoj řešení pro velké objemy dat s využitím .NET.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 05/17/2017
ms.openlocfilehash: a522d436328a3aeb1ed365baa2ec1e3b9d3387ed
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53385019"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Práce v ekosystému Apache Hadoop v HDInsight z počítače s Windows

Další informace o vývoji a možnosti správy na počítač s Windows pro práci v ekosystému Apache Hadoop v HDInsight. 

HDInsight je založené na Apache Hadoopu a součásti platformy Hadoop, technologie open source vyvinutý v Linuxu. HDInsight verze 3.4 a vyšší distribuce systému Ubuntu Linux používá jako základní operační systém pro cluster. Ale můžete pracovat s HDInsight z klientů Windows nebo Windows vývojové prostředí.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Pomocí prostředí PowerShell pro nasazení a Správa úloh
Prostředí Azure PowerShell je skriptovací prostředí, které můžete použít k řízení a automatizaci nasazení a úlohy správy v HDInsight z Windows.

Příklady úloh, které můžete provést v prostředí PowerShell:

* [Vytvářet clustery pomocí Powershellu](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Spouštějte dotazy Apache Hive pomocí Powershellu](hadoop/apache-hadoop-use-hive-powershell.md)
* [Správa clusterů pomocí Powershellu](hdinsight-administer-use-powershell.md)

Postupujte podle kroků a [instalace a konfigurace Azure Powershellu](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) získat nejnovější verzi. Pokud máte skripty, které je potřeba upravit tak, aby používaly nové rutiny pro Azure Resource Manager, najdete v článku [migrace do nástroje pro vývoj založených na Azure Resource Manageru pro clustery HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="utilities-you-can-run-in-a-browser"></a>Nástroje můžete spustit v prohlížeči
Následující nástroje mít webové uživatelské rozhraní, které běží v prohlížeči:
* **[Azure Cloud Shell (preview)](https://docs.microsoft.com/azure/cloud-shell/quickstart)**  je interaktivního příkazového řádku příkazového, na kterém běží v prohlížeči a v rámci webu Azure portal.
* **[Webové uživatelské rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md)**  správy a monitorování nástroj je k dispozici na webu Azure Portal, který lze použít ke správě různých typů úloh, jako například:
    * [Pomocí rozhraní REST API Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Apache Hive, zobrazení Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Apache Tez zobrazení Apache Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Nástroje data Lake (Hadoop) pro Visual Studio
K nasazení a správa topologií Storm pomocí nástrojů Data Lake pro Visual Studio. Nástroje data Lake se nainstaluje taky SCP.NET SDK, která umožňuje vývoj topologií C# Storm pomocí sady Visual Studio.

Než budete pokračovat v následujících příkladech [nainstalujte a Data Lake Tools pro Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md). 

Příklady úloh, které vám pomůžou s Visual Studio a nástrojů Data Lake pro Visual Studio:
* [Nasazení a správa topologií Storm ze sady Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Vývoj topologií C# pro Storm pomocí sady Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). Službu bits obsahuje příklad šablony pro topologií Storm, které můžete připojit k databázím, jako je Azure Cosmos DB a SQL Database.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio a .NET SDK 

Visual Studio pomocí sady .NET SDK můžete použít ke správě clusterů a vyvíjet aplikace pro velké objemy dat. Jiná Integrovaná vývojová prostředí můžete použít pro následující úlohy, ale příklady jsou uvedeny v sadě Visual Studio.

Příklady úloh, které vám pomůžou s využitím .NET SDK v sadě Visual Studio:
* [Vytváření clusterů a práci v HDInsight z aplikace rozhraní .NET Framework](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [Spouštějte dotazy Apache Hive pomocí sady .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)
* [Použití C# uživatelsky definovaných funkcí s Apache Hivu a Apache Pig datových proudů na Apache Hadoopu](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

> TIP   
> Pokud používáte řešení .NET s clustery HDInsight se systémem Windows, je vhodná doba k plánování migrace na linuxových clusterech. Další informace najdete v tématu [řešení migrace .NET pro Windows na základě HDInsight založených na Linuxu HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA a Eclipse IDE pro clustery Spark
Obě [Intellij IDEA](https://www.jetbrains.com/idea/download) a [integrovaného vývojového prostředí Eclipse](https://www.eclipse.org/downloads/) je možné použít k:
* Vývoj a odeslání aplikace Scala Spark na cluster HDInsight Spark.
* Přístup k prostředkům clusteru Spark.
* Vyvíjejte a spouštějte aplikace Scala Spark místně.

Tyto články popisují jak: 
* Intellij IDEA: [Vytváření aplikací Apache Spark pomocí sady Azure Toolkit pro Intellij modulu plug-in a Scala SDK.](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE nebo Scala integrované vývojové prostředí pro Eclipse: [Vytváření aplikací Apache Spark a sady Azure Toolkit pro Eclipse](spark/apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>Poznámkové bloky ve Sparku pro odborníky přes data 
Clustery Apache Spark v HDInsight zahrnují poznámkových bloků Apache Zeppelin a jader, které lze použít s poznámkovými bloky Jupyter. 

* [Další informace o použití jádra na clusterech Apache Spark s poznámkovými bloky Jupyter pro testování aplikací Spark](spark/apache-spark-zeppelin-notebook.md)
* [Další informace o použití poznámkových bloků Apache Zeppelin v clusterech Apache Spark pro spouštění úloh Spark](spark/apache-spark-jupyter-notebook-kernels.md) 


## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Spuštění nástroje založené na Linuxu a technologie ve Windows

Pokud narazíte na situace, kdy je nutné použít nástroj nebo technologii, která je jenom v Linuxu k dispozici, zvažte následující možnosti:

* **Prostředí bash (beta verze) ve Windows 10** poskytuje Linux podsystému ve Windows. Bash můžete přímo spouštět nástroje Linux bez nutnosti udržovat vyhrazené instalace systému Linux. [Nainstalujte a spusťte Bash beta ve Windows 10](https://msdn.microsoft.com/commandline/wsl/install_guide)
* **Docker pro Windows** poskytuje přístup k mnoha nástrojů založených na Linuxu a můžete spustit přímo z Windows. Například můžete použít Docker a spusťte Beeline klienta pro Hive přímo z Windows. Můžete také použít Docker ke spuštění místní aplikace Jupyter notebook a vzdálené připojení ke Sparku v HDInsight. [Začínáme s Docker pro Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](http://mobaxterm.mobatek.net/)**  umožňuje graficky procházení systému souborů clusteru přes připojení SSH.

## <a name="next-steps"></a>Další postup
Pokud teprve začínáte pracovat se v clusterech se systémem Linux, přečtěte si články postupujte podle:
* [Nastavte si Apache Hadoop, Apache Kafka, Apache Spark nebo jiných clusterů](hdinsight-hadoop-provision-linux-clusters.md)
* [Tipy pro clustery HDInsight v Linuxu](hdinsight-hadoop-linux-information.md)
