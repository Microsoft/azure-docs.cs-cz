---
title: Použití MapReduce a PowerShellu s Apache Hadoop – Azure HDInsight
description: Naučte se, jak pomocí PowerShellu vzdáleně spouštět úlohy MapReduce s Apache Hadoop v HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: 8b5f5a9fb8ddd25f750b19044bac7253bbff2f98
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545204"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Spuštění úloh MapReduce s využitím Apache Hadoop ve službě HDInsight pomocí PowerShellu

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Tento dokument poskytuje příklad použití Azure PowerShell ke spuštění úlohy MapReduce v clusteru Hadoop v HDInsight.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Hadoop v HDInsight. Další informace najdete v tématu [Vytvoření clusterů Apache Hadoop pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

* Prostředí PowerShell [AZ Module](/powershell/azure/) installed.

## <a name="run-a-mapreduce-job"></a>Spuštění úlohy MapReduce

Azure PowerShell poskytuje *rutiny* , které umožňují vzdáleně spouštět úlohy MapReduce ve službě HDInsight. V interním prostředí PowerShell provádí volání REST do [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (dříve nazývané Templeton) spuštěného v clusteru HDInsight.

Při spouštění úloh MapReduce ve vzdáleném clusteru HDInsight se používají následující rutiny.

|Rutina | Popis |
|---|---|
|Connect-AzAccount|Ověří Azure PowerShell předplatného Azure.|
|New-AzHDInsightMapReduceJobDefinition|Vytvoří novou *definici úlohy* pomocí zadaných informací MapReduce.|
|Start-AzHDInsightJob|Odešle definici úlohy do HDInsight a spustí úlohu. Vrátí se objekt *úlohy* .|
|Wait-AzHDInsightJob|Pomocí objektu úlohy zkontroluje stav úlohy. Čeká na dokončení úlohy nebo překročení doby čekání.|
|Get-AzHDInsightJobOutput|Slouží k načtení výstupu úlohy.|

Následující kroky ukazují, jak pomocí těchto rutin spustit úlohu v clusteru HDInsight.

1. Pomocí editoru uložte následující kód jako **mapreducejob.ps1** .

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Otevřete nový příkazový řádek **Azure PowerShell** . Změňte adresáře na umístění souboru **mapreducejob.ps1** a potom spusťte skript pomocí následujícího příkazu:

    ```azurepowershell
    .\mapreducejob.ps1
    ```

    Po spuštění skriptu budete vyzváni k zadání názvu clusteru HDInsight a přihlášení ke clusteru. Můžete být také vyzváni k ověření předplatného Azure.

3. Po dokončení úlohy se zobrazí výstup podobný následujícímu textu:

    ```output
    Cluster         : CLUSTERNAME
    ExitCode        : 0
    Name            : wordcount
    PercentComplete : map 100% reduce 100%
    Query           :
    State           : Completed
    StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
    SubmissionTime  : 12/5/2014 8:34:09 PM
    JobId           : job_1415949758166_0071
    ```

    Tento výstup indikuje, že se úloha úspěšně dokončila.

    > [!NOTE]  
    > Pokud je **ExitCode** hodnotou jinou než 0, přečtěte si téma [řešení potíží](#troubleshooting).

    Tento příklad také ukládá stažené soubory do souboru **output.txt** v adresáři, ze kterého spouštíte skript.

### <a name="view-output"></a>Zobrazit výstup

Chcete-li zobrazit slova a počty vytvářené úlohou, otevřete soubor **output.txt** v textovém editoru.

> [!NOTE]  
> Výstupní soubory úlohy MapReduce jsou neměnné. Takže pokud tuto ukázku znovu spustíte, musíte změnit název výstupního souboru.

## <a name="troubleshooting"></a>Řešení potíží

Pokud se po dokončení úlohy nevrátí žádné informace, zobrazte chyby úlohy. Chcete-li zobrazit informace o chybě pro tuto úlohu, přidejte na konec souboru **mapreducejob.ps1** následující příkaz. Pak soubor uložte a znovu spusťte skript.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Tato rutina vrátí informace, které byly při spuštění úlohy zapsány do STDERR.

## <a name="next-steps"></a>Další kroky

Jak vidíte, Azure PowerShell poskytuje snadný způsob, jak spouštět úlohy MapReduce v clusteru HDInsight, monitorovat stav úlohy a načíst výstup. Informace o dalších způsobech, jak můžete pracovat se systémem Hadoop ve službě HDInsight:

* [Použití MapReduce v HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Použití Apache Hive s Apache Hadoop v HDInsight](hdinsight-use-hive.md)