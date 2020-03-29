---
title: Odeslání úloh MapReduce pomocí sady HDInsight .NET SDK – Azure
description: Naučte se odesílat úlohy MapReduce do Azure HDInsight Apache Hadoop pomocí hdinsight .NET SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/15/2020
ms.openlocfilehash: e50510f2420d69be37af584a2648a794e1561ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157045"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Spouštění úloh MapReduce pomocí sady HDInsight .NET SDK

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Přečtěte si, jak odeslat úlohy MapReduce pomocí sady HDInsight .NET SDK. Clustery HDInsight jsou dodávány se souborem jar s některými ukázkami MapReduce. Jar soubor `/example/jars/hadoop-mapreduce-examples.jar`je .  Jedním ze vzorků je **počet slov**. Vyvíjíte konzolové aplikace Jazyka C# k odeslání úlohy počtu slov.  Úloha přečte `/example/data/gutenberg/davinci.txt` soubor a výstupy `/example/data/davinciwordcount`výsledků do .  Pokud chcete znovu spustit aplikaci, je nutné vyčistit výstupní složku.

> [!NOTE]  
> Kroky v tomto článku musí být provedeny z klienta systému Windows. Informace o používání klienta Linuxu, OS X nebo Unixu pro práci s Hive použijte volič karet zobrazený v horní části článku.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Hadoop na HDInsight. Viz [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Odeslat úlohy MapReduce pomocí sady HDInsight .NET SDK

Sada HDInsight .NET SDK poskytuje klientské knihovny rozhraní .NET, které usnadňují práci s clustery HDInsight z rozhraní .NET.

1. Spusťte Visual Studio a vytvořte konzolovou aplikaci Jazyka C#.

1. Přejděte na **tools** > **NuGet Package Manager** > **Console** a zadejte následující příkaz:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Napište níže uvedený kód do **Program.cs**. Potom kód upravte nastavením `existingClusterName`hodnot `existingClusterPassword` `defaultStorageAccountName`pro: , , `defaultStorageAccountKey`, a `defaultStorageContainerName`.

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
            private const string existingClusterPassword = "<Cluster User Password>";
            private const string defaultStorageAccountName = "<Default Storage Account Name>"; 
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";
    
            private const string existingClusterUsername = "admin";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
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

1. Stisknutím **klávesy F5** spusťte aplikaci.

Chcete-li úlohu spustit znovu, je nutné změnit název výstupní `/example/data/davinciwordcount`složky úlohy v ukázce, kterou je .

Po úspěšném dokončení úlohy aplikace vytiskne obsah `part-r-00000`výstupního souboru .

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili několik způsobů, jak vytvořit cluster HDInsight. Další informace naleznete v následujících článcích:

* Informace o odeslání úlohy Hive najdete v tématu [Spuštění dotazů Apache Hive pomocí sady HDInsight .NET SDK](apache-hadoop-use-hive-dotnet-sdk.md).
* Informace o vytváření clusterů HDInsight najdete [v tématu Vytváření clusterů Apache Hadoop založených na Linuxu v HDInsightu](../hdinsight-hadoop-provision-linux-clusters.md).
* Správa clusterů HDInsight najdete [v tématu Správa clusterů Apache Hadoop v HDInsightu](../hdinsight-administer-use-portal-linux.md).
* Informace o sponě HDInsight .NET SDK najdete v [tématu odkaz na hdinsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).
* Neinteraktivní ověřování v Azure najdete v [tématu Vytváření neinteraktivních aplikací HDInsight .NET](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).