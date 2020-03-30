---
title: Spouštění dotazů Apache Hive pomocí hdinsight .NET SDK – Azure
description: Naučte se odesílat úlohy Apache Hadoop do Azure HDInsight Apache Hadoop pomocí HDInsight .NET SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: a9d71c8aebb9cc4a0adbd461aead6e2612bd13bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552487"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Spuštění dotazů Apache Hive pomocí sady HDInsight .NET SDK

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Přečtěte si, jak odesílat dotazy Apache Hive pomocí sady HDInsight .NET SDK. Napíšete c# program pro odeslání dotazu Hive pro výpis Hive tabulek a zobrazit výsledky.

> [!NOTE]  
> Kroky v tomto článku musí být provedeny z klienta systému Windows. Informace o používání klienta Linuxu, OS X nebo Unixu pro práci s Hive použijte volič karet zobrazený v horní části článku.

## <a name="prerequisites"></a>Požadavky

Než začnete tento článek, musíte mít následující položky:

* Cluster Apache Hadoop v HDInsightu. Viz [Začínáme používat Hadoop založený na Linuxu v HDInsightu](apache-hadoop-linux-tutorial-get-started.md).

    > [!IMPORTANT]  
    > září 15, 2017 HDInsight .NET SDK podporuje jenom vracející se výsledky dotazu Hive z účtů Azure Storage. Pokud tento příklad použijete s clusterem HDInsight, který používá Azure Data Lake Storage jako primární úložiště, nemůžete načíst výsledky hledání pomocí sady .NET SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/) 2013 a dále. Měl by být nainstalován alespoň **vývoj pracovního vytížení .NET.**

## <a name="run-a-hive-query"></a>Spuštění dotazu hive

Sada HDInsight .NET SDK poskytuje klientské knihovny ROZHRANÍ .NET, což usnadňuje práci s clustery HDInsight z rozhraní .NET.

1. Vytvořte konzolovou aplikaci Jazyka C# v sadě Visual Studio.

1. V konzole Správce balíčků Nuget spusťte následující příkaz:

        Install-Package Microsoft.Azure.Management.HDInsight.Job

1. Chcete-li inicializovat hodnoty proměnných, `ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName`upravte níže uvedený kód: . Potom použijte revidovaný kód jako celý obsah **Program.cs** v sadě Visual Studio.

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
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            // Only Azure Storage accounts are supported by the SDK
            private const string DefaultStorageAccountName = "<Default Storage Account Name>";
            private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
            private const string DefaultStorageContainerName = "<Default Blob Container Name>";

            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";

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

1. Stisknutím **klávesy F5** spusťte aplikaci.

Výstup aplikace by měl být podobný:

![Výstup úlohy HDInsight Hadoop Hive](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak odeslat dotazy Apache Hive pomocí HDInsight .NET SDK. Další informace naleznete v následujících článcích:

* [Začínáme se službou Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Vytvoření clusterů Apache Hadoop v HDInsightu](../hdinsight-hadoop-provision-linux-clusters.md)
* [Odkaz na hdinsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Použití Apache Sqoop s HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Vytváření aplikací .NET HDInsight pro neinteraktivní ověřování](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
