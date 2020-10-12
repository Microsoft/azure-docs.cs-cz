---
title: Spouštění dotazů Apache Hive pomocí rozhraní HDInsight .NET SDK – Azure
description: Naučte se odesílat Apache Hadoop úlohy do Azure HDInsight Apache Hadoop pomocí sady HDInsight .NET SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/24/2019
ms.openlocfilehash: cdb2207ab834ab84cedd2ad23e5e26186ac44039
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020469"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Spouštění dotazů Apache Hive pomocí sady HDInsight .NET SDK

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Naučte se odesílat Apache Hive dotazy pomocí sady HDInsight .NET SDK. Napíšete program v jazyce C#, který odešle dotaz na podregistr pro výpis tabulek podregistru a zobrazí výsledky.

> [!NOTE]  
> Kroky v tomto článku je třeba provést z klienta systému Windows. Informace o použití klienta se systémem Linux, OS X nebo UNIX pro práci s podregistrem získáte pomocí voliče karet zobrazeného v horní části článku.

## <a name="prerequisites"></a>Požadavky

Než začnete tento článek, musíte mít následující položky:

* Cluster Apache Hadoop v HDInsight. Přečtěte si téma Začínáme [používat Hadoop se systémem Linux ve službě HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!IMPORTANT]  
    > Od 15. září 2017 podporuje sada HDInsight .NET SDK jenom vracení výsledků dotazů na podregistr z účtů Azure Storage. Pokud použijete tento příklad s clusterem HDInsight, který používá Azure Data Lake Storage jako primární úložiště, nemůžete načíst výsledky hledání pomocí sady .NET SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/) 2013 a novější. Je potřeba nainstalovat minimálně úlohu **vývoj pro desktopy .NET** .

## <a name="run-a-hive-query"></a>Spustit dotaz na podregistr

Sada HDInsight .NET SDK poskytuje klientské knihovny .NET, které usnadňují práci s clustery HDInsight z .NET.

1. Vytvořte konzolovou aplikaci v jazyce C# v aplikaci Visual Studio.

1. Z konzoly Správce balíčků NuGet spusťte následující příkaz:

    ```console
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Upravte kód níže pro inicializaci hodnot proměnných: `ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName` . Pak použijte revidovaný kód jako celý obsah **program.cs** v aplikaci Visual Studio.

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

1. Stisknutím klávesy **F5** spusťte aplikaci.

Výstup aplikace by měl vypadat přibližně takto:

![Výstup úlohy podregistru HDInsight Hadoop](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak odesílat Apache Hive dotazy pomocí sady HDInsight .NET SDK. Další informace najdete v těchto článcích:

* [Začínáme se službou Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Vytváření clusterů Apache Hadoop ve službě HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Referenční informace k sadě HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Použití Apache Sqoop se službou HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Vytváření aplikací .NET HDInsight pro neinteraktivní ověřování](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
