---
title: Použití počítače s Windows s Hadoopem na HDInsight – Azure
description: Práce z počítače s Windows v Hadoopu na HDInsightu. Spravujte clustery a ucházujte se pomocí nástrojů PowerShellu, Visual Studia a Linuxu. Vyvíjejte řešení pro velké objemy dat pomocí rozhraní .NET.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/20/2019
ms.openlocfilehash: 3ec50acc693452fe73d929effcea98b12fc5ff8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933938"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Práce v ekosystému Apache Hadoop na HDInsight z počítače s Windows

Přečtěte si o možnostech vývoje a správy na počítači s Windows pro práci v ekosystému Apache Hadoop na HDInsightu.

HDInsight je založen na komponentách Apache Hadoop a Hadoop, open-source technologií vyvinutých na Linuxu. HDInsight verze 3.4 a vyšší používá distribuci Ubuntu Linux jako základní operační systém pro cluster. S hdinsightvšak můžete pracovat z klienta Windows nebo vývojového prostředí Systému Windows.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Použití PowerShellu pro úlohy nasazení a správy

Azure PowerShell je skriptovací prostředí, které můžete použít k řízení a automatizaci úloh nasazení a správy v HDInsight u Windows.

Příklady úkolů, které můžete s PowerShellem dělat:

* [Vytvořte clustery pomocí prostředí PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Spusťte dotazy Apache Hive pomocí prostředí PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).
* [Správa clusterů pomocí prostředí PowerShell](hdinsight-administer-use-powershell.md).

Podle pokynů [nainstalujte a nakonfigurujte Azure Powershell,](https://docs.microsoft.com/powershell/azure/install-az-ps) abyste získali nejnovější verzi.

## <a name="utilities-you-can-run-in-a-browser"></a>Nástroje, které lze spustit v prohlížeči

Následující nástroje mají webové uživatelské prostředí, které běží v prohlížeči:
* **[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)** je interaktivní prostředí příkazového řádku, které běží ve vašem prohlížeči a z portálu Azure.

* **[Webové uživatelské rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md)** je nástroj pro správu a monitorování dostupný na webu Azure Portal, který lze použít ke správě různých druhů úloh, jako jsou:
    * [Použití Apache Ambari s rozhraním REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Apache Hive View v Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Apache Tez Pohled v Apache Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Nástroje datového jezera (Hadoop) pro visual studio

Pomocí nástrojů Data Lake Tools pro Visual Studio nasazujte a spravujte topologie stormu. Nástroje data lake také nainstaluje SCP.NET SDK, který umožňuje vyvíjet c# Storm topologie s Visual Studio.

Než přejdete k následujícím příkladům, [nainstalujte a vyzkoušejte nástroje Data Lake Tools pro visual studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Příklady úkolů, které můžete dělat s Visual Studio a Nástroje datového jezera pro Visual Studio:
* [Nasazení a správa topologií stormu z Visual Studia](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Vývoj C# topologií pro Storm pomocí Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). Bity obsahují ukázkové šablony pro topologii Storm, které můžete připojit k databázím, jako je Azure Cosmos DB a SQL Database.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio a sada .NET SDK

Pomocí sady Visual Studio se sadou .NET SDK můžete spravovat clustery a vyvíjet aplikace pro velké objemy dat. Můžete použít jiné IDE pro následující úkoly, ale příklady jsou zobrazeny v sadě Visual Studio.

Příklady úkolů, které můžete dělat s sadou .NET SDK v sadě Visual Studio:
* [Azure HDInsight SDK pro rozhraní .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).
* [Spusťte dotazy Apache Hive pomocí sady .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).
* [Používejte uživatelem definované funkce Jazyka C# se streamováním Apache Hive a Apache Pig na Apache Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA a Eclipse IDE pro clustery Spark

[Jak Intellij IDEA](https://www.jetbrains.com/idea/download) a Eclipse [IDE](https://www.eclipse.org/downloads/) lze použít k:
* Vyvíjejte a odesílejte aplikaci Scala Spark v clusteru HDInsight Spark.
* Přístup k prostředkům clusteru Spark.
* Vyvíjejte a spouštějte aplikaci Scala Spark místně.

Tyto články ukazují, jak:
* Intellij IDEA: [Vytvořte aplikace Apache Spark pomocí modulu plug-in Azure Toolkit for Intellij a Scala SDK.](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE nebo Scala IDE pro Eclipse: [Vytvoření aplikací Apache Spark a sady nástrojů Azure pro Eclipse](spark/apache-spark-eclipse-tool-plugin.md)

## <a name="notebooks-on-spark-for-data-scientists"></a>Notebooky na Spark pro datové vědce

Clustery Apache Spark v HDInsightu zahrnují notebooky apache zeppelin a jádra, které lze použít s notebooky Jupyter.

* [Naučte se používat jádra v clusterech Apache Spark s notebooky Jupyter k testování aplikací Spark](spark/apache-spark-zeppelin-notebook.md)
* [Naučte se používat notebooky Apache Zeppelin v clusterech Apache Spark ke spouštění úloh Spark](spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Spouštění linuxových nástrojů a technologií ve Windows

Pokud narazíte na situaci, kdy musíte použít nástroj nebo technologii, která je k dispozici pouze na Linuxu, zvažte následující možnosti:

* **Bash na Ubuntu na Windows 10** poskytuje podsystém Linux na Windows. Bash umožňuje přímo spustit linuxové nástroje, aniž byste museli udržovat vyhrazenou instalaci Linuxu. Postup instalace najdete v [příručce k instalaci podsystému Windows pro Linux pro Windows 10.](https://docs.microsoft.com/windows/wsl/install-win10)  Ostatní [unixové granáty](https://www.gnu.org/software/bash/) budou fungovat také.
* **Docker pro Windows** poskytuje přístup k mnoha linuxovým nástrojům a lze je spouštět přímo ze systému Windows. Docker můžete například použít ke spuštění klienta Beeline pro Hive přímo z Windows. Docker můžete taky použít ke spuštění místního notebooku Jupyter a vzdáleně se připojit ke Sparku na HDInsightu. [Začínáme s Dockerem pro Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](https://mobaxterm.mobatek.net/)** umožňuje graficky procházet souborový systém clusteru přes připojení SSH.

## <a name="cross-platform-tools"></a>Nástroje pro různé platformy

Rozhraní příkazového řádku Azure (CLI) je nové víceplatformové prostředí příkazového řádku Microsoftu pro správu prostředků Azure.  Další informace naleznete v [tématu Azure Command-Line Interface (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="next-steps"></a>Další kroky

Pokud s prací v linuxových clusterech tečujete, přečtěte si následující články:
* [Nastavení Apache Hadoop, Apache Kafka, Apache Spark nebo jiných clusterů](hdinsight-hadoop-provision-linux-clusters.md)
* [Tipy pro clustery HDInsight na Linuxu](hdinsight-hadoop-linux-information.md)
