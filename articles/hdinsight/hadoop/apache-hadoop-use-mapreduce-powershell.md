---
title: Použití MapReduce a prostředí PowerShell s Apache Hadoopu – Azure HDInsight
description: Zjistěte, jak pomocí Powershellu vzdáleně spouštět úlohy MapReduce s Apache Hadoop v HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21b56d32-1785-4d44-8ae8-94467c12cfba
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 05/09/2018
ms.date: 04/15/2019
ms.author: v-yiso
ms.openlocfilehash: 29e23d5919a953566c803f2b7825a75a2993723c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129021"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Spuštění úlohy mapreduce je možné s Apache Hadoop v HDInsight pomocí Powershellu

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]



Tento dokument obsahuje příklad použití Azure Powershellu a spusťte úlohu MapReduce v Hadoop na clusteru HDInsight.

## <a id="prereq"></a>Požadavky

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* **Cluster Azure HDInsight (Hadoop v HDInsight)**

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Pracovní stanice s prostředím Azure PowerShell**.

## <a id="powershell"></a>Spustit úlohu MapReduce

Prostředí Azure PowerShell poskytuje *rutiny* , které umožňují vzdálené spouštění úloh MapReduce ve službě HDInsight. Interně, prostředí PowerShell provede volání REST pro [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (dříve se označovaly jako Templeton) spuštěná na clusteru HDInsight.

Při spuštění úlohy mapreduce je možné ve vzdáleném clusteru HDInsight se používají následující rutiny.

* **Connect-AzAccount**: Ověří prostředí Azure PowerShell ke svému předplatnému Azure.

* **New-AzHDInsightMapReduceJobDefinition**: Vytvoří novou *úlohy definice* s použitím zadaných informací MapReduce.

* **Start-AzHDInsightJob**: Odešle definice úlohy HDInsight a spustí úlohu. A *úlohy* je vrácen objekt.

* **Wait-AzHDInsightJob**: Objekt úlohy používá ke kontrole stavu úlohy. To počká, až do dokončení úlohy nebo je Překročená doba čekání.

* **Get-AzHDInsightJobOutput**: Umožňuje načíst výstup úlohy.

Následující kroky ukazují, jak tyto rutiny použít ke spuštění úlohy ve vašem clusteru HDInsight.

1. Pomocí editoru, uložte následující kód jako **mapreducejob.ps1**.

    ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount -EnvironmentName AzureChinaCloud
    }

    # Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"

    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    #NOTE: This assumes that the storage account is in the same resource
    #      group as the cluster. If it is not, change the
    #      --ResourceGroupName parameter to the group that contains storage.
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
    -ResourceGroupName $resourceGroup)[0].Value

    #Create a storage context
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey

    #Define the MapReduce job
    #NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
    # -JarFile = the JAR containing the MapReduce application
    # -ClassName = the class of the application
    # -Arguments = The input file, and the output directory
    $wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
        -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
        -ClassName "wordcount" `
        -Arguments `
            "/example/data/gutenberg/davinci.txt", `
            "/example/data/WordCountOutput"

    #Submit the job to the cluster
    Write-Host "Start the MapReduce job..." -ForegroundColor Green
    $wordCountJob = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $wordCountJobDefinition `
        -HttpCredential $creds

    #Wait for the job to complete
    Write-Host "Wait for the job to complete..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
        -Blob 'example/data/WordCountOutput/part-r-00000' `
        -Container $container `
        -Destination output.txt `
        -Context $context
    # Print the output of the job.
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds
    ```

2. Otevřete novou **prostředí Azure PowerShell** příkazového řádku. Změňte adresář na umístění **mapreducejob.ps1** souboru a potom použijte následující příkaz pro spuštění skriptu:

        .\mapreducejob.ps1

    Při spuštění skriptu se zobrazí výzva k zadání názvu clusteru HDInsight a přihlášení ke clusteru. Také můžete být vyzváni k ověření ke svému předplatnému Azure.

3. Při dokončení úlohy se zobrazí výstup podobný následujícímu textu:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Tento výstup označuje, že úloha byla úspěšně dokončena.

    > [!NOTE]
    > Pokud **ExitCode** je hodnota než 0, najdete v článku [Poradce při potížích s](#troubleshooting).

    Tento příklad také ukládá stažené soubory, které chcete **výstup.txt** souboru v adresáři, který spustí skript z.

### <a name="view-output"></a>Zobrazení výstupu

Chcete-li zobrazit slova a počty vytvořený úlohou, otevřete **výstup.txt** souboru v textovém editoru.

> [!NOTE]
> Výstupní soubory úlohy MapReduce jsou neměnné. Takže pokud znovu spustíte tento příklad, musíte změnit název výstupního souboru.

## <a id="troubleshooting"></a>Řešení potíží

Pokud žádné informace se vrátí po dokončení úlohy, zobrazení chyb pro úlohy. Chcete-li zobrazit informace o chybě pro tuto úlohu, přidejte následující příkaz na konec objektu **mapreducejob.ps1** souboru, uložit a znovu jej spusťte.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Tato rutina vrátí informace, které se zapsala do STDERR při spuštění úlohy.

## <a id="summary"></a>Shrnutí

Jak je vidět, prostředí Azure PowerShell poskytuje snadný způsob, jak na clusteru HDInsight spustí úlohy mapreduce je možné monitorovat stav úlohy a načtení výstupu.

## <a id="nextsteps"></a>Další kroky

Obecné informace o úlohy mapreduce je možné v HDInsight:

* [Použití MapReduce pro Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Informace o jiných způsobech, jakými můžete pracovat s Hadoop v HDInsight:

* [Použití Apache Hivu s Apache Hadoop v HDInsight](hdinsight-use-hive.md)
* [Použití Apache Pig s Apache Hadoop v HDInsight](hdinsight-use-pig.md)
