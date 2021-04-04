---
title: Spouštění úloh Apache Sqoop pomocí .NET a HDInsight – Azure
description: Naučte se používat sadu HDInsight .NET SDK ke spouštění služby Sqoop pro import a export mezi Apache Hadoopm a Azure SQL Database.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, hdiseo17may2017, devx-track-csharp
ms.date: 01/14/2020
ms.openlocfilehash: a06326f5b4cf34a06475c10e368c6dcbb49ca9e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98928344"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Spouštění úloh Apache Sqoop pomocí sady .NET SDK pro Apache Hadoop ve službě HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Naučte se používat sadu Azure HDInsight .NET SDK ke spouštění úloh Apache Sqoop ve službě HDInsight k importu a exportu mezi clusterem HDInsight a databází Azure SQL Database nebo SQL Server.

## <a name="prerequisites"></a>Požadavky

* Dokončení [Nastavení testovacího prostředí](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) [pro použití Apache Sqoop se systémem Hadoop ve službě HDInsight](./hdinsight-use-sqoop.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* Seznamte se se znalostí pro Sqoop. Další informace najdete v tématu [uživatelská příručka pro Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Použití Sqoop v clusterech HDInsight se sadou .NET SDK

Sada HDInsight .NET SDK poskytuje klientské knihovny .NET, takže je snazší pracovat s clustery HDInsight z .NET. V této části vytvoříte konzolovou aplikaci v jazyce C#, která bude exportována `hivesampletable` do Azure SQL Database tabulky, kterou jste vytvořili z požadovaných součástí.

## <a name="set-up"></a>Nastavení

1. Spusťte Visual Studio a vytvořte konzolovou aplikaci v jazyce C#.

1. Přejděte do **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků** a spusťte následující příkaz:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Export Sqoop

Z podregistru do SQL Server.  Tento příklad exportuje data z tabulky podregistru `hivesampletable` do `mobiledata` tabulky v SQL Database.

1. Použijte následující kód v souboru program. cs. Upravte kód a nastavte hodnoty pro `ExistingClusterName` , a `ExistingClusterPassword` .

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

1. Chcete-li program spustit, vyberte klávesu **F5** .

## <a name="sqoop-import"></a>Import Sqoop

Z SQL Server Azure Storage. Tento příklad závisí na výše provedeném exportu.  Tento příklad importuje data z `mobiledata` tabulky v SQL Database do `wasb:///tutorials/usesqoop/importeddata` adresáře na výchozím účtu úložiště v clusteru.

1. Nahraďte kód uvedený výše v `//sqoop start //sqoop end` bloku následujícím kódem:

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. Chcete-li program spustit, vyberte klávesu **F5** .

## <a name="limitations"></a>Omezení

HDInsight se systémem Linux nabízí tato omezení:

* Hromadný export: konektor Sqoop, který se používá k exportu dat do Microsoft SQL Server nebo Azure SQL Database v současné době nepodporuje hromadné vložení.

* Dávkování: pomocí `-batch` přepínače Sqoop provede vícenásobné vkládání místo dávkování operací vložení.

## <a name="next-steps"></a>Další kroky

Nyní jste se naučili, jak používat Sqoop. Další informace najdete v následujících tématech:

* [Použití Apache Oozie se službou HDInsight](../hdinsight-use-oozie-linux-mac.md): použijte akci Sqoop v pracovním postupu Oozie.
* [Nahrávání dat do HDInsight](../hdinsight-upload-data.md): Najděte další metody pro nahrávání dat do služby HDInsight nebo Azure Blob Storage.
