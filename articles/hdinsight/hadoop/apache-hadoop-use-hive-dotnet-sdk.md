---
title: Spouštění dotazů Hive pomocí sady HDInsight .NET SDK – Azure
description: Zjistěte, jak k odesílání úloh Hadoop do Azure HDInsight Hadoop pomocí sady HDInsight .NET SDK.
ms.reviewer: jasonh
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 5cababaa7c808969e6e24a70581f54639f269621
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016008"
---
# <a name="run-hive-queries-using-hdinsight-net-sdk"></a>Spouštění dotazů Hive pomocí sady HDInsight .NET SDK
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Zjistěte, jak k odesílání dotazů Hive pomocí sady HDInsight .NET SDK. Napište program C# se odeslat dotaz Hive pro zobrazení seznamu tabulek Hive a zobrazení výsledků.

> [!NOTE]
> Kroky v tomto článku je potřeba provést z klienta Windows. Informace o použití systému Linux, OS X nebo Unix klienta pro práci s Hive použijte volič karty zobrazí v horní části článku.

## <a name="prerequisites"></a>Požadavky
Před zahájením tohoto článku, musíte mít následující položky:

* **Cluster Hadoop v HDInsight**. Zobrazit [Začínáme používat Hadoop využívající systém Linux v HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!WARNING]
    > Od 15. září 2017 sady HDInsight .NET SDK podporuje pouze vrácení výsledků dotazu Hive z účtů služby Azure Storage. Pokud používáte v tomto příkladu se cluster HDInsight, používající Azure Data Lake Store jako primární úložiště, nelze načíst výsledky hledání pomocí sady .NET SDK.

* **Visual Studio 2013/2015/2017**.

## <a name="run-a-hive-query"></a>Spuštění dotazu Hive
Sady HDInsight .NET SDK obsahuje klientské knihovny .NET, která usnadňuje práci s clustery HDInsight z .NET. 

**K odesílání úloh**

1. Vytvořte konzolovou aplikaci C# v sadě Visual Studio.
2. Z konzoly Správce balíčků Nuget spusťte následující příkaz:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. Pomocí následujícího kódu:

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

![Výstup úlohy Hive v HDInsight Hadoop](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Další postup
V tomto článku jste se naučili několik způsobů, jak vytvořit HDInsight cluster. Další informace naleznete v následujících článcích:

* [Začínáme s Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Vytvoření clusterů Hadoop v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Správa clusterů Hadoop v HDInsight pomocí webu Azure portal](../hdinsight-administer-use-management-portal.md)
* [Referenční dokumentace sady HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Použití Pigu se službou HDInsight](hdinsight-use-pig.md)
* [Použití nástroje Sqoop se HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Vytvoření aplikací .NET HDInsight pro neinteraktivní ověřování](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
 


