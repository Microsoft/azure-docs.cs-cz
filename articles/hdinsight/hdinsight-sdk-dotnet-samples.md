---
title: 'Azure HDInsight: Ukázky .NET'
description: Najít C# příklady .NET na Githubu pro běžné úlohy pomocí sady HDInsight SDK pro .NET.
author: hrasheed-msft
ms.service: hdinsight
ms.topic: sample
ms.date: 04/15/2019
ms.author: hrasheed
ms.openlocfilehash: 94e2f007f70d55387b641b3d9166fa84f26f16ba
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2019
ms.locfileid: "59580272"
---
# <a name="azure-hdinsight-net-samples"></a>Azure HDInsight: Ukázky .NET

> [!div class="op_single_selector"]
> * [Příklady v .NET](hdinsight-sdk-dotnet-samples.md)
> * [Příklady v Pythonu](hdinsight-sdk-python-samples.md)
> * [Příklady v Javě](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

Tento článek obsahuje:

* Odkazy na ukázky pro úlohy vytváření clusteru.
* Obsahuje odkazy na referenční obsah pro další úlohy správy.

Je možné [aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Vaše předplatné sady Visual Studio vám dává kredity každý měsíc, můžete použít k placení za služby Azure.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Azure HDInsight SDK pro .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>Správa clusteru – vytváření

* [Vytvoření clusteru Kafka](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateKafkaClusterSample.cs)
* [Vytvoření clusteru Spark](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateSparkClusterSample.cs)
* [Vytvoření clusteru Spark s Azure Data Lake Storage Gen2](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateHadoopClusterWithAdlsGen2Sample.cs)
* [Vytvoření clusteru Spark s Enterprise Security Package (ESP)](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateEspClusterSample.cs)

Tyto ukázky pro rozhraní .NET můžete získat pomocí klonování [hdinsight-dotnet-sdk-samples](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples) úložiště GitHub.

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

Fragmenty kódu pro tento další funkce sady SDK najdete v [sady HDInsight SDK pro .NET referenční dokumentaci](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).