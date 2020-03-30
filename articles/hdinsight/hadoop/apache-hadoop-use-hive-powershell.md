---
title: Použití Apache Hive s PowerShellem v HDInsightu – Azure
description: Použití PowerShellu ke spouštění dotazů Apache Hive v Apache Hadoop v Azure HDInsightu
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: deaa934b257fab74830d75e308a283e7608dc590
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552589"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Spuštění dotazů Apache Hive pomocí Prostředí PowerShell

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Tento dokument obsahuje příklad použití Azure PowerShellu ke spuštění dotazů Apache Hive v clusteru Apache Hadoop v clusteru HDInsight.

> [!NOTE]  
> Tento dokument neposkytuje podrobný popis co HiveQL příkazy, které se používají v příkladech. Informace o HiveQL, který se používá v tomto příkladu, naleznete v [tématu Použití Apache Hive s Apache Hadoop na HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Hadoop na HDInsight. Viz [Začínáme s HDInsight na Linuxu](./apache-hadoop-linux-tutorial-get-started.md).

* Nainstalován [modul Az prostředí](https://docs.microsoft.com/powershell/azure/overview) PowerShell.

## <a name="run-a-hive-query"></a>Spuštění dotazu Hive

Azure PowerShell poskytuje *rutiny,* které umožňují vzdáleně spouštět dotazy Hive na HDInsight. Interně rutiny provádět volání REST [webHcat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) v clusteru HDInsight.

Následující rutiny se používají při spouštění dotazů Hive ve vzdáleném clusteru HDInsight:

* `Connect-AzAccount`: Ověřuje Azure PowerShell do vašeho předplatného Azure.
* `New-AzHDInsightHiveJobDefinition`: Vytvoří *definici úlohy* pomocí zadaných příkazů HiveQL.
* `Start-AzHDInsightJob`: Odešle definici úlohy do HDInsight a spustí úlohu. Je vrácen objekt *úlohy.*
* `Wait-AzHDInsightJob`: Používá objekt úlohy ke kontrole stavu úlohy. Čeká na dokončení úlohy nebo na překročení čekací doby.
* `Get-AzHDInsightJobOutput`: Slouží k načtení výstupu úlohy.
* `Invoke-AzHDInsightHiveJob`: Používá se ke spuštění příkazů HiveQL. Tato rutina blokuje dokončení dotazu a pak vrátí výsledky.
* `Use-AzHDInsightCluster`: Nastaví aktuální cluster, `Invoke-AzHDInsightHiveJob` který se má použít pro příkaz.

Následující kroky ukazují, jak pomocí těchto rutin spustit úlohu v clusteru HDInsight:

1. Pomocí editoru uložte následující `hivejob.ps1`kód jako .

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Otevřete nový příkazový řádek **Azure PowerShellu.** Změňte adresáře na `hivejob.ps1` umístění souboru a potom pomocí následujícího příkazu spusťte skript:

        .\hivejob.ps1

    Po spuštění skriptu budete vyzváni k zadání názvu clusteru a přihlašovacích údajů účtu správce HTTPS/Cluster. Můžete být také vyzváni k přihlášení k předplatnému Azure.

3. Po dokončení úlohy vrátí informace podobné následujícímu textu:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Jak již bylo `Invoke-Hive` zmíněno dříve, lze spustit dotaz a čekat na odpověď. Pomocí následujícího skriptu zobrazíte, jak invoke-hive funguje:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    Výstup vypadá takto:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]  
   > Pro delší dotazy HiveQL můžete použít soubory skriptu Azure PowerShell **Here-Strings** nebo HiveQL. Následující úryvek ukazuje, jak `Invoke-Hive` pomocí rutiny spustit soubor skriptu HiveQL. Soubor skriptu HiveQL musí být odeslán do wasbs://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Další informace o **řetězecích here -Strings**naleznete <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">v tématu Using Windows PowerShell Here-Strings</a>.

## <a name="troubleshooting"></a>Řešení potíží

Pokud po dokončení úlohy nejsou vráceny žádné informace, zobrazte protokoly chyb. Chcete-li zobrazit informace o chybě pro tuto `hivejob.ps1` úlohu, přidejte na konec souboru následující, uložte jej a spusťte jej znovu.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Tato rutina vrátí informace, které jsou zapsány do STDERR během zpracování úlohy.

## <a name="summary"></a>Souhrn

Jak můžete vidět, Azure PowerShell poskytuje snadný způsob, jak spustit dotazy Hive v clusteru HDInsight, sledovat stav úlohy a načíst výstup.

## <a name="next-steps"></a>Další kroky

Obecné informace o Hive v HDInsight:

* [Použití Apache Hive s Apache Hadoop na HDInsight](hdinsight-use-hive.md)

Informace o dalších způsobech práce s Hadoopem na HDInsightu:

* [Použití MapReduce s Apache Hadoop na HDInsight](hdinsight-use-mapreduce.md)
