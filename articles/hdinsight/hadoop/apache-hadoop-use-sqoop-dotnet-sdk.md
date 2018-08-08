---
title: Spouštění úloh Sqoop pomocí .NET a HDInsight – Azure
description: Zjistěte, jak pomocí sady HDInsight .NET SDK spustit Sqoop import a export mezi clusterem Hadoop a službě Azure SQL database.
keywords: sqoop úlohy
editor: jasonwhowell
services: hdinsight
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: 19c275de80b872fe214e45a52de7d6fb283daf41
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39595140"
---
# <a name="run-sqoop-jobs-by-using-net-sdk-for-hadoop-in-hdinsight"></a>Spouštění úloh Sqoop pomocí sady .NET SDK pro Hadoop v HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Zjistěte, jak pomocí .NET SDK služby Azure HDInsight můžete spouštět úlohy Sqoop v HDInsight pro import a export mezi clusterem HDInsight a Azure SQL database nebo databáze systému SQL Server.

> [!NOTE]
> I když používáte postupy v tomto článku se buď cluster HDInsight založených na Windows nebo Linux, fungují pouze z klienta Windows. Zvolit jiné metody, použijte volič karty v horní části tohoto článku.
> 

## <a name="prerequisites"></a>Požadavky
Než začnete tento kurz, musíte mít následující položky:

* Cluster Hadoop v HDInsight. Další informace najdete v tématu [vytvoření clusteru a SQL database](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Pomocí Sqoop na clusterech HDInsight pomocí sady .NET SDK
Sady HDInsight .NET SDK obsahuje klientské knihovny .NET, tak, aby bylo jednodušší fungují s clustery HDInsight z .NET. V této části vytvoříte aplikaci konzoly C# pro export hivesampletable do tabulky Azure SQL Database, kterou jste vytvořili dříve v tomto kurzu.

## <a name="submit-a-sqoop-job"></a>Odeslání úlohy Sqoop

1. Vytvořte konzolovou aplikaci C# v sadě Visual Studio.

2. V konzole Správce balíčků Visual Studio importu balíčku ho spuštěním následujícího příkazu NuGet:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job

3. Pomocí následujícího kódu v souboru Program.cs:
   
        using System.Collections.Generic;
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
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitSqoopJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
   
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
   
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
   
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
   
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }

4. Chcete-li spustit program, vyberte **F5** klíč. 

## <a name="limitations"></a>Omezení
Linuxovým systémem HDInsight představuje následující omezení:

* Hromadný export: The Sqoop konektor, který slouží k exportu dat Microsoft SQL Server nebo Azure SQL Database aktuálně nepodporuje operace hromadného vložení.

* Dávkování: pomocí `-batch` při přepnutí provede vložení, Sqoop provede několik vložení místo dávkování operace vložení.

## <a name="next-steps"></a>Další postup
Nyní jste se naučili, jak použít Sqoop. Další informace naleznete v tématu:

* [Použití Oozie s HDInsight](../hdinsight-use-oozie.md): použití Sqoopu akce v pracovním postupu Oozie.
* [Analýza zpoždění letů pomocí HDInsight](../hdinsight-analyze-flight-delay-data.md): použití Hive k analýze letu zpoždění dat a potom použít Sqoop k exportování dat do Azure SQL database.
* [Nahrání dat do HDInsight](../hdinsight-upload-data.md): Najít další metody pro nahrávání dat do HDInsight nebo Azure Blob storage.

