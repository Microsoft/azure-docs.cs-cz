---
title: Pomocí Hadoop MapReduce na Linuxovým systémem HDInsight – Azure .NET
description: Další informace o použití aplikace .NET pro streamování MapReduce na systémem Linux HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 3a9a026af67cd5528dfc00148bcbbcef82b8b189
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714657"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migrace řešení .NET pro Windows na základě HDInsight založených na Linuxu HDInsight

Použití clusterů HDInsight se systémem Linux [Mono (https://mono-project.com) ](https://mono-project.com) ke spouštění aplikací .NET. Mono umožňuje používat součásti rozhraní .NET, jako jsou třeba aplikace MapReduce se systémem Linux HDInsight. V tomto dokumentu zjistěte, jak migrace řešení .NET vytvořené pro clustery HDInsight se systémem Windows pro práci s Mono na HDInsight založených na Linuxu.

## <a name="mono-compatibility-with-net"></a>Mono kompatibilitu s .NET

Mono verze 4.2.1 je součástí HDInsight verze 3.6. Další informace o verzi Mono součástí HDInsight najdete v tématu [verzí komponenty HDInsight](hdinsight-component-versioning.md). Instalace konkrétní verze architektury mono, najdete v článku [instalace nebo aktualizace Mono](hdinsight-hadoop-install-mono.md) dokumentu.

Další informace o kompatibilitě mezi Mono a .NET najdete v článku [Mono compatibility (https://www.mono-project.com/docs/about-mono/compatibility/) ](https://www.mono-project.com/docs/about-mono/compatibility/) dokumentu.

> [!IMPORTANT]  
> Je kompatibilní s Mono rozhraní SCP.NET. Další informace o použití SCP.NET architektuře Mono, naleznete v tématu [vývoj topologií C# pro Apache Storm v HDInsight pomocí Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Automatizované přenositelnost analýzy

[.NET Portability Analyzeru](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) umožňuje generovat sestavu nekompatibility mezi vaší aplikací a Mono. Ke konfiguraci analyzátoru ke kontrole vaší aplikace pro Mono přenositelnost, postupujte následovně:

1. Nainstalujte [.NET Portability Analyzeru](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Během instalace vyberte verzi sady Visual Studio používat.

2. Ze sady Visual Studio 2015, vyberte __analyzovat__ > __nastavení analyzátor přenositelnosti__a ujistěte se, že __4.5__ se změnami __Mono__ oddílu.

    ![4.5 změnami Mono části Nastavení analyzátoru](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Vyberte __OK__ uložte konfiguraci.

3. Vyberte __analyzovat__ > __analyzovat sestavení přenositelnost__. Vyberte sestavení, která obsahuje vaše řešení a pak vyberte __otevřít__ spusťte analýzu.

4. Po dokončení analýzy, vyberte __analyzovat__ > __zobrazit sestavy analýzy__. V __výsledky analýzy přenositelnost__vyberte __otevřete sestavu__ k otevření sestavy.

    ![Dialogové okno výsledků analyzátor přenositelnosti](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> Analyzátor nemůže zachytit všech problémů v rámci řešení. Například cestu k souboru z `c:\temp\file.txt` se považuje za OK, pokud Mono běží na Windows. Stejné cesta není platná na platformě Linux.

## <a name="manual-portability-analysis"></a>Ruční přenositelnost analýzy

Proveďte ruční auditu podle informací uvedených v kódu [přenositelnost aplikací (https://www.mono-project.com/docs/getting-started/application-portability/) ](https://www.mono-project.com/docs/getting-started/application-portability/) dokumentu.

## <a name="modify-and-build"></a>Upravit a sestavení

Můžete nadále používat Visual Studio pro vytváření řešení .NET pro HDInsight. Ale musíte zajistit, že projekt je nakonfigurován na použití rozhraní .NET Framework 4.5.

## <a name="deploy-and-test"></a>Nasazení a testování

Jakmile změnili jste svoje řešení podle doporučení z .NET Portability Analyzeru nebo ruční analýzy, musíte ho otestovat s HDInsight. Testování řešení v clusteru HDInsight se systémem Linux můžete odhalit drobné problémy, které je třeba jej opravit. Doporučujeme povolit dodatečné protokolování v aplikaci během testování.

Další informace o přístup k protokolům najdete v následujících dokumentech:

* [Přístup aplikací Apache Hadoop YARN přihlášení založené na Linuxu HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Další postup

* [Použití jazyka C# s MapReduce v HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Použití C# uživatelsky definovaných funkcí s Apache Hivu a Apache Pig](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Vývoj C# topologií pro Apache Storm v HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
