---
title: Spouštět úlohy Apache Pig s .NET SDK pro Hadoop – Azure HDInsight
description: Další informace o použití sady .NET SDK pro Hadoop odesílat úlohy Pig ke clusteru Hadoop v HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: hrasheed
ms.openlocfilehash: 0aa45ae807f153e6f1a3fde1d648571b29802dc2
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632471"
---
# <a name="run-apache-pig-jobs-using-the-net-sdk-for-apache-hadoop-in-hdinsight"></a>Apache Pig úlohy spouštět pomocí sady .NET SDK pro Apache Hadoop v HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Zjistěte, jak odesílat úlohy Apache Pig ke clusteru Hadoop v Azure HDInsight pomocí sady .NET SDK pro Apache Hadoop.

Sady HDInsight .NET SDK obsahuje klientské knihovny .NET, které usnadňuje práci s clustery HDInsight z .NET. Pig umožňuje vytvoření MapReduce operací pomocí řady transformací dat modelování. V tomto dokumentu se dozvíte, jak používat základní aplikaci v C# pro odeslání úlohy Pig do clusteru HDInsight.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku, budete potřebovat.

* Cluster Azure HDInsight (Hadoop v HDInsight) (buď Windows nebo Linuxem).

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Visual Studio 2012, 2013, 2015 nebo 2017.

## <a name="create-the-application"></a>Vytvoření aplikace

Sady HDInsight .NET SDK obsahuje klientské knihovny .NET, která usnadňuje práci s clustery HDInsight z .NET.

1. Z **souboru** nabídky v sadě Visual Studio, vyberte **nový** a pak vyberte **projektu**.

2. Nový projekt zadejte nebo vyberte následující hodnoty:

   | Vlastnost | Hodnota |
   | ------ | ------ |
   | Kategorie | Šablony/Visual C#/Windows |
   | Šablona | Konzolová aplikace |
   | Název | SubmitPigJob |

3. Projekt vytvoříte kliknutím na **OK**.

4. Z **nástroje** nabídce vyberte možnost **Správce balíčků knihoven** nebo **Správce balíčků NuGet**a pak vyberte **Konzola správce balíčků**.

5. K instalaci balíčků sady .NET SDK, použijte následující příkaz:

        Install-Package Microsoft.Azure.Management.HDInsight.Job

6. V Průzkumníku řešení poklikejte na **Program.cs** ho otevřete. Nahraďte stávající kód následujícím kódem.

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitPigJob
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

                SubmitPigJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitPigJob()
            {
                var parameters = new PigJobSubmissionParameters
                {
                    Query = @"LOGS = LOAD '/example/data/sample.log';
                                LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                RESULT = order FREQUENCIES by COUNT desc;
                                DUMP RESULT;"
                };

                System.Console.WriteLine("Submitting the Pig job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

7. Chcete-li spustit aplikaci, stiskněte **F5**.

8. Chcete-li aplikaci ukončit, stiskněte **ENTER**.

## <a name="next-steps"></a>Další postup

Informace o Pig v HDInsight najdete v tématu [použití Pigu se systémem Hadoop v HDInsight](hdinsight-use-pig.md).

Další informace o použití systému Hadoop v HDInsight najdete v následujících dokumentech:

* [Použití Hivu s Hadoopem v HDInsight](hdinsight-use-hive.md)
* [Použití MapReduce se systémem Hadoop v HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/
