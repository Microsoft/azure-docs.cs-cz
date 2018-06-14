---
title: Ladění a analýze služby Hadoop s výpisů paměti haldy - Azure | Microsoft Docs
description: Automatické shromažďování výpisů paměti haldy pro služby Hadoop a umístěte do účtu úložiště objektů Blob v Azure pro ladění a analýzu.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e4ec4ebb-fd32-4668-8382-f956581485c4
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 0721d20987008e5cc6370c6e853440ce93edcfa9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31398114"
---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>V úložišti objektů Blob k ladění a analýze služby Hadoop výpisy paměti shromažďování haldy
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Výpisy haldy obsahovat snímek paměti aplikace, včetně hodnot proměnných v době, kdy byla vytvořena výpis. Takže se hodí pro diagnostiku problémů, ke kterým dochází za běhu. Výpisy haldy můžete automaticky shromážděna pro služby Hadoop a umístěn uvnitř účtu Azure Blob storage uživatele v části HDInsightHeapDumps /.

Shromažďování výpisů paměti haldy pro různé služby musí být povolen pro služby v jednotlivých clusterech. Výchozí hodnota pro tuto funkci je potřeba vypnout pro cluster. Tyto výpisy haldy může být velký, proto se doporučuje pro monitorování účtu úložiště Blob, kde se uloží po kolekce byla povolena.

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Informace v tomto článku se vztahují pouze na HDInsight se systémem Windows. Informace v HDInsight se systémem Linux najdete v tématu [výpisy paměti haldy povolit pro služby Hadoop v HDInsight se systémem Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)


## <a name="eligible-services-for-heap-dumps"></a>Oprávněných služeb pro výpisy haldy
Můžete povolit výpisů paměti haldy pro následující služby:

* **hcatalog** -tempelton
* **Hive** -hiveserver2, metaúložiště, derbyserver
* **mapreduce** -jobhistoryserver
* **yarn** -resourcemanager, nodemanager, timelineserver
* **hdfs** -datanode, secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Konfigurační prvky, které umožňují výpisů paměti haldy
Chcete-li na výpisů paměti haldy pro službu, musíte nastavit odpovídající konfigurační prvky v části pro služby, který je určený **service_name**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

Hodnota **service_name** mohou být některé z služeb tady: tempelton, hiveserver2, metaúložiště, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode, nebo namenode.

## <a name="enable-using-azure-powershell"></a>Povolit pomocí Azure PowerShell
Například pokud chcete zapnout haldy výpisy pomocí prostředí Azure PowerShell pro jobhistoryserver, můžete použít následující skript:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Povolit pomocí sady .NET SDK
Například pokud chcete zapnout haldy výpisy pomocí .NET SDK služby Azure HDInsight pro jobhistoryserver, můžete použít následující kód:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
