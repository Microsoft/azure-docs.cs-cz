---
title: Využití R v HDInsight k přizpůsobení clusterů - Azure | Microsoft Docs
description: Naučte se instalovat pomocí akce skriptu R a použijte prostředí R v clusterech prostředí HDInsight.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: be851270-afa5-4af0-a69e-2d343a4deeb7
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: c5fb38de8b1023a05709d07ba17b0c776c87957e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31405867"
---
# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Instalace a použití R na clusterech HDInsight Hadoop

Zjistěte, jak pro přizpůsobení systému Windows na základě clusteru HDInsight pomocí pomocí akce skriptu R a clusterů v tom, jak používat R v HDInsight. [HDInsight nabídky](https://azure.microsoft.com/pricing/details/hdinsight/) zahrnuje R Server v rámci clusteru HDInsight. To umožňuje skripty R se použije ke spuštění distribuovaných výpočty MapReduce a Spark. Další informace naleznete v tématu [Začínáme používat R Server v HDInsight](r-server/r-server-get-started.md). Informace o používání R s clusteru se systémem Linux najdete v tématu [instalací a použitím R na clusterů systému HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md).

R můžete nainstalovat na libovolný typ clusteru (Hadoop, Spark, HBase, Storm) v Azure HDInsight pomocí *akce skriptu*. Ukázkový skript instalace R na clusteru služby HDInsight je k dispozici z objektu blob úložiště Azure jen pro čtení v [ https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1 ](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

**Související články**

* [Na nainstalovat a používat R clusterů systému HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [Vytvoření clusterů systému Hadoop v HDInsight](hdinsight-hadoop-provision-linux-clusters.md): Obecné informace o vytváření clusterů HDInsight
* [Přizpůsobení clusteru HDInsight pomocí akce skriptu][hdinsight-cluster-customize]: Obecné informace o přizpůsobení clusterů HDInsight pomocí akce skriptu
* [Vývoj skriptů akce skriptu pro HDInsight](hdinsight-hadoop-script-actions.md)

## <a name="what-is-r"></a>Co je R?
<a href="http://www.r-project.org/" target="_blank">R projekt pro statistické výpočty</a> je open source jazyk a prostředí pro statistické výpočty. R poskytuje stovky sestavení v statistických funkcí a vlastní programovací jazyk, který kombinuje aspektů funkčnosti a objektově orientované programování. Také poskytuje rozsáhlé možnosti grafického rozhraní. R je upřednostňovaný programovací prostředí pro nejvíce professional statistikami a vědců v celé řadě polí.

R je kompatibilní s Azure Blob Storage (WASB) tak, aby data, která je uložená existuje lze zpracovat využití R v HDInsight.  

## <a name="install-r"></a>Nainstalujte jazyk R
A [ukázkový skript](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) nainstalovat na HDInsight clusteru má k dispozici jen pro čtení objektů blob v Azure Storage. Tato část obsahuje informace o tom, jak pomocí vzorového skriptu při vytváření clusteru pomocí portálu Azure.

> [!NOTE]
> Ukázkový skript byla zavedena v systému verze clusteru HDInsight verze 3.1. Další informace o verzích clusterů HDInsight, naleznete v části [verze clusteru HDInsight](hdinsight-component-versioning.md).
>
>

1. Při vytváření clusteru služby HDInsight z portálu, klikněte na tlačítko **volitelné konfiguraci**a potom klikněte na **akcí skriptů**.
2. Na **akcí skriptů** stránky, zadejte následující hodnoty:

    ![Použití akce skriptu k přizpůsobení cluster](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "použití akce skriptu k přizpůsobení clusteru")

    <table border='1'>
        <tr><th>Vlastnost</th><th>Hodnota</th></tr>
        <tr><td>Název</td>
            <td>Zadejte název pro skript akce, například <b>nainstalovat R</b>.</td></tr>
        <tr><td>Identifikátor URI skriptu</td>
            <td>Zadejte identifikátor URI skriptu, která je volána, chcete-li přizpůsobit clusteru, například <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
        <tr><td>Typ uzlu</td>
            <td>Zadejte uzly, na kterých běží přizpůsobení skriptu. Můžete zvolit <b>všechny uzly</b>, <b>hlavní pouze uzly</b>, nebo <b>uzlů pracovního procesu</b> pouze.
        <tr><td>Parametry</td>
            <td>Zadejte parametry, pokud se vyžadují skriptem. Skript, který chcete nainstalovat, ale nevyžaduje žádné parametry, tak to můžete nechat prázdné.</td></tr>
    </table>

    Můžete přidat více než jednu akci skriptu pro instalaci více součástí v clusteru. Po přidání skripty, kliknutím na značku zaškrtnutí zahájíte crating clusteru.

Tento skript můžete použít také k instalaci R v HDInsight pomocí prostředí Azure PowerShell nebo sady SDK rozhraní .NET HDInsight. Pokyny pro tyto postupy jsou uvedeny dále v tomto článku.

## <a name="run-r-scripts"></a>Spusťte skripty R
Tato část popisuje, jak spustit skript v jazyce R na clusteru Hadoop v prostředí HDInsight.

1. **Navázat připojení ke vzdálené ploše do clusteru**: povolení služby Vzdálená plocha pro cluster, který jste vytvořili pomocí R nainstalované z aplikace Portal a připojte se ke clusteru. Pokyny najdete v tématu [připojení ke clusterům HDInsight pomocí protokolu RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Otevřete konzolu R**: instalace R vloží odkaz ke konzole R na ploše hlavního uzlu. Klikněte na něj pro otevření konzoly R.
3. **Spustit skript jazyka R**: R skript můžete spustit přímo z konzoly R vložením, ho vyberete a stisknutím klávesy ENTER. Zde je jednoduchý příklad skript, který generuje čísla 1 až 100 a potom je vynásobí 2.

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

První dva řádky volání RHadoop knihovny, které jsou nainstalované s R. Poslední řádek vytiskne výsledků do konzoly. Výstup by měl vypadat takto:

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## <a name="install-r-using-aure-powershell"></a>Nainstalujte jazyk R pomocí prostředí Azure PowerShell
V tématu [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  Ukázka ukazuje, jak nainstalovat Spark pomocí Azure PowerShell. Je nutné přizpůsobit skript, který chcete použít [ https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1 ](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

## <a name="install-r-using-net-sdk"></a>Nainstalujte jazyk R pomocí sady .NET SDK
V tématu [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). Ukázka ukazuje, jak nainstalovat Spark pomocí sady .NET SDK. Je nutné přizpůsobit skript, který chcete použít [ https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1 ](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11).

## <a name="see-also"></a>Další informace najdete v tématech
* [Na nainstalovat a používat R clusterů systému HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [Vytvoření clusterů systému Hadoop v HDInsight](hdinsight-hadoop-provision-linux-clusters.md): Obecné informace o vytváření clusterů HDInsight
* [Přizpůsobení clusteru HDInsight pomocí akce skriptu][hdinsight-cluster-customize]: Obecné informace o přizpůsobení clusterů HDInsight pomocí akce skriptu
* [Vývoj skriptů akce skriptu pro HDInsight](hdinsight-hadoop-script-actions.md)
* [Nainstalovat a používat Spark v clusterech HDInsight][hdinsight-install-spark]: akce skriptu vzorku o instalaci Spark
* [Nainstalujte Giraph clustery HDInsight](hdinsight-hadoop-giraph-install.md): akce skriptu vzorku o instalaci Giraph
* [Nainstalujte Solr clustery HDInsight](hdinsight-hadoop-solr-install-linux.md): akce skriptu vzorku o instalaci Solr.

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]:spark/apache-spark-jupyter-spark-sql.md
