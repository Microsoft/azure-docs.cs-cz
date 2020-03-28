---
title: 'Azure HDInsight: ukázky .NET'
description: Najděte příklady Jazyka C# .NET na GitHubu pro běžné úkoly pomocí sady HDInsight SDK pro rozhraní .NET.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: sample
ms.date: 12/06/2019
ms.openlocfilehash: 097323b4566daeda737428121bee4d64e9f7248b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74951560"
---
# <a name="azure-hdinsight-net-samples"></a>Azure HDInsight: ukázky .NET

> [!div class="op_single_selector"]
> * [Příklady v .NET](hdinsight-sdk-dotnet-samples.md)
> * [Příklady v Pythonu](hdinsight-sdk-python-samples.md)
> * [Příklady v Javě](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

Tento článek obsahuje:

* Odkazy na ukázky pro úlohy vytváření clusteru.
* Odkazy na referenční obsah pro jiné úlohy správy.

Můžete si [aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Za své předplatné sady Visual Studio každý měsíc získáváte kredity, které můžete použít k placení za služby Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisite"></a>Požadavek

[Sada Azure HDInsight SDK pro .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>Správa clusteru – tvorba

* [Vytvoření clusteru Kafka](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateKafkaClusterSample.cs)
* [Vytvoření clusteru Spark](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateSparkClusterSample.cs)
* [Vytvoření clusteru Spark s Azure Data Lake Storage Gen2](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateHadoopClusterWithAdlsGen2Sample.cs)
* [Vytvoření clusteru Spark s balíčkem zabezpečení rozlehlé sítě (ESP)](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateEspClusterSample.cs)

Tyto ukázky pro rozhraní .NET můžete získat klonováním úložiště [GitHub hdinsight-dotnet-sdk-samples.](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples)

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

Fragmenty kódu pro tuto další funkci sady SDK naleznete v [referenční dokumentaci HDInsight SDK pro rozhraní .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).
