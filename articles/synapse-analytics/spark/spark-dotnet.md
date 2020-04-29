---
title: Použití .NET pro Apache Spark s Azure synapse Analytics
description: Přečtěte si, jak pomocí .NET a Apache Spark provádět dávkové zpracování, streamování v reálném čase, strojové učení a zápis dotazů ad-hoc do notebooků Azure synapse Analytics.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430510"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Použití .NET pro Apache Spark s Azure synapse Analytics

[Rozhraní .NET pro Apache Spark](https://dot.net/spark) je zdarma, open source a podpora .NET pro různé platformy pro Spark. .NET for Apache Spark poskytuje vazby rozhraní .NET pro Spark, které umožňují přístup k rozhraním API Spark přes jazyky C# a F #. Díky rozhraní .NET pro Apache Spark máte možnost psát a spouštět uživatelsky definované funkce pro Spark pomocí .NET. Rozhraní .NET API pro Spark vám umožní přístup ke všem aspektům Sparku, které vám pomůžou analyzovat vaše data, včetně Spark SQL a strukturovaného streamování.

Data můžete analyzovat pomocí technologie .NET pro Apache Spark prostřednictvím definic úloh Spark Batch nebo pomocí interaktivních poznámkových bloků služby Azure synapse Analytics. V tomto článku se dozvíte, jak používat rozhraní .NET pro Apache Spark s využitím obou postupů v rámci služby Azure synapse. 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Odeslání dávkových úloh pomocí definice úlohy Spark

V tomto kurzu se dozvíte, jak pomocí služby Azure synapse Analytics [vytvořit definice úloh Apache Spark pro fondy synapse Spark](apache-spark-job-definitions.md). Pokud jste aplikaci nebalíte k odeslání do Azure synapse, proveďte následující kroky.

1. Spusťte následující příkazy pro publikování aplikace. Nezapomeňte nahradit *mySparkApp* cestou k vaší aplikaci.

   **Ve Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **V systému Linux:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET pro Apache Spark ve službě Azure synapse Analytics – notebooky

Při vytváření nového poznámkového bloku si zvolíte jádro jazyka, u kterého chcete vyjádřit obchodní logiku. Existuje podpora jádra pro několik jazyků, včetně C#.

Pokud chcete použít .NET pro Apache Spark ve vašem notebooku Azure synapse Analytics, vyberte jako jádro **.NET Spark (C#)** a připojte notebook k existujícímu fondu Spark.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET pro Apache Spark ve službě Azure synapse Analytics – notebooky 

Poznámkové bloky jsou skvělou možností pro vytváření prototypů rozhraní .NET pro Apache Spark kanálů a scénářů. Můžete začít pracovat s, porozumění, filtrováním, zobrazením a vizualizací dat rychle a efektivně. Specialisté na data, odborníci na data, obchodní analytiké a technici strojového učení můžou spolupracovat přes sdílený interaktivní dokument. Zobrazí se okamžité výsledky z průzkumu dat a mohou vizualizovat data ve stejném poznámkovém bloku.

### <a name="how-to-use-notebooks"></a>Použití poznámkových bloků

Při vytváření nového poznámkového bloku si zvolíte jádro jazyka, u kterého chcete vyjádřit svoji obchodní logiku. Existuje podpora jádra pro několik jazyků, včetně C#. 

Pokud chcete použít .NET pro Apache Spark ve vašem notebooku Azure synapse Analytics, vyberte jako jádro **.NET Spark (C#)** a připojte notebook k existujícímu fondu Spark. 

Poznámkový blok .NET Spark je založený na interaktivním prostředí .NET a poskytuje interaktivní prostředí v C# s možností používat rozhraní .NET pro Spark mimo box, který je `spark` už předdefinovaný.

### <a name="sparknet-c-kernel-features"></a>Spark.NET funkce jádra C#

Při použití rozhraní .NET pro Apache Spark v notebooku Azure synapse Analytics jsou k dispozici následující funkce:

* Deklarativní HTML: vygenerujte výstup z vašich buněk pomocí syntaxe HTML, jako jsou záhlaví, seznamy s odrážkami a dokonce i zobrazení obrázků.
* Jednoduché příkazy jazyka C# (například přiřazení, tisk do konzoly, vyvolání výjimek atd.).
* Víceřádkové bloky kódu C# (například příkazy if, smyčky foreach, definice tříd a tak dále).
* Přístup ke standardní knihovně C# (například System, LINQ, Vyčíslitelnés a tak dále).
* Podpora pro [funkce jazyka C# 8,0](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Spark jako předem definovaná proměnná, která vám umožní přístup k vaší Apache Spark relaci.
* Podpora definování [uživatelsky definovaných funkcí rozhraní .NET, které mohou běžet v rámci Apache Spark](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql).
* Podpora vizualizace výstupu z úloh Sparku pomocí různých grafů (například spojnicový, pruhový nebo histogram) a rozložení (například jednoduché, překrytí a tak dále) pomocí `XPlot.Plotly` knihovny.
* Možnost zahrnout balíčky NuGet do poznámkového bloku C#

## <a name="next-steps"></a>Další kroky

* [Dokumentace k rozhraní .NET pro Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET Interactive](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)