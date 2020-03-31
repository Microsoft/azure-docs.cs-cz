---
title: Použití MapReduce a PowerShellu s Apache Hadoop - Azure HDInsight
description: Naučte se používat PowerShell ke vzdálenému spouštění úloh MapReduce s Apache Hadoop na HDInsightu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: b3c1abb7bff54e3e2d294b073b867c6c0e06f482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75830067"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Spuštění úloh MapReduce s Apache Hadoop na HDInsight pomocí PowerShellu

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Tento dokument poskytuje příklad použití Azure PowerShell u spuštění úlohy MapReduce v clusteru Hadoop na HDInsight.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Hadoop na HDInsight. Viz [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

* Nainstalován [modul Az prostředí](https://docs.microsoft.com/powershell/azure/overview) PowerShell.

## <a name="run-a-mapreduce-job"></a>Spuštění úlohy MapReduce

Azure PowerShell poskytuje *rutiny,* které vám umožní vzdáleně spouštět úlohy MapReduce na HDInsight. Interně PowerShell provádí volání REST na [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (dříve nazývaný Templeton) spuštěný v clusteru HDInsight.

Následující rutiny se používají při spuštění mapreduce úloh ve vzdáleném clusteru HDInsight.

|Rutina | Popis |
|---|---|
|Účet Connect-Az|Ověřuje Azure PowerShell do vašeho předplatného Azure.|
|Nová-AzHDInsightMapReduceJobDefinition|Vytvoří novou *definici úlohy* pomocí zadaných informací MapReduce.|
|Start-AzHDInsightJob|Odešle definici úlohy do HDInsight a spustí úlohu. Je vrácen objekt *úlohy.*|
|Wait-AzHDInsightJob|Používá objekt úlohy ke kontrole stavu úlohy. Čeká na dokončení úlohy nebo na překročení čekací doby.|
|Get-AzHDInsightJobOutput|Slouží k načtení výstupu úlohy.|

Následující kroky ukazují, jak pomocí těchto rutin spustit úlohu v clusteru HDInsight.

1. Pomocí editoru uložte následující kód jako **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Otevřete nový příkazový řádek **Azure PowerShellu.** Změňte adresáře na umístění souboru **mapreducejob.ps1** a potom pomocí následujícího příkazu spusťte skript:

        .\mapreducejob.ps1

    Při spuštění skriptu budete vyzváni k zadání názvu clusteru HDInsight a přihlášení clusteru. Můžete být také vyzváni k ověření vašeho předplatného Azure.

3. Po dokončení úlohy obdržíte výstup podobný následujícímu textu:

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
    > Pokud **exitcode** je hodnota jiná než 0, naleznete [v tématu Řešení potíží](#troubleshooting).

    Tento příklad také ukládá stažené soubory do souboru **output.txt** v adresáři, ze kterého skript spouštěte.

### <a name="view-output"></a>Zobrazit výstup

Chcete-li zobrazit slova a počty vytvořené úlohou, otevřete soubor **output.txt** v textovém editoru.

> [!NOTE]  
> Výstupní soubory úlohy MapReduce jsou neměnné. Pokud tedy znovu spustíte tuto ukázku, je třeba změnit název výstupního souboru.

## <a name="troubleshooting"></a>Řešení potíží

Pokud po dokončení úlohy nejsou vráceny žádné informace, zobrazte chyby úlohy. Chcete-li zobrazit informace o chybě pro tuto úlohu, přidejte na konec souboru **mapreducejob.ps1** následující příkaz. Potom soubor uložte a skript znovu spusťte.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Tato rutina vrátí informace, které byly zapsány do STDERR při spuštění úlohy.

## <a name="next-steps"></a>Další kroky

Jak můžete vidět, Azure PowerShell poskytuje snadný způsob, jak spustit MapReduce úlohy v clusteru HDInsight, sledovat stav úlohy a načíst výstup. Informace o dalších způsobech práce s Hadoopem na HDInsightu:

* [Použití mapreduce na HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Použití Apache Hive s Apache Hadoop na HDInsight](hdinsight-use-hive.md)
