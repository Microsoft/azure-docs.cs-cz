---
title: Spouštění úloh Apache Sqoop pomocí rozhraní .NET a HDInsight – Azure
description: Přečtěte si, jak pomocí sady HDInsight .NET SDK spouštět import a export Apache Sqoop mezi clusterem Apache Hadoop a databází Azure SQL Database.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/14/2020
ms.openlocfilehash: f0f767273a40bc91b1d49477c896b0b157623106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157060"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Spuštění úloh Apache Sqoop pomocí sady .NET SDK pro Apache Hadoop v HDInsightu

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Naučte se používat Azure HDInsight .NET SDK ke spuštění úloh Apache Sqoop v HDInsight u importu a exportu mezi clusterem HDInsight a databází Azure SQL Database nebo SQL Serveru.

## <a name="prerequisites"></a>Požadavky

* Dokončení [Nastavení testovacího prostředí](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) z [use Apache Sqoop s Hadoopem v HDInsightu](./hdinsight-use-sqoop.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* Obeznámenost s Sqoop. Další informace naleznete v [uživatelské příručce společnosti Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Použití sqoopu v clusterech HDInsight s sadou .NET SDK

Sada HDInsight .NET SDK poskytuje klientské knihovny .NET, takže je snazší pracovat s clustery HDInsight z rozhraní .NET. V této části vytvoříte konzolovou aplikaci `hivesampletable` Jazyka C# pro export tabulky Azure SQL Database, kterou jste vytvořili z předpokladů.

## <a name="set-up"></a>Nastavit

1. Spusťte Visual Studio a vytvořte konzolovou aplikaci Jazyka C#.

1. Přejděte na **tools** > **NuGet Package Manager** > **Console** a spusťte následující příkaz:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Vývoz Sqoop

Z Hive na SQL Server.  Tento příklad exportuje `hivesampletable` data z `mobiledata` tabulky Hive do tabulky v databázi SQL.

1. V souboru Program.cs použijte následující kód. Upravte kód a nastavte `ExistingClusterName`hodnoty `ExistingClusterPassword`pro písmena a) a .

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
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
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. Chcete-li program spustit, vyberte klávesu **F5.**

## <a name="sqoop-import"></a>Import Sqoop

Z SQL Serveru do úložiště Azure. Tento příklad závisí na provedeném exportu výše.  Tento příklad importuje `mobiledata` data z tabulky `wasb:///tutorials/usesqoop/importeddata` v databázi SQL do adresáře ve výchozím účtu úložiště clusteru.

1. Nahraďte výše `//sqoop start //sqoop end` uvedený kód v bloku následujícím kódem:

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. Chcete-li program spustit, vyberte klávesu **F5.**

## <a name="limitations"></a>Omezení

HdInsight založený na Linuxu představuje následující omezení:

* Hromadný export: Konektor Sqoop, který se používá k exportu dat na Microsoft SQL Server nebo Azure SQL Database, momentálně nepodporuje hromadné vložení.

* Dávkování: Pomocí přepínače `-batch` Sqoop provádí více břitových destiček namísto dávkování operace vložení.

## <a name="next-steps"></a>Další kroky

Teď jste se naučili používat Sqoop. Další informace naleznete v tématu:

* [Použijte Apache Oozie s HDInsight:](../hdinsight-use-oozie-linux-mac.md)Použijte akci Sqoop v pracovním postupu Oozie.
* [Nahrávání dat do HDInsightu](../hdinsight-upload-data.md): Najděte další metody nahrávání dat do HDInsightu nebo úložiště objektů Blob Azure.
