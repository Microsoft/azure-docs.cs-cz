---
title: Použití MapReduce a prostředí PowerShell s Apache Hadoopu – Azure HDInsight
description: Zjistěte, jak pomocí Powershellu vzdáleně spouštět úlohy MapReduce s Apache Hadoop v HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: hrasheed
ms.openlocfilehash: 6f1620c9977f997b4037fbf3f823c429e43b4f6a
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436258"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Spuštění úlohy mapreduce je možné s Apache Hadoop v HDInsight pomocí Powershellu

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

* **Connect-AzureRmAccount**: Ověří prostředí Azure PowerShell ke svému předplatnému Azure.

* **Nové AzureRmHDInsightMapReduceJobDefinition**: Vytvoří novou *úlohy definice* s použitím zadaných informací MapReduce.

* **Start-AzureRmHDInsightJob**: Odešle definice úlohy HDInsight a spustí úlohu. A *úlohy* je vrácen objekt.

* **Čekání AzureRmHDInsightJob**: Objekt úlohy používá ke kontrole stavu úlohy. To počká, až do dokončení úlohy nebo je Překročená doba čekání.

* **Get-AzureRmHDInsightJobOutput**: Umožňuje načíst výstup úlohy.

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

* [Použití Apache Hivu s Apache Hadoop v HDInsight](hdinsight-use-hive.md)
* [Použití Apache Pig s Apache Hadoop v HDInsight](hdinsight-use-pig.md)
