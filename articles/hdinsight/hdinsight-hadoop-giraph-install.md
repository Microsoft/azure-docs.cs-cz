---
title: Na nainstalovat a používat Giraph clusterů systému Hadoop v HDInsight - Azure | Microsoft Docs
description: Zjistěte, jak přizpůsobit clusteru HDInsight s Giraph a jak používat Giraph.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 77a1d0e0-55de-4e61-98a0-060914fb7ca0
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: c4cd643d4bdd95493f63bb5b1c1f855bc95bf226
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="install-and-use-giraph-on-windows-based-hdinsight-clusters"></a>Nainstalovat a používat Giraph v clusterech HDInsight se systémem Windows

Zjistěte, jak přizpůsobit na bázi clusteru HDInsight se systémem Windows s Giraph pomocí akce skriptu a postupy pro použití Giraph ke zpracování velkých grafy. Informace o používání Giraph s clusteru se systémem Linux najdete v tématu [Giraph nainstalovat na clusterů systému HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).

> [!IMPORTANT]
> Kroky v tomto dokumentu pracovat pouze s clustery HDInsight se systémem Windows. HDInsight je k dispozici pouze v systému Windows verze nižší než HDInsight 3.4. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Informace o tom, jak nainstalovat Giraph na clusteru HDInsight se systémem Linux najdete v tématu [Giraph nainstalovat na clusterů systému HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).


Giraph můžete nainstalovat na libovolný typ clusteru (Hadoop, Spark, HBase, Storm) v Azure HDInsight pomocí *akce skriptu*. Ukázkový skript pro instalaci Giraph v clusteru HDInsight je k dispozici z objektu blob úložiště Azure jen pro čtení v [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). Ukázkový skript pracuje pouze s verze clusteru HDInsight verze 3.1. Další informace o verzích clusterů HDInsight, naleznete v části [verze clusteru HDInsight](hdinsight-component-versioning.md).

**Související články**

