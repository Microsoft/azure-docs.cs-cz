---
title: Generování doporučení pomocí Mahout HDInsight z prostředí PowerShell – Azure | Microsoft Docs
description: Další informace o použití Apache Mahout strojového učení knihovny pro generování doporučení s HDInsight (Hadoop) ze skriptu prostředí PowerShell systémem vašeho klienta.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 49a092ee23b79c483aa7bbd8b3d5150e909b6884
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight-powershell"></a>Generování doporučení pomocí Apache Mahout s Hadoop v HDInsight (PowerShell)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Další informace o použití [Apache Mahout](http://mahout.apache.org) knihovny machine learning s Azure HDInsight ke generování doporučení. V příkladu v tomto dokumentu používá ke spuštění úlohy Mahout prostředí Azure PowerShell.

## <a name="prerequisites"></a>Požadavky

* Cluster HDInsight se systémem Linux. Informace o vytváření jeden najdete v tématu [začít používat systémem Linux Hadoop v HDInsight][getstarted].

    > [!IMPORTANT]
    > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Azure PowerShell](/powershell/azure/overview)

## <a name="recommendations"></a>Generování doporučení pomocí prostředí Azure PowerShell

> [!WARNING]
> Úlohy v této části funguje tak, že pomocí Azure PowerShell. Řadu třídy součástí Mahout nefungují aktuálně s prostředím Azure PowerShell. Seznam tříd, které nefungují s prostředím Azure PowerShell najdete v tématu [Poradce při potížích s](#troubleshooting) části.
>
> Příklad použití SSH se připojit k HDInsight a spuštění příklady Mahout přímo na clusteru, naleznete v části [generování doporučení pomocí Mahout a HDInsight (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md).

Jednou z funkcí, které zajišťuje Mahout je modul doporučení. Tento modul přijímá data ve formátu `userID`, `itemId`, a `prefValue` (předvolba uživatele pro položku). Mahout data používá k určení uživatelů s předvolby jako položky, které se dají použít tak, aby doporučení.

Následující příklad je zjednodušený návod, jak funguje proces doporučení:

* **společné výskyt**: Jan, Alice a Bob všechny líbilo *hvězdičky válek*, *Empire stávky zpět*, a *návrat Jedi*. Mahout Určuje, že uživatelé, kteří také jako některého z těchto filmy jako další dvě.

* **společné výskyt**: Bob a Alice také líbilo *The Menace fiktivní*, *útoku klonů*, a *odvety Sith*. Mahout Určuje, že uživatelé, kteří líbilo předchozí tři filmy také jako tyto filmy.

* **Podobnosti doporučení**: protože Jan líbilo první tři filmy, Mahout vypadá na filmy ostatní s líbilo podobných předvolby, ale nebyla Jan sledovaná (líbilo nebo hodnocení). V takovém případě se doporučuje Mahout *The Menace fiktivní*, *útoku klonů*, a *odvety Sith*.

### <a name="understanding-the-data"></a>Pochopení dat

[GroupLens Research] [ movielens] poskytuje hodnocení data pro filmy ve formátu, který je kompatibilní s Mahout. Tato data jsou k dispozici na výchozí úložiště pro váš cluster v `/HdiSamples/HdiSamples/MahoutMovieData`.

Existují dva soubory, `moviedb.txt` (informace o videa) a `user-ratings.txt`. `user-ratings.txt` Soubor se používá během analýzy. `moviedb.txt` Soubor se používá k zadejte popisný text při zobrazení výsledky analýzy.

Data obsažená v ratings.txt uživatel má struktura `userID`, `movieID`, `userRating`, a `timestamp`, která sděluje, jak vysoce každý uživatel hodnocení filmu. Tady je příklad dat:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>Spustit úlohu

Spustit úlohu, která používá modul doporučení Mahout s daty film pomocí následujícího skriptu prostředí Windows PowerShell:

> [!NOTE]
> Tento soubor vyzve k zadání informace, které se používá k připojení ke svému clusteru HDInsight a spuštění úloh. To může trvat několik minut na dokončení a stáhněte si soubor výstup.txt úloh.

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=5-98)]

> [!NOTE]
> Mahout úlohy neodebírejte dočasná data, která je vytvořena při zpracování úlohy. `--tempDir` v úloze příklad izolovat dočasné soubory do konkrétního adresáře je zadán parametr.

Úloha Mahout nevrátí výstup STDOUT. Místo toho je uložený v zadané výstupní adresář jako **část r-00000**. Skript stáhne tento soubor do **výstup.txt** v aktuálním adresáři na pracovní stanici.

Tento text je příkladem obsah tohoto souboru:

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

První sloupec je `userID`. Hodnoty obsažené v ' [' a ']' jsou `movieId`:`recommendationScore`.

Skript také stáhne `moviedb.txt` a `user-ratings.txt` soubory, které jsou potřebné k formátování výstupu být srozumitelnější.

### <a name="view-the-output"></a>Zobrazit výstup

I když generovaný výstup může být OK pro použití v aplikaci, není uživatelsky přívětivý. `moviedb.txt` Ze serveru, můžete použít k vyřešení `movieId` film název. Pomocí následujícího skriptu prostředí PowerShell zobrazíte doporučení s názvy film:

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=106-180)]

Pokud chcete zobrazit doporučení v uživatelsky přívětivý formátu použijte následující příkaz: 

```powershell
.\show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt
```

Výstup se bude podobat následujícímu:

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237

## <a name="troubleshooting"></a>Řešení potíží

### <a name="cannot-overwrite-files"></a>Nelze přepsat soubory

Mahout úlohy se vyčistit dočasné soubory, které byly vytvořeny během zpracování. Kromě toho úlohy Nepřepisovat existující soubor výstupu.

Aby nedocházelo k chybám při spuštění úlohy Mahout, odstraňte dočasné a výstupní soubory mezi spustí. Chcete-li odebrat soubory vytvořené pomocí dřívějších skripty v tomto dokumentu, použijte následující skript prostředí PowerShell:

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Azure PowerShell can't delete blobs using wildcard,
#so have to get a list and delete one at a time
# Start with the output
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/out"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
# Next the temp files
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/temp"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
```

### <a name="nopowershell"></a>Třídy, které nefungují s prostředím Azure PowerShell

Mahout úlohy, které používají následující třídy vrátit různé chybové zprávy při použití v prostředí Windows PowerShell:

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Ke spuštění úloh, které používají tyto třídy, připojte se ke clusteru HDInsight pomocí protokolu SSH a spuštění úloh z příkazového řádku. Příklad použití SSH ke spuštění úloh Mahout, naleznete v části [generování doporučení pomocí Mahout a HDInsight (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md).

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili použití Mahout, zjišťovat další způsoby, jak pracovat s daty v HDInsight:

* [Hive s HDInsight](hadoop/hdinsight-use-hive.md)
* [Pig s HDInsight](hadoop/hdinsight-use-pig.md)
* [MapReduce s HDInsight](hadoop/hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
