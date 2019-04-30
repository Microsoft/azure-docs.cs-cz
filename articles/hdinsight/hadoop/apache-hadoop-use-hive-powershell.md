---
title: Použití Apache Hivu pomocí prostředí PowerShell ve službě HDInsight – Azure
description: Spouštění dotazů Hive v Apache Hadoop v HDInsight pomocí Powershellu.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 04/23/2018
ms.date: 04/15/2019
ms.author: v-yiso
ms.openlocfilehash: 108a3e7d899eef4ca78ae7507bf4852b861e74d5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095516"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Spouštějte dotazy Apache Hive pomocí Powershellu
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Tento dokument obsahuje příklad použití Azure Powershellu v režimu skupiny prostředků Azure ke spouštění dotazů Hive v clusteru HDInsight v Apache Hadoop.

> [!NOTE]  
> Tento dokument neobsahuje podrobný popis co dělat, které se používají v příkladech příkazy HiveQL. Informace o HiveQL, který se používá v tomto příkladu najdete v tématu [použití Apache Hivu se službou Apache Hadoop v HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Apache Hadoop s linuxem v clusteru HDInsight verze 3.4 nebo vyšší.

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Klient pomocí Azure Powershellu.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Spuštění dotazu Hive

Prostředí Azure PowerShell poskytuje *rutiny* , které umožňují vzdálené spouštění dotazů Hive v HDInsight. Interně jsou rutiny volání REST [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) v clusteru HDInsight.

Při spouštění dotazů Hive ve vzdáleném clusteru HDInsight se používají následující rutiny:

* `Connect-AzAccount`: Ověří prostředí Azure PowerShell ke svému předplatnému Azure.
* `New-AzHDInsightHiveJobDefinition`: Vytvoří *úlohy definice* pomocí zadané příkazy HiveQL.
* `Start-AzHDInsightJob`: Odešle definice úlohy HDInsight a spustí úlohu. A *úlohy* je vrácen objekt.
* `Wait-AzHDInsightJob`: Objekt úlohy používá ke kontrole stavu úlohy. To počká, až do dokončení úlohy nebo je Překročená doba čekání.
* `Get-AzHDInsightJobOutput`: Umožňuje načíst výstup úlohy.
* `Invoke-AzHDInsightHiveJob`: Umožňuje spouštět příkazy HiveQL. Tato rutina bloky dotaz dokončí a vrátí výsledky.
* `Use-AzHDInsightCluster`: Nastaví aktuální cluster pro účely `Invoke-AzHDInsightHiveJob` příkazu.

Následující kroky ukazují, jak tyto rutiny použít ke spuštění úlohy ve vašem clusteru HDInsight:

1. Pomocí editoru, uložte následující kód jako `hivejob.ps1`.

    ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount -EnvironmentName AzureChinaCloud
    }

    #Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"

    #HiveQL
    #Note: set hive.execution.engine=tez; is not required for
    #      Linux-based HDInsight
    $queryString = "set hive.execution.engine=tez;" +
                "DROP TABLE log4jLogs;" +
                "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

    #Create an HDInsight Hive job definition
    $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

    #Submit the job to the cluster
    Write-Host "Start the Hive job..." -ForegroundColor Green

    $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds

    #Wait for the Hive job to complete
    Write-Host "Wait for the job to complete..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

    # Print the output
    Write-Host "Display the standard output..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $hiveJob.JobId `
        -HttpCredential $creds
    ```

2. Otevřete novou **prostředí Azure PowerShell** příkazového řádku. Změňte adresář na umístění `hivejob.ps1` souboru a potom použijte následující příkaz pro spuštění skriptu:

        .\hivejob.ps1

    Po spuštění skriptu budete vyzváni k zadání názvu clusteru a přihlašovací údaje účtu správce HTTPS/clusteru. Také můžete být vyzváni k přihlášení ke svému předplatnému Azure.

3. Po dokončení úlohy vrátit informace podobné následujícímu textu:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Jak už bylo zmíněno dříve, `Invoke-Hive` je možné spustit dotaz a čekat na odpověď. Abyste viděli, jak funguje Invoke-Hive pomocí následujícího skriptu:

    ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount -EnvironmentName AzureChinaCloud
    }

    #Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"

    # Set the cluster to use
    Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds

    $queryString = "set hive.execution.engine=tez;" +
                "DROP TABLE log4jLogs;" +
                "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/';" +
                "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
    Invoke-AzureRmHDInsightHiveJob `
        -StatusFolder "statusout" `
        -Query $queryString
    ```

    Výstup bude vypadat jako následující text:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > Pro delší HiveQL dotazy, můžete použít rutinu prostředí Azure PowerShell **tady řetězce** rutiny nebo HiveQL soubory skriptů. Následující fragment kódu ukazuje způsob použití `Invoke-Hive` rutina umožňuje spustit soubor skript HiveQL. Musí být odeslán souboru skript HiveQL do wasb: / /.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Další informace o **tady řetězce**, naleznete v tématu <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">pomocí Windows Powershellu tady řetězce</a>.

## <a name="troubleshooting"></a>Řešení potíží

Pokud po dokončení úlohy nevrátí žádné informace, podívejte se na protokoly chyb. Chcete-li zobrazit informace o chybě pro tuto úlohu, přidejte následující na konec objektu `hivejob.ps1` souboru, uložit a znovu jej spusťte.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Tato rutina vrátí informace, které jsou zapsána do STDERR během zpracování úlohy.

## <a name="summary"></a>Souhrn

Jak je vidět, prostředí Azure PowerShell poskytuje snadný způsob, jak spouštět dotazy Hive v clusteru služby HDInsight, sledovat stav úlohy a načtení výstupu.

## <a name="next-steps"></a>Další postup

Obecné informace o Hivu ve službě HDInsight:

* [Použití Apache Hivu s Apache Hadoop v HDInsight](hdinsight-use-hive.md)

Informace o jiných způsobech, jakými můžete pracovat s Hadoop v HDInsight:

* [Použití Apache Pig s Apache Hadoop v HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce se službou Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md)
