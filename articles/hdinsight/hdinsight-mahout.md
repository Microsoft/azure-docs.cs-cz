---
title: Generování doporučení pomocí Mahout HDInsight z prostředí PowerShell – Azure
description: Další informace o použití Apache Mahout strojového učení knihovny ke generování filmových doporučení pomocí HDInsight (Hadoop) z Powershellový skript spuštěn na klientovi.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: jasonh
ms.openlocfilehash: 587ea8d9082a696853d8e25a36d9536c762d0582
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599985"
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight-powershell"></a>Generování filmových doporučení pomocí Apache Mahout Hadoop v HDInsight (PowerShell)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Další informace o použití [Apache Mahout](http://mahout.apache.org) knihovna pro machine learning pomocí Azure HDInsight ke generování filmových doporučení. V příkladu v tomto dokumentu používá prostředí Azure PowerShell ke spouštění úloh Mahout.

## <a name="prerequisites"></a>Požadavky

* Cluster HDInsight se systémem Linux. Informace o vytvoření nového, najdete v části [Začínáme používat Hadoop využívající systém Linux v HDInsight][getstarted].

    > [!IMPORTANT]
    > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Azure PowerShell](/powershell/azure/overview)

## <a name="recommendations"></a>Generování doporučení pomocí Azure Powershellu

> [!WARNING]
> V této části pracuje s využitím Azure Powershellu. Mnohé z tříd poskytovaných pomocí Mahoutu nefungují aktuálně pomocí Azure Powershellu. Seznam tříd, které fungují s Azure Powershellem, najdete v článku [Poradce při potížích s](#troubleshooting) oddílu.
>
> Příklad použití SSH pro připojení k HDInsight a spuštění příkladů Mahout přímo na clusteru, naleznete v tématu [generování filmových doporučení pomocí Mahout a HDInsight (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md).

Jednou z funkcí, které poskytuje Mahout je doporučovací modul. Tento modul přijímá data ve formátu `userID`, `itemId`, a `prefValue` (Předvolby uživatele pro položky). Mahout data používá k určení uživatelů s předvolby podobné položky, které je možné použít k doporučení.

Následující příklad je zjednodušený návod, jak toho, jak funguje proces doporučení:

* **Společného výskytu**: Joe, Alice a Bob všechny líbilo *Hvězdných*, *The Empire katastrof zpět*, a *návrat Jedi*. Mahout Určuje, že uživatelé, kteří se také jako jednu z těchto filmy, jako je další dvě.

* **Společného výskytu**: Bob a Alice spokojeni také *The fiktivní Menace*, *útoku klonů*, a *odvety Sith*. Mahout Určuje, že uživatelé, kteří také líbilo předchozí tři filmy, jako jsou tyto videa.

* **Doporučení podle podobnosti**: protože Joe líbilo první tři videa, Mahout vypadá na filmy ostatní se podobá předvolby spokojeni, ale Joe nebyl sledovali vysílání televizní (líbilo nebo hodnocení). V takovém případě se doporučuje Mahout *The fiktivní Menace*, *útoku klonů*, a *odvety Sith*.

### <a name="understanding-the-data"></a>Pochopení dat

[Výzkum GroupLens] [ movielens] poskytuje data hodnocení filmů, ve formátu, který je kompatibilní s Mahout. Tato data jsou k dispozici na výchozí úložiště pro váš cluster na `/HdiSamples/HdiSamples/MahoutMovieData`.

Existují dva soubory `moviedb.txt` (informace o videa) a `user-ratings.txt`. `user-ratings.txt` Soubor se používá během analýzy. `moviedb.txt` Soubor se používá k poskytování popisný text při zobrazení výsledků analýzy.

Data obsažená v ratings.txt uživatel má strukturu z `userID`, `movieID`, `userRating`, a `timestamp`, který dává pokyn, jak vysoce hodnocené filmu každého uživatele. Tady je příklad dat:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>Spuštění úlohy

Pomocí následujícího skriptu prostředí Windows PowerShell a spusťte úlohu, která používá data o filmech doporučovací modul Mahout:

> [!NOTE]
> Tento soubor vás vyzve k zadání informací, které se používá pro připojení k vašemu clusteru HDInsight a spouštění úloh. Může trvat několik minut, než se úlohy dokončí, a stáhněte si soubor výstup.txt.

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=5-98)]

> [!NOTE]
> Mahout úlohy neodebírejte dočasných dat, který je vytvořen při zpracování úlohy. `--tempDir` Je zadán parametr v úloze příklad k izolaci dočasných souborů do konkrétní adresář.

Mahout úlohy do STDOUT nevrátí výstup. Místo toho je uložený v zadané výstupní adresář jako **část r-00000**. Tento soubor a stáhne skript **výstup.txt** v aktuálním adresáři na pracovní stanici.

Následující text je příkladem obsah tohoto souboru:

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

První sloupec je `userID`. Hodnoty obsažené v ' [' a ']' jsou `movieId`:`recommendationScore`.

Skript se také stáhne `moviedb.txt` a `user-ratings.txt` soubory, které jsou potřebné k formátování výstupu, aby byly lépe čitelné.

### <a name="view-the-output"></a>Zobrazit výstup

I když generovaný výstup může být OK pro použití v aplikaci, není popisný. `moviedb.txt` Ze serveru je možné přeložit `movieId` název filmu. Následující skript prostředí PowerShell použijte k zobrazení doporučení s názvy filmu:

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=106-180)]

Použijte následující příkaz k zobrazení doporučení v uživatelsky přívětivé formátu: 

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

### <a name="cannot-overwrite-files"></a>Nejde přepsat soubory

Neprovádějte mahout úlohy čištění dočasné soubory, které byly vytvořeny během zpracování. Kromě toho úlohy Nepřepisovat existující výstupního souboru.

Aby nedocházelo k chybám při spuštění Mahout úloh, odstraňte dočasné a výstupní soubory mezi spuštění. K odebrání souborů vytvořených databázovým starší skripty v tomto dokumentu, použijte následující příkaz powershellu:

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

### <a name="nopowershell"></a>Třídy, které nefungují v prostředí Azure PowerShell

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

Ke spuštění úlohy, které používají tyto třídy, připojte se ke clusteru HDInsight pomocí SSH a spusťte úlohy z příkazového řádku. Příklad použití SSH ke spouštění úloh Mahout, naleznete v tématu [generování filmových doporučení pomocí Mahout a HDInsight (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md).

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak pomocí Mahoutu, popisující další způsoby práce s daty v HDInsight:

* [Hive s HDInsight](hadoop/hdinsight-use-hive.md)
* [Pig s HDInsight](hadoop/hdinsight-use-pig.md)
* [MapReduce se službou HDInsight](hadoop/hdinsight-use-mapreduce.md)

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