* [Nainstalujte Giraph clusterů systému HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Vytvoření clusterů systému Hadoop v HDInsight](hdinsight-provision-clusters.md): Obecné informace o vytváření clusterů HDInsight.
* [Přizpůsobení clusteru HDInsight pomocí akce skriptu][hdinsight-cluster-customize]: Obecné informace o přizpůsobení clusterů HDInsight pomocí akce skriptu.
* [Vývoj skriptů akce skriptu pro HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>Co je Giraph?
<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> umožňuje provádět grafu zpracování pomocí Hadoop a lze použít s Azure HDInsight. Grafy model relace mezi objekty, jako je připojení mezi směrovače na velké sítě, jako je Internet nebo vztahy mezi uživateli v sociálních sítích (někdy označované jako sociálních grafu). Zpracování graf umožňuje důvod o vztahy mezi objekty v grafu, jako například:

* Identifikace potenciální přátel na základě vaší aktuální relací.
* Identifikace nejkratší trasy mezi dvěma počítači v síti.
* Výpočet pořadí stránky webových stránek.

## <a name="install-giraph-using-portal"></a>Nainstalujte Giraph pomocí portálu
1. Zahájení vytváření clusteru pomocí **vytvořit vlastní** možnost, jak je popsáno v [vytvoření Hadoop clusterů v HDInsight](hdinsight-provision-clusters.md).
2. Na **akcí skriptů** stránku průvodce, klikněte na tlačítko **přidat akce skriptu** poskytnout podrobnosti o akce skriptu, jak je uvedeno níže:

    ![Použití akce skriptu k přizpůsobení cluster](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "použití akce skriptu k přizpůsobení clusteru")

    <table border='1'>
        <tr><th>Vlastnost</th><th>Hodnota</th></tr>
        <tr><td>Název</td>
            <td>Zadejte název akce skriptu. Například <b>nainstalovat Giraph</b>.</td></tr>
        <tr><td>Identifikátor URI skriptu</td>
            <td>Zadejte identifikátoru URI (Uniform Resource) do skriptu, která je volána, chcete-li přizpůsobit clusteru. Například <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
        <tr><td>Typ uzlu</td>
            <td>Zadejte uzly, na kterých běží přizpůsobení skriptu. Můžete zvolit <b>všechny uzly</b>, <b>hlavní pouze uzly</b>, nebo <b>pouze uzly pracovního procesu</b>.
        <tr><td>Parametry</td>
            <td>Zadejte parametry, pokud se vyžadují skriptem. Skript, který chcete nainstalovat Giraph nevyžaduje žádné parametry, takže to můžete nechat prázdné.</td></tr>
    </table>

    Můžete přidat více než jednu akci skriptu pro instalaci více součástí v clusteru. Po přidání skripty, klikněte na značku zaškrtnutí zahájíte vytváření clusteru.

## <a name="use-giraph"></a>Použití Giraph
Příklad SimpleShortestPathsComputation používáme k předvedení základní <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> implementace pro vyhledání nejkratšího cesta mezi objekty v grafu. Použijte následující postup k odeslání vzorovými daty a ukázkové jar, spusťte úlohu pomocí SimpleShortestPathsComputation příklad a pak zobrazte výsledky.

1. Ukázkový datový soubor nahrajte do Azure Blob storage. Na místní pracovní stanici, vytvořte nový soubor s názvem **tiny_graph.txt**. Měl by obsahovat následující řádky:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Nahrajte soubor tiny_graph.txt do primárního úložiště pro váš cluster HDInsight. Pokyny, jak odesílat data, najdete v části [nahrávání dat pro úlohy Hadoop do HDInsight](hdinsight-upload-data.md).

    Tato data popisuje vztah mezi objekty v řízené grafu, ve formátu [zdroj\_id, zdroj\_hodnotu [[cíle\_id], [edge\_hodnotu],...]]. Každý řádek představuje vztah mezi **zdroj\_id** objektů a jeden nebo více **cíle\_id** objekty. **Edge\_hodnotu** (nebo váhy) lze považovat za sílu nebo distance připojení mezi **source_id** a **cíle\_id**.

    Vykreslovat limitu, a pomocí hodnoty (nebo váhy) jako vzdálenost mezi objekty, výše uvedená data může vypadat například takto:

    ![tiny_graph.txt vykreslovat jako kroužky řádků různých vzdálenost mezi](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)
2. Spusťte SimpleShortestPathsComputation příklad. Pomocí následující rutiny prostředí Azure PowerShell pro spuštění příkladu pomocí souboru tiny_graph.txt jako vstup.

    > [!IMPORTANT]
    > Podpora prostředí Azure PowerShell pro správu prostředků služby HDInsight pomocí Azure Service Manageru je **zastaralá** a k 1. lednu 2017 jsme ji odebrali. Kroky v tomto dokumentu používají nové rutiny služby HDInsight, které pracují s Azure Resource Managerem.
    >
    > Podle postupu v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs) si nainstalujte nejnovější verzi prostředí Azure PowerShell. Pokud máte skripty, které je potřeba upravit tak, aby používaly nové rutiny, které pracují s nástrojem Azure Resource Manager, najdete další informace v tématu [Migrace na vývojové nástroje založené na Azure Resource Manageru pro clustery služby HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

    ```powershell
    $clusterName = "clustername"
    # Giraph examples jar
    $jarFile = "wasb:///example/jars/giraph-examples.jar"
    # Arguments for this job
    $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
                    "-ca", "mapred.job.tracker=headnodehost:9010",
                    "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
                    "-vip", "wasb:///example/data/tiny_graph.txt",
                    "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
                    "-op",  "wasb:///example/output/shortestpaths",
                    "-w", "2"
    # Create the definition
    $jobDefinition = New-AzureHDInsightMapReduceJobDefinition
        -JarFile $jarFile
        -ClassName "org.apache.giraph.GiraphRunner"
        -Arguments $jobArguments

    # Run the job, write output to the Azure PowerShell window
    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job
    Write-Host "STDERR"
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
    ```

    Ve výše uvedeném příkladu nahraďte **clustername** s názvem clusteru HDInsight s Giraph nainstalována.
3. Zkontrolujte výsledky. Po dokončení úlohy, výsledky se uloží v dva výstupní soubory v **wasb: / / / Příklad nebo na více systémů nebo shotestpaths** složky. Soubory se nazývají **část m-00001** a **část m-00002**. Proveďte následující kroky ke stažení a zobrazte výstup:

    ```powershell
    $subscriptionName = "<SubscriptionName>"       # Azure subscription name
    $storageAccountName = "<StorageAccountName>"   # Azure Storage account name
    $containerName = "<ContainerName>"             # Blob storage container name

    # Select the current subscription
    Select-AzureSubscription $subscriptionName

    # Create the Storage account context object
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Download the job output to the workstation
    Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
    Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force
    ```

    Tím se vytvoří **příklad nebo výstupní/shortestpaths** struktury adresářů v aktuálním adresáři na pracovní stanici a stažení dva výstupní soubory do tohoto umístění.

    Použití **Cat** rutiny k zobrazení obsahu souborů:

        Cat example/output/shortestpaths/part*

    Výstup by měl vypadat přibližně takto:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation příklad je pevný programového začínat objektu ID 1 a najít nejkratší cestu k jiné objekty. Proto byste si měli přečíst výstup jako `destination_id distance`, kde je vzdálenost hodnota (nebo váhy) okraje mezi objektem ID 1 a identifikátor cílové cesty

    Tato vizualizace, můžete ověřit výsledky podle cestě nejkratší cest mezi ID 1 a všechny ostatní objekty. Všimněte si, že nejkratší cesta ID 1 až ID 4 je 5. Toto je celkový počet vzdálenost mezi <span style="color:orange">ID 1 a 3</span>a potom <span style="color:red">ID 3 a 4</span>.

    ![Kreslení objektů jako kroužky s nejkratší cest mezi](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-aure-powershell"></a>Nainstalujte Giraph pomocí prostředí Azure PowerShell
V tématu [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  Ukázka ukazuje, jak nainstalovat Spark pomocí Azure PowerShell. Je nutné přizpůsobit skript, který chcete použít [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>Nainstalujte Giraph pomocí sady .NET SDK
V tématu [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). Ukázka ukazuje, jak nainstalovat Spark pomocí sady .NET SDK. Je nutné přizpůsobit skript, který chcete použít [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="see-also"></a>Další informace najdete v tématech
* [Nainstalujte Giraph clusterů systému HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Vytvoření clusterů systému Hadoop v HDInsight](hdinsight-provision-clusters.md): Obecné informace o vytváření clusterů HDInsight.
* [Přizpůsobení clusteru HDInsight pomocí akce skriptu][hdinsight-cluster-customize]: Obecné informace o přizpůsobení clusterů HDInsight pomocí akce skriptu.
* [Vývoj skriptů akce skriptu pro HDInsight](hdinsight-hadoop-script-actions.md).
* [Nainstalovat a používat Spark v clusterech HDInsight][hdinsight-install-spark]: akce skriptu vzorku o instalaci Spark.
* [Nainstalujte Solr clustery HDInsight](hdinsight-hadoop-solr-install.md): akce skriptu vzorku o instalaci Solr.

[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
