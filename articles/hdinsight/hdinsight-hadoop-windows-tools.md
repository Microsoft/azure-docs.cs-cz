---
title: Použití počítače s Windows se systémem Hadoop ve službě HDInsight – Azure
description: Práce z počítače s Windows v systému Hadoop ve službě HDInsight. Spravujte a Dotazujte clustery pomocí nástrojů PowerShell, Visual Studio a Linux. Vývoj řešení pro velké objemy dat pomocí .NET
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/20/2019
ms.openlocfilehash: 6482b2ec07a0473ddbea2098d37c5d67c8b68704
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539696"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Práce v Apache Hadoop ekosystému ve službě HDInsight z počítače s Windows

Přečtěte si o možnostech vývoje a správy na počítači s Windows pro práci v Apache Hadoop ekosystému ve službě HDInsight.

HDInsight je založený na komponentách Apache Hadoop a Hadoop, open source technologiích vyvinutých na platformě Linux. HDInsight verze 3,4 a vyšší používá distribuci Ubuntu Linux jako základní operační systém pro cluster. Můžete ale pracovat se službou HDInsight z klienta Windows nebo vývojového prostředí Windows.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Použití PowerShellu pro úlohy nasazení a správy

Azure PowerShell je skriptovací prostředí, které můžete použít k řízení a automatizaci úloh nasazení a správy ve službě HDInsight ze systému Windows.

Příklady úloh, které můžete provádět s PowerShellem:

* [Vytvářejte clustery pomocí PowerShellu](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Spouštějte Apache Hive dotazy pomocí PowerShellu](hadoop/apache-hadoop-use-hive-powershell.md).
* [Správa clusterů pomocí PowerShellu](hdinsight-administer-use-powershell.md)

Pokud chcete získat nejnovější verzi, postupujte podle kroků pro [instalaci a konfiguraci Azure PowerShellu](/powershell/azure/install-az-ps) .

## <a name="utilities-you-can-run-in-a-browser"></a>Nástroje, které můžete spustit v prohlížeči

Následující nástroje mají webové uživatelské rozhraní, které běží v prohlížeči:
* **[Azure Cloud Shell](../cloud-shell/overview.md)** je interaktivní prostředí příkazového řádku, které běží v prohlížeči a v rámci Azure Portal.

* **[Webové uživatelské rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md)** je nástroj pro správu a monitorování, který je k dispozici ve Azure Portal, který je možné použít ke správě různých druhů úloh, jako je například:
    * [Použití Apache Ambari s REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Zobrazení Apache Hive v Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Zobrazení Apache Tez v Apache Ambari](./index.yml)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Data Lake (Hadoop) Tools pro Visual Studio

Pomocí Data Lakech nástrojů pro Visual Studio nasaďte a spravujte topologie přeplavování. Nástroje pro Data Lake také instalují sadu SDK SCP.NET, která umožňuje vyvíjet topologie C# s využitím sady Visual Studio.

Než přejdete na následující příklady, [nainstalujte a vyzkoušejte si data Lake nástroje pro Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Příklady úloh, které můžete provádět pomocí sady Visual Studio a Data Lakech nástrojů pro Visual Studio:
* [Nasazení a Správa topologií přeplavování ze sady Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Vývoj topologií v jazyce C# pro práci s využitím sady Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). Služba BITS zahrnuje ukázkové šablony pro topologie s více poddatabázemi, které se můžete připojit k databázím, například Azure Cosmos DB a SQL Database.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio a sada .NET SDK

Sadu Visual Studio se sadou .NET SDK můžete použít ke správě clusterů a vývoji aplikací pro velké objemy dat. Můžete použít další prostředí pro následující úkoly, ale příklady jsou uvedeny v aplikaci Visual Studio.

Příklady úloh, které můžete provádět s .NET SDK v sadě Visual Studio:
* [Azure HDInsight SDK pro .NET](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true)
* [Spouštějte Apache Hive dotazy pomocí sady .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).
* [Pomocí uživatelem definovaných funkcí jazyka C# se Apache Hive a Apache vepřovým proudem v Apache Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>IntelliJ nápad a rozzatmění IDE pro clustery Spark

[INTELLIJ nápad](https://www.jetbrains.com/idea/download) i [rozhraní IDE pro zatmění](https://www.eclipse.org/downloads/) lze použít k:
* Vývoj a odesílání aplikace Scala Spark v clusteru HDInsight Spark.
* Přístup k prostředkům clusteru Spark.
* Vývoj a spouštění aplikace Scala Spark lokálně

Tyto články ukazují, jak:
* IntelliJ nápad: [vytváření Apache Sparkch aplikací pomocí sady Azure Toolkit pro IntelliJ a sady Scala SDK.](spark/apache-spark-intellij-tool-plugin.md)
* Integrované vývojové prostředí (IDE) nebo Scala IDE pro zatmění: [vytváření aplikací Apache Spark a Azure Toolkit for Eclipse](spark/apache-spark-eclipse-tool-plugin.md)

## <a name="notebooks-on-spark-for-data-scientists"></a>Poznámkové bloky ve Sparku pro odborníky přes data

Clustery Apache Spark v HDInsight zahrnují notebooky Apache Zeppelin a jádra, které je možné použít s poznámkovým blokem Jupyter.

* [Naučte se používat jádra v Apache Spark clusterech Jupyter poznámkových blocích k testování aplikací Spark.](spark/apache-spark-zeppelin-notebook.md)
* [Naučte se používat notebooky Apache Zeppelin v clusterech Apache Spark ke spouštění úloh Sparku.](spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Spouštění nástrojů a technologií založených na systému Linux ve Windows

Pokud jste pocházeli v situaci, kdy je nutné použít nástroj nebo technologii, která je k dispozici pouze pro Linux, vezměte v úvahu následující možnosti:

* **Bash v Ubuntu ve Windows 10** poskytuje podsystém Linux ve Windows. Bash umožňuje přímo spouštět nástroje pro Linux bez nutnosti udržovat vyhrazenou instalaci systému Linux. Pokyny k instalaci najdete v tématu [Instalační příručka k systému Windows pro Linux pro systém Windows 10](/windows/wsl/install-win10) .  Budou fungovat i další [prostředí UNIX](https://www.gnu.org/software/bash/) .
* **Docker for Windows** poskytuje přístup k mnoha nástrojům pro Linux a dá se spustit přímo z Windows. Můžete například použít Docker ke spuštění klienta Beeline pro podregistr přímo z Windows. Docker můžete použít také ke spuštění místního poznámkového bloku Jupyter a ke vzdálenému připojení ke Sparku ve službě HDInsight. [Začínáme s Docker for Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](https://mobaxterm.mobatek.net/)** umožňuje grafiku procházet systémem souborů clusteru přes připojení SSH.

## <a name="cross-platform-tools"></a>Nástroje pro různé platformy

Rozhraní příkazového řádku Azure (CLI) je nové víceplatformové prostředí příkazového řádku Microsoftu pro správu prostředků Azure.  Další informace najdete v tématu [rozhraní příkazového řádku (CLI) pro Azure Command-Line](/cli/azure/).

## <a name="next-steps"></a>Další kroky

Pokud už nepracujete na clusterech se systémem Linux, přečtěte si článek postup:
* [Nastavení Apache Hadoop, Apache Kafka, Apache Spark nebo dalších clusterů](hdinsight-hadoop-provision-linux-clusters.md)
* [Tipy pro clustery HDInsight v systému Linux](hdinsight-hadoop-linux-information.md)