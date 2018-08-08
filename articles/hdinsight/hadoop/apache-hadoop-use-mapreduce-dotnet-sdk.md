---
title: Odeslání úlohy mapreduce je možné pomocí sady HDInsight .NET SDK – Azure
description: Zjistěte, jak odesílat úlohy MapReduce na Azure HDInsight Hadoop pomocí sady HDInsight .NET SDK.
editor: jasonwhowell
services: hdinsight
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: a22a2c75f5f520fb74d7ef6a57ff519e95ad273e
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39596478"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Spuštění úlohy mapreduce je možné pomocí sady HDInsight .NET SDK
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Zjistěte, jak odesílat úlohy mapreduce je možné pomocí sady HDInsight .NET SDK. HDInsight clustery součástí soubor jar s pár ukázek MapReduce. Na soubor jar je */example/jars/hadoop-mapreduce-examples.jar*.  Jednou z ukázek je *wordcount*. Můžete vyvíjet aplikaci konzoly C# se odeslat úlohu wordcount.  Načte úlohu */example/data/gutenberg/davinci.txt* souboru a vypíše výsledky do */example/data/davinciwordcount*.  Pokud chcete znovu spustit aplikaci, musí vyčištění výstupní složky.

> [!NOTE]
> Kroky v tomto článku je potřeba provést z klienta Windows. Informace o použití systému Linux, OS X nebo Unix klienta pro práci s Hive použijte volič karty zobrazí v horní části článku.
> 
> 

## <a name="prerequisites"></a>Požadavky
Před zahájením tohoto článku, musíte mít následující položky:

* **Cluster Hadoop v HDInsight**. Zobrazit [Začínáme používat Hadoop využívající systém Linux v HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **Visual Studio 2013/2015/2017**.

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Odeslání úlohy mapreduce je možné pomocí sady HDInsight .NET SDK
Sady HDInsight .NET SDK obsahuje klientské knihovny .NET, která usnadňuje práci s clustery HDInsight z .NET. 

**K odesílání úloh**

1. Vytvořte konzolovou aplikaci C# v sadě Visual Studio.
2. Z konzoly Správce balíčků NuGet spusťte následující příkaz:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```
3. Pomocí následujícího kódu:

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string existingClusterUsername = "<Cluster Username>";
            private const string existingClusterPassword = "<Cluster User Password>";

            private const string defaultStorageAccountName = "<Default Storage Account Name>"; //<StorageAccountName>.blob.core.windows.net
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";

            private const string sourceFile = "/example/data/gutenberg/davinci.txt";  
            private const string outputFolder = "/example/data/davinciwordcount";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);

                SubmitMRJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };

                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };

                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
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
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + 
                        defaultStorageAccountName + 
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); 
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }
    ```

4. Stisknutím klávesy **F5** spusťte aplikaci.

Znovu spustit úlohu, musíte změnit název složky výstup úlohy v ukázce je "/ Příklad/data/davinciwordcount".

Po úspěšném dokončení úlohy aplikace vytiskne obsah výstupního souboru "část r-00000".

## <a name="next-steps"></a>Další postup
V tomto článku jste se naučili několik způsobů, jak vytvořit HDInsight cluster. Další informace naleznete v následujících článcích:

* Odeslání úlohy Hive, najdete v části [spouštění dotazů Hive pomocí sady HDInsight .NET SDK](apache-hadoop-use-hive-dotnet-sdk.md).
* Vytváření clusterů HDInsight, naleznete v tématu [založených na Linuxu vytvoření Hadoop clusterů v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Správa clusterů HDInsight, naleznete v tématu [spravovat Hadoop clusterů v HDInsight](../hdinsight-administer-use-portal-linux.md).
* Učení sady HDInsight .NET SDK, naleznete v tématu [referenční sady HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).
* Pro neinteraktivní ověřování v Azure, najdete v článku [vytváření aplikací .NET HDInsight jako neinteraktivní ověřování](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).

