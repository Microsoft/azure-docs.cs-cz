---
title: Použijte Hadoop Pig v prostředí PowerShell v HDInsight - Azure | Microsoft Docs
description: Zjistěte, jak úlohy Pig do clusteru Hadoop v HDInsight pomocí prostředí Azure PowerShell.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 737089c1-b494-4387-9def-7b4dac3be532
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: a3e62647ec41cfdfc7f0f7bb55474215ab435ee8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="use-azure-powershell-to-run-pig-jobs-with-hdinsight"></a>Pomocí prostředí Azure PowerShell ke spuštění úlohy Pig s HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Tento dokument poskytuje příklad použití Azure PowerShell k odesílání úloh Pig do Hadoop v clusteru HDInsight. Pig umožňuje zapsat úloh MapReduce pomocí jazyka (Pig Latin) této transformace dat modely, nikoli mapování a snížit funkce.

> [!NOTE]
> Tento dokument neposkytuje podrobný popis co dělat, Pig Latin příkazy použít v příkladech. Informace o Pig Latin použité v tomto příkladu najdete v tématu [použijte Pig s Hadoop v HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Požadavky

* **Cluster Azure HDInsight**

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Pracovní stanice s prostředím Azure PowerShell**.

## <a id="powershell"></a>Spustit úlohu Pig

Prostředí Azure PowerShell poskytuje *rutiny* které umožňují vzdáleně spouštět úlohy Pig v HDInsight. Interně, prostředí PowerShell používá volání REST [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) běžící v clusteru HDInsight.

Při spuštění úlohy Pig na vzdálený cluster HDInsight, se používají následující rutiny:

* **Připojit AzureRmAccount**: ověřuje prostředí Azure PowerShell k předplatnému Azure.
* **Nové AzureRmHDInsightPigJobDefinition**: vytvoří *úlohy definice* pomocí zadané příkazy Pig Latin.
* **Spuštění AzureRmHDInsightJob**: odešle definici úlohy do HDInsight a spuštění úlohy. A *úlohy* se vrátí objekt.
* **Počkejte AzureRmHDInsightJob**: používá objekt úlohy a zkontrolujte stav úlohy. Se čeká na dokončení úlohy nebo byla překročena doba čekání.
* **Get-AzureRmHDInsightJobOutput**: používá se k načtení výstup úlohy.

Následující kroky ukazují, jak tyto rutiny použít ke spuštění úlohy v clusteru HDInsight.

1. Pomocí editoru, uložte následující kód jako **pigjob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Otevřete nový příkazový řádek prostředí Windows PowerShell. Změňte adresáře na umístění **pigjob.ps1** souboru a potom použijte následující příkaz pro spuštění skriptu:

        .\pigjob.ps1

    Zobrazí se výzva k přihlášení k předplatnému Azure. Potom budete vyzváni k HTTPs nebo správce název účtu a hesla pro HDInsight cluster.

2. Po dokončení úlohy, měla by vrátit informace podobná následující text:

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

Pokud žádné informace se vrátí po dokončení úlohy, zobrazte protokoly chyb. Chcete-li zobrazit informace o chybě pro tuto úlohu, přidejte následující příkaz na konec **pigjob.ps1** souboru, uložit jej a znovu jej spusťte.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Tato rutina vrátí informace, které byla zapsána do STDERR během zpracování úlohy.

## <a id="summary"></a>Shrnutí
Jak vidíte, Azure PowerShell poskytuje snadný způsob, jak spouštět úlohy Pig v clusteru HDInsight, sledovat stav úlohy a načíst výstup.

## <a id="nextsteps"></a>Další kroky
Obecné informace o Pig v HDInsight:

* [Použijte Pig s Hadoop v HDInsight](hdinsight-use-pig.md)

Informace o jiných způsobech můžete pracovat s Hadoop v HDInsight:

* [Použijte Hive s Hadoop v HDInsight](hdinsight-use-hive.md)
* [Používání nástroje MapReduce s Hadoop v HDInsight](hdinsight-use-mapreduce.md)
