---
title: Použití Pigu Hadoop v prostředí PowerShell v HDInsight – Azure
description: Zjistěte, jak odesílat úlohy Pig do clusterů Hadoop v HDInsight pomocí Azure Powershellu.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: e50c551381a065e8651ab7970ef9cf18c6c3a765
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011013"
---
# <a name="use-azure-powershell-to-run-pig-jobs-with-hdinsight"></a>Spuštění úlohy Pig s HDInsight pomocí Azure Powershellu

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Tento dokument obsahuje příklad použití Azure Powershellu pro odeslání úlohy Pig ke clusteru Hadoop v clusteru HDInsight. Pig umožňuje zapisovat úlohy mapreduce je možné pomocí jazyka (Pig Latin) této transformace dat modely, spíše než mapovací a redukční funkce.

> [!NOTE]
> Tento dokument neobsahuje podrobný popis co dělat v příkladech používají příkazy Pig Latin. Informace o Pig Latin použitý v tomto příkladu najdete v tématu [použití Pigu se systémem Hadoop v HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Požadavky

* **Cluster Azure HDInsight**

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Pracovní stanice s prostředím Azure PowerShell**.

## <a id="powershell"></a>Spuštění úlohy Pig

Prostředí Azure PowerShell poskytuje *rutiny* , které umožňují vzdáleně spouštět úlohy Pig v HDInsight. Interně, prostředí PowerShell používá volání REST pro [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) spuštěné v clusteru HDInsight.

Při spuštění úlohy Pig ve vzdáleném clusteru HDInsight se používají následující rutiny:

* **Connect-AzureRmAccount**: ověřuje prostředí Azure PowerShell ke svému předplatnému Azure.
* **Nové AzureRmHDInsightPigJobDefinition**: vytvoří *úlohy definice* pomocí zadané příkazy Pig Latin.
* **Start-AzureRmHDInsightJob**: odešle definice úlohy HDInsight a spustí úlohu. A *úlohy* je vrácen objekt.
* **Čekání AzureRmHDInsightJob**: používá objekt úlohy a zkontrolujte stav úlohy. To počká, až úloha dokončí, nebo byla překročena doba čekání.
* **Get-AzureRmHDInsightJobOutput**: používá se k načtení výstupu úlohy.

Následující kroky ukazují, jak tyto rutiny použít ke spuštění úlohy ve vašem clusteru HDInsight.

1. Pomocí editoru, uložte následující kód jako **pigjob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Otevřete nový příkazový řádek Windows Powershellu. Změňte adresář na umístění **pigjob.ps1** souboru a potom použijte následující příkaz pro spuštění skriptu:

        .\pigjob.ps1

    Zobrazí se výzva k přihlášení ke svému předplatnému Azure. Potom budete vyzváni k zadání název účtu HTTPs/Admin a heslo pro HDInsight cluster.

2. Po dokončení úlohy, měla by vrátit informace podobné následujícímu textu:

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="troubleshooting"></a>Řešení potíží

Pokud po dokončení úlohy nevrátí žádné informace, podívejte se na protokoly chyb. Chcete-li zobrazit informace o chybě pro tuto úlohu, přidejte následující příkaz na konec objektu **pigjob.ps1** souboru, uložit a znovu jej spusťte.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Tato rutina vrátí informace, které se zapsala do STDERR během zpracování úlohy.

## <a id="summary"></a>Shrnutí
Jak je vidět, prostředí Azure PowerShell poskytuje snadný způsob, jak na clusteru HDInsight spustí úlohy Pig, sledovat stav úlohy a načtení výstupu.

## <a id="nextsteps"></a>Další kroky
Obecné informace o Pig v HDInsight:

* [Použití Pigu se systémem Hadoop v HDInsight](hdinsight-use-pig.md)

Informace o jiných způsobech, jakými můžete pracovat s Hadoop v HDInsight:

* [Použití Hivu s Hadoopem v HDInsight](hdinsight-use-hive.md)
* [Použití MapReduce se systémem Hadoop v HDInsight](hdinsight-use-mapreduce.md)
