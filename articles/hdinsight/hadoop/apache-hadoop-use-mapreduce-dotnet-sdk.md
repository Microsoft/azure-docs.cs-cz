---
title: Odesílání úloh MapReduce pomocí sady SDK rozhraní .NET HDInsight - Azure | Microsoft Docs
description: Zjistěte, jak k odesílání úloh MapReduce do Azure HDInsight Hadoop pomocí sady .NET SDK HDInsight.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
ms.assetid: c85e44b0-85fd-4185-ad1c-c34a9fe5ef44
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: 0ea6e559cd9fd857f66ca1d9a7ed91d5f9c238e4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Spuštění úloh MapReduce pomocí sady .NET SDK HDInsight
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Zjistěte, jak k odesílání úloh MapReduce pomocí sady .NET SDK HDInsight. HDInsight clustery jsou součástí soubor jar se některé ukázky MapReduce. Je na soubor jar */example/jars/hadoop-mapreduce-examples.jar*.  Jeden z ukázky je *wordcount*. Můžete vyvíjet konzolovou aplikaci C# se odeslat úlohu wordcount.  Načte úlohu */example/data/gutenberg/davinci.txt* souboru a vrací výsledky do */example/data/davinciwordcount*.  Pokud chcete znovu spusťte aplikaci, musí vyčistit do výstupní složky.

> [!NOTE]
> Kroky v tomto článku je potřeba provést z klienta Windows. Informace o používání Linux, OS X nebo Unix klienta pro práci s Hive použijte volič karty v horní článek ukazuje.
> 
> 

## <a name="prerequisites"></a>Požadavky
Před zahájením tohoto článku, musíte mít následující položky:

* **Cluster Hadoop v HDInsight**. V tématu [začít používat systémem Linux Hadoop v HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **Visual Studio 2013 nebo 2015 nebo 2017**.

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Odesílání úloh MapReduce pomocí sady .NET SDK HDInsight
.NET SDK služby HDInsight poskytuje klientské knihovny .NET, která usnadňuje práci s clustery HDInsight pomocí technologie .NET. 

**K odesílání úloh**

1. Vytvořte konzolovou aplikaci C# v sadě Visual Studio.
2. Z konzoly Správce balíčků NuGet spusťte následující příkaz:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```
3. Použijte následující kód:

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

Chcete-li úlohu znovu spustit, musíte změnit název složky výstup úlohy, v ukázce je "/ Příklad/data/davinciwordcount".

Po úspěšném dokončení úlohy aplikace vytiskne obsah výstupní soubor "část r-00000".

## <a name="next-steps"></a>Další postup
V tomto článku jste se naučili několik způsobů, jak vytvořit cluster služby HDInsight. Další informace naleznete v následujících článcích:

* Odeslání úlohy Hive, najdete v části [spouštění dotazů Hive pomocí sady .NET SDK HDInsight](apache-hadoop-use-hive-dotnet-sdk.md).
* Vytváření clusterů HDInsight, naleznete v části [vytvořit systémem Linux Hadoop clusterů v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Správa clusterů HDInsight, naleznete v části [spravovat Hadoop clusterů v HDInsight](../hdinsight-administer-use-portal-linux.md).
* Učení .NET SDK služby HDInsight, naleznete v části [referenční informace sady SDK rozhraní .NET HDInsight](https://msdn.microsoft.com/library/mt271028.aspx).
* Pro neinteraktivní ověřování v Azure najdete v tématu [vytvořit neinteraktivního ověřování aplikace .NET HDInsight](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).

