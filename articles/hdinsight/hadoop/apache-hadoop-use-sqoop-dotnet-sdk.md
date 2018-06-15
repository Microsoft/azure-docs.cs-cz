---
title: Pomocí rozhraní .NET a HDInsight - Azure spustíte úlohy Sqoop | Microsoft Docs
description: Naučte se používat sadu .NET SDK HDInsight Sqoop import a export mezi clusteru Hadoop a Azure SQL database.
keywords: sqoop úlohy
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
ms.assetid: 87bacd13-7775-4b71-91da-161cb6224a96
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 818e4aca63249c7a1543abe146e0691e993e9e80
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200295"
---
# <a name="run-sqoop-jobs-by-using-net-sdk-for-hadoop-in-hdinsight"></a>Spuštění úloh Sqoop pomocí .NET SDK pro Hadoop v HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Další informace o použití .NET SDK služby Azure HDInsight ke spuštění úloh Sqoop v HDInsight k importu a exportu mezi clusteru služby HDInsight a databázi Azure SQL nebo databáze systému SQL Server.

> [!NOTE]
> Přestože je možné použít postupy v tomto článku se buď cluster HDInsight se systémem Linux nebo systému Windows, nebudou fungovat pouze z klienta Windows. Zvolte jiné metody, pomocí volič karty v horní části tohoto článku.
> 

## <a name="prerequisites"></a>Požadavky
Než začnete tento kurz, musíte mít následující položky:

* Cluster Hadoop v HDInsight. Další informace najdete v tématu [vytvoření clusteru s podporou a databázi SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Použití nástroje Sqoop clustery prostředí HDInsight pomocí .NET SDK
.NET SDK služby HDInsight poskytuje klientské knihovny .NET, tak, aby bylo jednodušší pro práci s clustery HDInsight pomocí technologie .NET. V této části vytvoříte konzolovou aplikaci C# pro export hivesampletable do tabulky Azure SQL Database, který jste vytvořili dříve v tomto kurzu.

## <a name="submit-a-sqoop-job"></a>Odeslání úlohy Sqoop

1. Vytvořte konzolovou aplikaci C# v sadě Visual Studio.

2. Z konzoly Správce balíčků Visual Studio importujte balíček tak, že spustíte příkaz NuGet:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job

3. Použijte následující kód v souboru Program.cs:
   
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
HDInsight se systémem Linux představuje následující omezení:

* Hromadné exportu: Sqoop konektor, který slouží k exportu dat Microsoft SQL Server nebo Azure SQL Database aktuálně nepodporuje hromadné vložení.

* Dávkování: pomocí `-batch` přepínače, když provádí vložení, Sqoop provádí více vloží místo dávkování operace insert.

## <a name="next-steps"></a>Další postup
Nyní jste se naučili postup použití nástroje Sqoop. Další informace naleznete v tématu:

* [Použijte Oozie s HDInsight](../hdinsight-use-oozie.md): použití Sqoop akce v pracovním postupu Oozie.
* [Analýza dat zpoždění letu pomocí HDInsight](../hdinsight-analyze-flight-delay-data.md): použití Hive k analýze letu zpoždění dat a pak pomocí Sqoop exportovat data do Azure SQL database.
* [Nahrání dat do HDInsight](../hdinsight-upload-data.md): Najít další metody pro odesílání dat do HDInsight nebo Azure Blob storage.

