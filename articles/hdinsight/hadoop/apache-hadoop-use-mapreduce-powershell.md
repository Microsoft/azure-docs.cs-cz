---
title: Použití MapReduce a prostředí PowerShell se systémem Hadoop – Azure HDInsight
description: Zjistěte, jak pro vzdáleně spouštět úlohy MapReduce s Hadoop v HDInsight pomocí Powershellu.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: jasonh
ms.openlocfilehash: 5f2b6f11a611446ca13dff074b36a4040fb27d62
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43049042"
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Spuštění úloh MapReduce s Hadoop v HDInsight pomocí Powershellu

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Tento dokument obsahuje příklad použití Azure Powershellu a spusťte úlohu MapReduce v Hadoop na clusteru HDInsight.

## <a id="prereq"></a>Požadavky

* **Cluster Azure HDInsight (Hadoop v HDInsight)**

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Pracovní stanice s prostředím Azure PowerShell**.

## <a id="powershell"></a>Spustit úlohu MapReduce

Prostředí Azure PowerShell poskytuje *rutiny* , které umožňují vzdálené spouštění úloh MapReduce ve službě HDInsight. Interně, prostředí PowerShell provede volání REST pro [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (dříve se označovaly jako Templeton) spuštěná na clusteru HDInsight.

Při spuštění úlohy mapreduce je možné ve vzdáleném clusteru HDInsight se používají následující rutiny.

* **Connect-AzureRmAccount**: ověřuje prostředí Azure PowerShell ke svému předplatnému Azure.

* **Nové AzureRmHDInsightMapReduceJobDefinition**: vytvoří nový *úlohy definice* s použitím zadaných informací MapReduce.

* **Start-AzureRmHDInsightJob**: odešle definice úlohy HDInsight a spustí úlohu. A *úlohy* je vrácen objekt.

* **Čekání AzureRmHDInsightJob**: používá objekt úlohy a zkontrolujte stav úlohy. To počká, až do dokončení úlohy nebo je Překročená doba čekání.

* **Get-AzureRmHDInsightJobOutput**: používá se k načtení výstupu úlohy.

Následující kroky ukazují, jak tyto rutiny použít ke spuštění úlohy ve vašem clusteru HDInsight.

1. Pomocí editoru, uložte následující kód jako **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

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
Get-AzureRmHDInsightJobOutput `
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

* [Použití Hivu s Hadoopem v HDInsight](hdinsight-use-hive.md)
* [Použití Pigu se systémem Hadoop v HDInsight](hdinsight-use-pig.md)
