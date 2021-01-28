---
title: Odeslání úloh MapReduce pomocí sady HDInsight .NET SDK – Azure
description: Naučte se odesílat MapReduce úlohy do Azure HDInsight Apache Hadoop pomocí sady HDInsight .NET SDK.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/15/2020
ms.openlocfilehash: 8fbcb66be11c7c77a9bfaf0e6ec790622dcbbda7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932186"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Spouštění úloh MapReduce pomocí sady HDInsight .NET SDK

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Naučte se odesílat MapReduce úlohy pomocí sady HDInsight .NET SDK. Clustery HDInsight se dodává se souborem jar s některými MapReduce ukázkami. Soubor JAR je `/example/jars/hadoop-mapreduce-examples.jar` .  Jedna z ukázek je **WORDCOUNT**. Vyvíjíte konzolovou aplikaci v jazyce C# pro odeslání WORDCOUNT úlohy.  Úloha načte `/example/data/gutenberg/davinci.txt` soubor a výstupem výsledků do `/example/data/davinciwordcount` .  Chcete-li spustit aplikaci znovu, je nutné vyčistit výstupní složku.

> [!NOTE]  
> Kroky v tomto článku je třeba provést z klienta systému Windows. Informace o použití klienta se systémem Linux, OS X nebo UNIX pro práci s podregistrem získáte pomocí voliče karet zobrazeného v horní části článku.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Hadoop v HDInsight. Další informace najdete v tématu [Vytvoření clusterů Apache Hadoop pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Odeslání úloh MapReduce pomocí sady HDInsight .NET SDK

Sada HDInsight .NET SDK poskytuje klientské knihovny .NET, které usnadňují práci s clustery HDInsight z .NET.

1. Spusťte Visual Studio a vytvořte konzolovou aplikaci v jazyce C#.

1. Přejděte do **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků** a zadejte následující příkaz:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Zkopírujte kód uvedený níže do **program.cs**. Pak upravte kód nastavením hodnot pro: `existingClusterName` , `existingClusterPassword` , `defaultStorageAccountName` , `defaultStorageAccountKey` a `defaultStorageContainerName` .

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

1. Stisknutím klávesy **F5** spusťte aplikaci.

Pokud chcete úlohu znovu spustit, musíte v ukázce změnit název výstupní složky úlohy `/example/data/davinciwordcount` .

Po úspěšném dokončení úlohy aplikace vytiskne obsah výstupního souboru `part-r-00000` .

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o několika způsobech, jak vytvořit cluster HDInsight. Další informace najdete v těchto článcích:

* Informace o odeslání úlohy podregistru najdete v tématu [spuštění dotazů Apache Hive pomocí sady HDInsight .NET SDK](apache-hadoop-use-hive-dotnet-sdk.md).
* Informace o vytváření clusterů HDInsight najdete [v tématu Vytvoření clusterů Apache Hadoop se systémem Linux v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Informace o správě clusterů HDInsight najdete v tématu [správa Apache Hadoop clusterů ve službě HDInsight](../hdinsight-administer-use-portal-linux.md).
* Výukovou sadu SDK pro HDInsight .NET najdete v tématu Referenční informace k [sadě HDInsight .NET SDK](/dotnet/api/overview/azure/hdinsight).
* V případě neinteraktivního ověřování v Azure, přečtěte si téma [Vytvoření aplikací .NET HDInsight, které neumožňují interaktivní ověřování](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).
