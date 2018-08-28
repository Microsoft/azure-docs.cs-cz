---
title: Instalace a použití Giraphu na clusterech Hadoop v HDInsight – Azure
description: Zjistěte, jak přizpůsobit clusteru HDInsight pomocí Giraphu a použití Giraphu.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 0cf866ee472717c6021922927713ef0bf8817271
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43093240"
---
# <a name="install-and-use-giraph-on-windows-based-hdinsight-clusters"></a>Instalace a použití Giraphu na clusterech HDInsight se systémem Windows

Zjistěte, jak přizpůsobit Windows založené na clusteru HDInsight s využitím akcí skriptů Giraph a použití Giraph k zpracování rozsáhlých grafů. Informace o použití Giraphu ke clusteru se systémem Linux najdete v tématu [nainstalovat Giraph u clusterů systému HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).

> [!IMPORTANT]
> Kroky v tomto dokumentu fungovat jenom s clustery HDInsight se systémem Windows. HDInsight je dostupná jenom ve Windows pro verze nižší než HDInsight 3.4. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Informace o tom, jak nainstalovat Giraph v clusteru HDInsight se systémem Linux najdete v tématu [nainstalovat Giraph u clusterů systému HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).


Giraph můžete nainstalovat na libovolný typ clusteru Azure HDInsight (Hadoop, Storm, HBase, Spark) s použitím *akce skriptu*. Ukázkový skript do clusteru služby HDInsight nainstalovat Giraph je k dispozici jen pro čtení služby Azure storage blob na [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). Ukázkový skript funguje pouze s verze clusteru HDInsight verze 3.1. Další informace o verzích clusterů HDInsight, naleznete v tématu [verze clusterů HDInsight](hdinsight-component-versioning.md).

**Související články**

