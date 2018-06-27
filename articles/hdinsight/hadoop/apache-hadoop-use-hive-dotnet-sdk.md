---
title: Spouštění dotazů Hive pomocí sady SDK rozhraní .NET HDInsight - Azure | Microsoft Docs
description: Zjistěte, jak k odesílání úloh Hadoop do HDInsight Hadoop Azure pomocí sady .NET SDK HDInsight.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
ms.assetid: 4e291890-f8b4-426c-b5e8-d4fd512ff042
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 41c568191ab47faba8f67e855f0a52d5d97ffc6b
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37020643"
---
# <a name="run-hive-queries-using-hdinsight-net-sdk"></a>Spouštění dotazů Hive pomocí sady .NET SDK HDInsight
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Zjistěte, jak k odesílání dotazů Hive pomocí sady .NET SDK HDInsight. Zápis programu C# k odesílání dotazů Hive pro výpis tabulek Hive a zobrazit výsledky.

> [!NOTE]
> Kroky v tomto článku je potřeba provést z klienta Windows. Informace o používání Linux, OS X nebo Unix klienta pro práci s Hive použijte volič karty v horní článek ukazuje.

## <a name="prerequisites"></a>Požadavky
Před zahájením tohoto článku, musíte mít následující položky:

* **Cluster Hadoop v HDInsight**. V tématu [začít používat systémem Linux Hadoop v HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!WARNING]
    > Od 15. září 2017 sady .NET SDK HDInsight podporuje pouze vracejících výsledky dotazu Hive z účty Azure Storage. Pokud používáte v tomto příkladu se cluster služby HDInsight, která používá Azure Data Lake Store jako primární úložiště, nelze načíst výsledky vyhledávání pomocí .NET SDK.

* **Visual Studio 2013 nebo 2015 nebo 2017**.

## <a name="run-a-hive-query"></a>Spouštění dotazů Hive
.NET SDK služby HDInsight poskytuje klientské knihovny .NET, která usnadňuje práci s clustery HDInsight pomocí technologie .NET. 

**K odesílání úloh**

1. Vytvořte konzolovou aplikaci C# v sadě Visual Studio.
2. Z konzoly Správce balíčků Nuget spusťte následující příkaz:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. Použijte následující kód:

    ```csharp
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
   
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
                
                // Only Azure Storage accounts are supported by the SDK
                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitHiveJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitHiveJob()
                {
                    Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
                    List<string> args = new List<string> { { "argA" }, { "argB" } };
                    var parameters = new HiveJobSubmissionParameters
                    {
                        Query = "SHOW TABLES",
                        Defines = defines,
                        Arguments = args
                    };
   
                    System.Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);
   
                    System.Console.WriteLine("Waiting for the job completion ...");
   
                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }
   
                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure
   
                    System.Console.WriteLine("Job output is: ");
   
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }
    ```
4. Stisknutím klávesy **F5** spusťte aplikaci.

Výstup aplikace musí být podobně jako:

![HDInsight Hadoop Hive výstup úlohy](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Další postup
V tomto článku jste se naučili několik způsobů, jak vytvořit cluster služby HDInsight. Další informace naleznete v následujících článcích:

* [Začínáme s Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Vytvoření clusterů systému Hadoop v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Správa clusterů systému Hadoop v HDInsight pomocí portálu Azure](../hdinsight-administer-use-management-portal.md)
* [Referenční informace sady SDK rozhraní .NET HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Použití Pigu se službou HDInsight](hdinsight-use-pig.md)
* [Použití nástroje Sqoop s HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Vytvoření aplikací .NET HDInsight pro neinteraktivní ověřování](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
 


