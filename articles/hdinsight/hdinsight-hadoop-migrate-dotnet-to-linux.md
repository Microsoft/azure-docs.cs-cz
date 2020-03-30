---
title: Použití rozhraní .NET s Hadoop mapreduce na Linuxu-založené HDInsight - Azure
description: Přečtěte si, jak používat aplikace .NET pro streamování MapReduce na Linuxu založeném na HDInsightu.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 4b402975a151d26e8f335c07930274c156ac52fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272367"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migrace řešení .NET pro HDInsight založený na Windows na HDInsight založený na Linuxu

Clustery HDInsight založené na Linuxu používají [mono (https://mono-project.com) ](https://mono-project.com) ke spuštění aplikací .NET. Mono umožňuje používat komponenty .NET, jako je MapReduce aplikací s Linux-založené HDInsight. V tomto dokumentu se dozvíte, jak migrovat řešení .NET vytvořená pro clustery HDInsight založených na Windows, aby fungovala s Mono na Linuxu na HDInsightu.

## <a name="mono-compatibility-with-net"></a>Mono kompatibilita s rozhraním .NET

Mono verze 4.2.1 je součástí HDInsight verze 3.6. Další informace o verzi Mono, která je součástí hdinsightu, najdete v [tématu verze komponent HDInsight](hdinsight-component-versioning.md).

Další informace o kompatibilitě mezi mono a .NET naleznete v dokumentu [Mono compatibility (https://www.mono-project.com/docs/about-mono/compatibility/) ](https://www.mono-project.com/docs/about-mono/compatibility/) Document.

> [!IMPORTANT]  
> Rozhraní SCP.NET je kompatibilní s Mono. Další informace o používání SCP.NET s Mono najdete [v tématu Použití Visual Studia k vývoji topologií Jazyka C# pro Apache Storm na HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Analýza automatizované přenositelnosti

[Analyzátor přenositelnosti rozhraní .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) lze použít ke generování sestavy nekompatibility mezi aplikací a Mono. Pomocí následujících kroků nakonfigurujte analyzátor pro kontrolu přenositelnosti aplikace Mono:

1. Nainstalujte [analyzátor přenositelnosti rozhraní .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Během instalace vyberte verzi sady Visual Studio, kterou chcete použít.

2. V sadě Visual Studio 2015 vyberte __analyzovat__ > __nastavení analyzátoru přenositelnosti__a ujistěte se, že je v části __Mono__ zaškrtnuto __políčko 4.5.__

    ![4.5 zkontrolováno v sekci Mono pro nastavení analyzátoru](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Chcete-li konfiguraci uložit, vyberte __možnost OK.__

3. Vyberte __Analyzovat__ > __přenositelnost sestavení__. Vyberte sestavení, které obsahuje vaše řešení, a pak vyberte __Otevřít__ a začněte analýzu.

4. Po dokončení analýzy vyberte __Analyzovat__ > __sestavy analýzy zobrazení__. V __oznámení Výsledky analýzy přenositelnosti__vyberte __otevřít sestavu__ a otevřete sestavu.

    ![Dialogové okno Výsledky analyzátoru přenositelnosti](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> Analyzátor nemůže zachytit každý problém s řešením. Například cesta k `c:\temp\file.txt` souboru je považován za OK, pokud Mono běží v systému Windows. Stejná cesta není platná na platformě Linux.

## <a name="manual-portability-analysis"></a>Analýza manuální přenositelnosti

Proveďte ruční audit kódu pomocí informací v dokumentu [Přenositelnosthttps://www.mono-project.com/docs/getting-started/application-portability/) aplikace (](https://www.mono-project.com/docs/getting-started/application-portability/) dokument.

## <a name="modify-and-build"></a>Úpravy a sestavení

Pomocí sady Visual Studio můžete vytvářet řešení .NET pro HDInsight. Je však nutné zajistit, že projekt je nakonfigurován pro použití rozhraní .NET Framework 4.5.

## <a name="deploy-and-test"></a>Nasazení a testování

Po úpravě řešení pomocí doporučení z analyzátoru přenositelnosti .NET nebo z ruční analýzy je nutné jej otestovat pomocí služby HDInsight. Testování řešení v clusteru HDInsight založeném na Linuxu může odhalit drobné problémy, které je třeba opravit. Doporučujeme povolit další protokolování v aplikaci při testování.

Další informace o přístupu k protokolům naleznete v následujících dokumentech:

* [Přístup k protokolům aplikací Apache Hadoop YARN na Linuxu založeném na HDInsightu](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Další kroky

* [Použití jazyka C# s mapem MapReduce na webu HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Používejte uživatelem definované funkce Jazyka C# s Apache Hive a Apache Pig](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Vývoj topologií C# pro Apache Storm na HDInsightu](storm/apache-storm-develop-csharp-visual-studio-topology.md)
