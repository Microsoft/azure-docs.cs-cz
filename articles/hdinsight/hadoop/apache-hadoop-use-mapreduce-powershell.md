---
title: Používání MapReduce a prostředí PowerShell s Hadoop - Azure HDInsight | Microsoft Docs
description: Zjistěte, jak pomocí prostředí PowerShell vzdáleně spouštět úlohy MapReduce s Hadoop v HDInsight.
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
ms.date: 02/16/2018
ms.author: larryfr
ms.openlocfilehash: b6e68b4c93e6385367b5aab01bebe1d29b7b9d13
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Spuštění úloh MapReduce s Hadoop v HDInsight pomocí prostředí PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Tento dokument poskytuje příklad použití Azure PowerShell a spusťte úlohu MapReduce v Hadoop na clusteru HDInsight.

## <a id="prereq"></a>Požadavky

* **Cluster Azure HDInsight (Hadoop v HDInsight)**

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Pracovní stanice s prostředím Azure PowerShell**.

## <a id="powershell"></a>Spustit úlohu MapReduce

Prostředí Azure PowerShell poskytuje *rutiny* které umožňují vzdáleně spouštět úlohy MapReduce v HDInsight. Interně, prostředí PowerShell provádí volání REST na [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (dříve se označovaly jako Templeton) spuštěná na clusteru HDInsight.

Při spouštění úloh MapReduce v vzdáleného clusteru HDInsight, se používají následující rutiny.

* **Login-AzureRmAccount**: ověřuje prostředí Azure PowerShell k předplatnému Azure.

* **Nové AzureRmHDInsightMapReduceJobDefinition**: vytvoří novou *úlohy definice* pomocí zadané informace o MapReduce.

* **Spuštění AzureRmHDInsightJob**: odešle definici úlohy do HDInsight a spuštění úlohy. A *úlohy* se vrátí objekt.

* **Počkejte AzureRmHDInsightJob**: používá objekt úlohy a zkontrolujte stav úlohy. Se čeká na dokončení úlohy nebo je překročena doba čekání.

* **Get-AzureRmHDInsightJobOutput**: používá se k načtení výstup úlohy.

Následující kroky ukazují, jak tyto rutiny použít ke spuštění úlohy v clusteru HDInsight.

1. Pomocí editoru, uložte následující kód jako **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Otevřete nový **prostředí Azure PowerShell** příkazového řádku. Změňte adresáře na umístění **mapreducejob.ps1** souboru a potom použijte následující příkaz pro spuštění skriptu:

        .\mapreducejob.ps1

    Když spustíte skript, budete vyzváni k názvu clusteru HDInsight a přihlášení clusteru. Také můžete být vyzváni k ověření vašeho předplatného Azure.

3. Po dokončení úlohy, zobrazí se výstup podobný následujícímu:

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
    > Pokud **ExitCode** je hodnota než 0, najdete v části [Poradce při potížích s](#troubleshooting).

    Tento příklad také ukládá stažené soubory, které chcete **výstup.txt** soubor v adresáři, který spuštění skriptu z.

### <a name="view-output"></a>Zobrazení výstupu

Chcete-li zobrazit slova a počty vytvořeného úlohou, otevřete **výstup.txt** soubor v textovém editoru.

> [!NOTE]
> Výstupní soubory úlohy MapReduce jsou neměnné. Proto pokud tuto ukázku, musíte změnit název výstupního souboru.

## <a id="troubleshooting"></a>Řešení potíží

Pokud žádné informace se vrátí po dokončení úlohy, zobrazení chyb pro úlohu. Chcete-li zobrazit informace o chybě pro tuto úlohu, přidejte následující příkaz na konec **mapreducejob.ps1** souboru, uložit jej a znovu jej spusťte.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Tato rutina vrátí informace, které byl zapsán do STDERR, jak má být úloha spuštěna.

## <a id="summary"></a>Shrnutí

Jak vidíte, Azure PowerShell poskytuje snadný způsob, jak spouštět úlohy MapReduce v clusteru HDInsight, sledovat stav úlohy a načíst výstup.

## <a id="nextsteps"></a>Další kroky

Obecné informace o úloh MapReduce v HDInsight:

* [Používání nástroje MapReduce systému HDInsight Hadoop](hdinsight-use-mapreduce.md)

Informace o jiných způsobech můžete pracovat s Hadoop v HDInsight:

* [Použijte Hive s Hadoop v HDInsight](hdinsight-use-hive.md)
* [Použijte Pig s Hadoop v HDInsight](hdinsight-use-pig.md)