* [Nainstalovat Giraph u clusterů systému HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Vytvoření clusterů Hadoop v HDInsight](hdinsight-provision-clusters.md): Obecné informace o vytváření clusterů HDInsight.
* [Přizpůsobení clusteru HDInsight pomocí skriptových akcí][hdinsight-cluster-customize]: Obecné informace o přizpůsobení clusterů HDInsight pomocí skriptových akcí.
* [Vývoj skriptových akcí skriptů pro HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>Co je Giraph?
<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> je možné provádět zpracování s použitím Hadoopu grafů a jde použít s Azure HDInsight. Grafy modelovat vztahy mezi objekty, jako je například připojení mezi směrovači ve velké síti, jako je Internet nebo vztahy mezi uživateli v sociálních sítích (někdy označované jako sociální graf). Zpracování grafů umožňuje argumentovat o vztahy mezi objekty v grafu, jako například:

* Určení potenciální přátel podle aktuální relace.
* Identifikace nejkratší mezi dvěma počítači v síti.
* Výpočet hodnocení stránky webových stránek.

## <a name="install-giraph-using-portal"></a>Nainstalovat Giraph pomocí portálu
1. Začněte s vytvářením clusteru s použitím **vytvořit vlastní** možnosti, jak je popsáno v [vytváření clusterů Hadoop v HDInsight](hdinsight-provision-clusters.md).
2. Na **akcí skriptů** stránku průvodce, klikněte na tlačítko **přidat akci se skripty** k zajištění podrobných informací o akce skriptu, jak je znázorněno níže:

    ![Přizpůsobení clusteru pomocí akce skriptu](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "přizpůsobení clusteru pomocí akce skriptu")

    <table border='1'>
        <tr><th>Vlastnost</th><th>Hodnota</th></tr>
        <tr><td>Název</td>
            <td>Zadejte název akce skriptu. Například <b>nainstalovat Giraph</b>.</td></tr>
        <tr><td>Identifikátor URI skriptu</td>
            <td>Zadejte identifikátor URI (Uniform Resource), která je volána k přizpůsobení clusteru skriptu. Například <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
        <tr><td>Typ uzlu</td>
            <td>Zadejte uzly, na kterých běží přizpůsobení skriptu. Můžete zvolit <b>všechny uzly</b>, <b>hlavním uzlům pouze</b>, nebo <b>pracovní uzly pouze</b>.
        <tr><td>Parametry</td>
            <td>Zadejte parametry, pokud je to nutné skript. Skript, který chcete nainstalovat Giraph nevyžaduje žádné parametry, takže můžete nechat prázdné.</td></tr>
    </table>

    Můžete přidat více než jednu akci se skripty pro instalaci více součástí clusteru. Po přidání skripty, klikněte na značku zaškrtnutí zahájíte vytváření clusteru.

## <a name="use-giraph"></a>Použití Giraph
K předvedení na úrovni basic použijeme příklad SimpleShortestPathsComputation <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> implementace pro nalezení nejkratší cesty mezi objekty v grafu. Pomocí následujících kroků k nahrání ukázkových dat a ukázkový soubor jar, spustit úlohu s využitím příkladu SimpleShortestPathsComputation a pak zobrazte výsledky.

1. Nahrajte ukázkový datový soubor do úložiště objektů Blob v Azure. Na místní pracovní stanici, vytvořte nový soubor s názvem **tiny_graph.txt**. Měl by obsahovat následující řádky:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Nahrajte soubor tiny_graph.txt do primárního úložiště pro váš cluster HDInsight. Pokyny, jak nahrát data, najdete v části [nahrávání dat pro úlohy systému Hadoop v HDInsight](hdinsight-upload-data.md).

    Tato data popisuje vztah mezi objekty v řízeném grafu ve formátu [zdroj\_id, zdroj\_hodnoty, [[dest\_id], [edge\_hodnota];...]]. Každý řádek představuje vztah mezi **zdroj\_id** objekt a jeden nebo více **dest\_id** objekty. **Hrany\_hodnotu** (nebo váha) si lze představit jako sílu nebo vzdálenost připojení mezi **source_id** a **dest\_id**.

    Vleklým, a pomocí hodnoty (nebo váha) jako vzdálenosti mezi objekty, výše uvedená data může vypadat třeba takto:

    ![tiny_graph.txt vykreslen jako kroužky s různou vzdálenost mezi řádky](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)
2. Spustíte příklad SimpleShortestPathsComputation. Pomocí následujících rutin prostředí Azure PowerShell ke spuštění příkladu s použitím souboru tiny_graph.txt jako vstup.

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

    V předchozím příkladu nahraďte **clustername** s názvem, který se má nainstalovat Giraph clusteru HDInsight.
3. Zkontrolujte výsledky. Po dokončení úlohy, výsledky uloží ve dvou souborech výstup v **wasb: / / / Příklad/out/shotestpaths** složky. Soubory se nazývají **část m-00001** a **část m-00002**. Proveďte následující kroky si stáhnout a zobrazit výstup:

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

    Tím se vytvoří **příklad/výstup/shortestpaths** adresářovou strukturu v aktuálním adresáři na pracovní stanici a stažení dvou výstupní soubory do tohoto umístění.

    Použití **Cat** rutiny zobrazíte obsah souborů:

        Cat example/output/shortestpaths/part*

    Výstup by měl vypadat nějak takto:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation příklad je obtížné programového začít s objektu ID 1 a nalezení nejkratší cesty na jiné objekty. Proto byste si měli přečíst výstup jako `destination_id distance`, kde je vzdálenost hodnoty (nebo váha) okraje mezi objektem ID 1 a ID cílové cesty

    Tato vizualizace, můžete ověřit výsledky tak cestování nejkratší cesty mezi ID 1 a všechny ostatní objekty. Všimněte si, že nejkratší cesty mezi ID 1 a ID 4 je 5. Toto je celkový počet vzdálenost mezi <span style="color:orange">ID 1 a 3</span>a potom <span style="color:red">ID 3 a 4</span>.

    ![Vykreslení objektů jako kroužky s nejkratší cesty mezi](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-aure-powershell"></a>Nainstalovat Giraph pomocí Azure Powershellu
Zobrazit [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  Vzorek ukazuje, jak nainstalovat Spark pomocí Azure Powershellu. Je třeba přizpůsobit skript, který chcete použít [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>Nainstalovat Giraph pomocí sady .NET SDK
Zobrazit [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). Vzorek ukazuje, jak nainstalovat Spark pomocí sady .NET SDK. Je třeba přizpůsobit skript, který chcete použít [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="see-also"></a>Další informace najdete v tématech
* [Nainstalovat Giraph u clusterů systému HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Vytvoření clusterů Hadoop v HDInsight](hdinsight-provision-clusters.md): Obecné informace o vytváření clusterů HDInsight.
* [Přizpůsobení clusteru HDInsight pomocí skriptových akcí][hdinsight-cluster-customize]: Obecné informace o přizpůsobení clusterů HDInsight pomocí skriptových akcí.
* [Vývoj skriptových akcí skriptů pro HDInsight](hdinsight-hadoop-script-actions.md).
* [Instalace a použití Sparku na clusterech HDInsight][hdinsight-install-spark]: akce skriptu vzorku o instalaci Spark.
* [Nainstalovat Solr na clusterech HDInsight](hdinsight-hadoop-solr-install.md): akce skriptu vzorku o instalaci Solr.

[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
