---
title: Použití rozhraní .NET pro Apache Spark s Azure Synapse Analytics
description: Přečtěte si o používání rozhraní .NET a Apache Spark k dávkovému zpracování, streamování v reálném čase, strojovému učení a psaní ad hoc dotazů v poznámkových blocích Azure Synapse Analytics.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430510"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Použití rozhraní .NET pro Apache Spark s Azure Synapse Analytics

[.NET pro Apache Spark](https://dot.net/spark) je bezplatná, open source a podpora .NET napříč platformami pro Spark. .NET pro Apache Spark poskytuje vazby .NET pro Spark, které umožňují přístup k rozhraním API Spark prostřednictvím C# a F#. S rozhraním .NET pro Apache Spark máte možnost psát a spouštět uživatelem definované funkce pro Spark pomocí rozhraní .NET. Rozhraní .NET API pro Spark umožňují přístup ke všem aspektům Spark, které vám pomohou analyzovat vaše data, včetně Spark SQL a Structured Streaming.

Data můžete analyzovat pomocí rozhraní .NET pro definice dávkových úloh Apache Spark prostřednictvím sparku nebo pomocí interaktivních poznámkových bloků Azure Synapse Analytics. V tomto článku se dozvíte, jak používat .NET pro Apache Spark s Azure Synapse pomocí obou technik. 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Odeslat dávkové úlohy pomocí definice úlohy Spark

Navštivte kurz, kde se dozvíte, jak pomocí Azure Synapse Analytics [vytvářet definice úloh Apache Spark pro fondy Synapse Spark](apache-spark-job-definitions.md). Pokud jste nezabalili aplikaci k odeslání do Azure Synapse, proveďte následující kroky.

1. Chcete-li publikovat aplikaci, spusťte následující příkazy. Nezapomeňte nahradit *mySparkApp* s cestou k vaší aplikaci.

   **Ve Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **Na Linuxu:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET pro Apache Spark v poznámkových blocích Azure Synapse Analytics

Při vytváření nového poznámkového bloku zvolíte jazorné jádro, které chcete vyjádřit svou obchodní logiku. Existuje podpora jádra pro několik jazyků, včetně jazyka C#.

Pokud chcete ve svém poznámkovém bloku Azure Synapse Analytics použít rozhraní .NET pro Apache Spark, vyberte jako jádro **.NET Spark (C#)** a připojte poznámkový blok k existujícímu fondu Spark.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET pro Apache Spark v poznámkových blocích Azure Synapse Analytics 

Poznámkové bloky jsou skvělou volbou pro vytváření prototypů rozhraní .NET pro kanály a scénáře Apache Spark. Můžete začít pracovat s daty, porozumět jim, filtrovat, zobrazovat a vizualizovat data rychle a efektivně. Datoví inženýři, datoví vědci, obchodní analytici a inženýři strojového učení jsou schopni spolupracovat prostřednictvím sdíleného interaktivního dokumentu. Zobrazí se okamžité výsledky z průzkumu dat a můžete vizualizovat data ve stejném poznámkovém bloku.

### <a name="how-to-use-notebooks"></a>Jak používat poznámkové bloky

Při vytváření nového poznámkového bloku zvolíte jazorné jádro, které chcete vyjádřit obchodní logiku. Existuje podpora jádra pro několik jazyků, včetně jazyka C#. 

Pokud chcete ve svém poznámkovém bloku Azure Synapse Analytics použít rozhraní .NET pro Apache Spark, vyberte jako jádro **.NET Spark (C#)** a připojte poznámkový blok k existujícímu fondu Spark. 

Poznámkový blok .NET Spark je založen na interaktivních prostředích .NET a poskytuje interaktivní prostředí jazyka C# `spark` se schopností používat rozhraní .NET pro Spark po vybalení s proměnnou relace Spark, která je již předdefinovaná.

### <a name="sparknet-c-kernel-features"></a>funkce jádra Spark.NET C#

Následující funkce jsou k dispozici při použití rozhraní .NET pro Apache Spark v poznámkovém bloku Azure Synapse Analytics:

* Deklarativní HTML: Generujte výstup z buněk pomocí syntaxe HTML, jako jsou záhlaví, seznamy s odrážkami a dokonce i zobrazení obrázků.
* Jednoduché příkazy Jazyka C# (například přiřazení, tisk na konzolu, vyvolání výjimek a tak dále).
* Víceřádkové bloky kódu Jazyka C# (například if příkazy, foreach smyčky, definice tříd a tak dále).
* Přístup ke standardní knihovně Jazyka C# (například System, LINQ, Enumerables a tak dále).
* Podpora jazykových [funkcí jazyka C# 8.0](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* 'jiskra' jako předdefinovaná proměnná, která vám poskytne přístup k relaci Apache Spark.
* Podpora pro definování [uživatelem definovaných funkcí rozhraní .NET, které lze spustit v rámci Apache Spark](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql).
* Podpora vizualizace výstupu z úloh Spark pomocí různých grafů (například čáry, pruhu nebo histogramu) a rozložení `XPlot.Plotly` (například jednoho, překrytého a tak dále) pomocí knihovny.
* Možnost zahrnout balíčky NuGet do poznámkového bloku Jazyka C#.

## <a name="next-steps"></a>Další kroky

* [.NET pro dokumentaci Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [Interaktivní rozhraní .NET](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)