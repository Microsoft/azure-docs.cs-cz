---
title: Ladění a analýza služby Hadoop s výpisů paměti haldy – Azure
description: Automatické shromažďování výpisů paměti haldy pro služby Hadoop a umístíte text do účtu úložiště objektů Blob v Azure pro ladění a analýzu.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 35f7843ebf49e79d9045c72493bb38b218234288
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43099763"
---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>Shromažďování výpisů haldy v úložišti objektů Blob pro ladění a analýza služby Hadoop
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Výpisů paměti haldy obsahují snímek paměti aplikace, včetně hodnot proměnných v době, kdy byl vytvořen výpis paměti. Takže se hodí pro diagnostiku problémů, ke kterým dochází za běhu. Výpisů paměti haldy můžete automaticky shromážděna pro služby Hadoop a umístit do účtu úložiště objektů Blob v Azure uživatele v rámci HDInsightHeapDumps /.

Shromažďování výpisů paměti haldy pro různé služby musí být povolena pro služby v jednotlivých clusterech. Výchozí hodnota pro tuto funkci je možné vypnout pro cluster. Tyto výpisů paměti haldy můžou být velké, proto se doporučuje pro monitorování účtu služby Blob storage, kde se uloží po kolekce se povolila.

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Informace v tomto článku se vztahují pouze na HDInsight se systémem Windows. Informace týkající se systémem Linux HDInsight naleznete v tématu [výpisů povolit haldy pro služby Hadoop v HDInsight se systémem Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)


## <a name="eligible-services-for-heap-dumps"></a>Opravňující služby pro výpisů paměti haldy
Můžete povolit výpisů paměti haldy pro následující služby:

* **hcatalog** -tempelton
* **Hive** -hiveserver2, metastore, derbyserver
* **mapreduce** -jobhistoryserver
* **yarn** – správce prostředků, nodemanager, timelineserver
* **hdfs** -datanode secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Konfigurační prvky, které povolení výpisů paměti haldy
Zapnutí výpisů paměti haldy pro služby, je nutné nastavit odpovídající konfigurační prvky v části pro tuto službu, která je určená **service_name**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

Hodnota **service_name** mohou být kterékoli ze služeb tady: tempelton hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode secondarynamenode, nebo namenode.

## <a name="enable-using-azure-powershell"></a>Povolení s využitím Azure Powershellu
Například pomocí Azure Powershellu pro jobhistoryserver zapnout výpisů paměti haldy, můžete použít následující skript:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Povolit pomocí sady .NET SDK
Například pomocí .NET SDK služby Azure HDInsight pro jobhistoryserver zapnout výpisů paměti haldy, můžete použít následující kód:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
