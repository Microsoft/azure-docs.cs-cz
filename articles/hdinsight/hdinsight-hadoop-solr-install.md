---
title: Použití akce skriptu k instalaci Solr na clusteru Hadoop - Azure | Microsoft Docs
description: Zjistěte, jak přizpůsobit clusteru HDInsight pomocí Solr pomocí akce skriptu.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b1e6f338-8ac1-4b38-bbb5-2f7388b9de3b
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: caabe0fea6286c9439e8929b054d771868dcb6f1
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34272144"
---
# <a name="install-and-use-solr-on-windows-based-hdinsight-clusters"></a>Nainstalovat a používat Solr v clusterech HDInsight se systémem Windows

Zjistěte, jak přizpůsobit clusteru HDInsight se systémem Windows s Solr pomocí akce skriptu a jak používat Solr k vyhledávání dat.

> [!IMPORTANT]
> Kroky v tomto dokumentu pracovat pouze s clustery HDInsight se systémem Windows. HDInsight je k dispozici pouze v systému Windows verze nižší než HDInsight 3.4. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Informace o používání Solr s clusteru se systémem Linux najdete v tématu [instalací a použitím Solr na clusterů systému HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md).


Solr můžete nainstalovat na libovolný typ clusteru (Hadoop, Spark, HBase, Storm) v Azure HDInsight pomocí *akce skriptu*. Ukázkový skript pro instalaci Solr v clusteru HDInsight je k dispozici z objektu blob úložiště Azure jen pro čtení v [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

Ukázkový skript pracuje pouze s verze clusteru HDInsight verze 3.1. Další informace o verzích clusterů HDInsight, naleznete v části [verze clusteru HDInsight](hdinsight-component-versioning.md).

Ukázkový skript použitým v tomto tématu vytvoří cluster se systémem Windows Solr s konkrétní konfigurací. Pokud chcete konfigurovat Solr cluster s různých kolekcí, horizontálních oddílů, schémata, repliky, atd., musíte upravit skript a binární soubory Solr odpovídajícím způsobem.

**Související články**

* [Na nainstalovat a používat Solr clusterů systému HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Vytvoření clusterů systému Hadoop v HDInsight](hdinsight-provision-clusters.md): Obecné informace o vytváření clusterů HDInsight.
* [Přizpůsobení clusteru HDInsight pomocí akce skriptu][hdinsight-cluster-customize]: Obecné informace o přizpůsobení clusterů HDInsight pomocí akce skriptu.
* [Vývoj skriptů akce skriptu pro HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-solr"></a>Co je Solr?
<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> je platforma enterprise vyhledávání, která umožňuje efektivní fulltextové vyhledávání na data. Při Hadoop umožňuje ukládání a správě obrovské množství dat, Apache Solr poskytuje možnosti vyhledávání lze snadno obnovit data.

## <a name="install-solr-using-portal"></a>Nainstalujte Solr pomocí portálu
1. Zahájení vytváření clusteru pomocí **vytvořit vlastní** možnost, jak je popsáno v [vytvoření Hadoop clusterů v HDInsight](hdinsight-provision-clusters.md).
2. Na **akcí skriptů** stránku průvodce, klikněte na tlačítko **přidat akce skriptu** poskytnout podrobnosti o akce skriptu, jak je uvedeno níže:

    ![Použití akce skriptu k přizpůsobení cluster](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "použití akce skriptu k přizpůsobení clusteru")

    <table border='1'>
        <tr><th>Vlastnost</th><th>Hodnota</th></tr>
        <tr><td>Název</td>
            <td>Zadejte název akce skriptu. Například <b>nainstalovat Solr</b>.</td></tr>
        <tr><td>Identifikátor URI skriptu</td>
            <td>Zadejte identifikátoru URI (Uniform Resource) do skriptu, která je volána, chcete-li přizpůsobit clusteru. Například <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>Typ uzlu</td>
            <td>Zadejte uzly, na kterých běží přizpůsobení skriptu. Můžete zvolit <b>všechny uzly</b>, <b>hlavní pouze uzly</b>, nebo <b>pouze uzly pracovního procesu</b>.
        <tr><td>Parametry</td>
            <td>Zadejte parametry, pokud se vyžadují skriptem. Skript, který chcete nainstalovat Solr nevyžaduje žádné parametry, takže to můžete nechat prázdné.</td></tr>
    </table>

    Můžete přidat více než jednu akci skriptu pro instalaci více součástí v clusteru. Po přidání skripty, klikněte na značku zaškrtnutí zahájíte vytváření clusteru.

## <a name="use-solr"></a>Použití Solr
Musí začínat indexování Solr s některých datových souborů. Solr potom můžete spustit vyhledávací dotazy na indexovaná data. Proveďte následující kroky a použít Solr v clusteru služby HDInsight:

1. **Pomocí protokolu RDP (Remote Desktop) vzdáleného do clusteru HDInsight s Solr nainstalován**. Z portálu Azure povolení vzdálené plochy pro cluster, který jste vytvořili pomocí Solr nainstalován a pak vzdálené do clusteru. Pokyny najdete v tématu [připojení ke clusterům HDInsight pomocí protokolu RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Index Solr tím, že nahrajete datové soubory**. Pokud jste indexu Solr, vložíte v něm, které můžete potřebovat pro vyhledávání na dokumenty. Index Solr, pomocí protokolu RDP vzdáleného do clusteru, přejděte na ploše, otevřete příkazový řádek Hadoop a přejděte do **C:\apps\dist\solr-4.7.2\example\exampledocs**. Spusťte následující příkaz:

        java -jar post.jar solr.xml monitor.xml

    V konzole se zobrazí následující výstup:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    Nástroj post.jar indexuje Solr s dva ukázkové dokumenty **solr.xml** a **monitor.xml**. Nástroj post.jar a ukázkové dokumenty jsou dostupné s Solr instalace.
3. **Solr řídicí panel můžete použít pro vyhledávání v rámci indexované dokumenty**. V této relaci RDP ke clusteru HDInsight, otevřete Internet Explorer a spusťte Solr řídicí panel v **http://headnodehost:8983/solr/#/**. V levém podokně z **základní selektor** rozevíracího seznamu, vyberte **collection1**a v rámci, klikněte na tlačítko **dotazu**. Jako příklad vyberte a vrátí všechny dokumenty v Solr, zadejte následující hodnoty:

   * V **q** textové pole, zadejte  **\*:**\*. Tato možnost vrátí všechny dokumenty, které jsou uloženy ve Solr. Pokud chcete vyhledat konkrétní řetězec v rámci dokumenty, můžete zadat sem tento řetězec.
   * V **wt** textové pole, vyberte formát výstupu. Výchozí hodnota je **json**. Klikněte na tlačítko **spuštění dotazu**.

     ![Použití akce skriptu k přizpůsobení cluster](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "spuštění dotazu na řídicím panelu Solr")

     Výstup vrací dva dokumenty, které jsme použili pro indexování Solr. Výstup vypadá přibližně takto:

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }
4. **Doporučená: Zálohování indexovaná data z Solr do úložiště objektů Azure Blob, které jsou přidruženy ke clusteru HDInsight**. Jako osvědčených postupů měli zálohovat indexovaná data z uzlů clusteru Solr do úložiště objektů Blob v Azure. Proveďte následující kroky k tomu:

   1. Z relace RDP otevřete Internet Explorer a přejděte na následující adresu URL:

           http://localhost:8983/solr/replication?command=backup

       Zobrazí takováto odpověď:

           <?xml version="1.0" encoding="UTF-8"?>
           <response>
             <lst name="responseHeader">
               <int name="status">0</int>
               <int name="QTime">9</int>
             </lst>
             <str name="status">OK</str>
           </response>
   2. V této relaci vzdálené přejděte na {SOLR_HOME}\{kolekce} \data. Pro cluster vytvořené prostřednictvím ukázkový skript, měl by být **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**. V tomto umístění, měli byste vidět do složky snímku vytvořen s názvem podobná **snímku.* časové razítko ***.
   3. ZIP složky snímků a nahrajte ho do úložiště objektů Blob Azure. Z příkazového řádku Hadoop přejděte do umístění složky snímků pomocí následujícího příkazu:

             hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

       Tento příkaz zkopíruje snímku do /example/data nebo v kontejneru v rámci výchozí účet úložiště, které jsou přidruženy ke clusteru.

## <a name="install-solr-using-aure-powershell"></a>Nainstalujte Solr pomocí prostředí Azure PowerShell
V tématu [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  Ukázka ukazuje, jak nainstalovat Spark pomocí Azure PowerShell. Je nutné přizpůsobit skript, který chcete použít [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="install-solr-using-net-sdk"></a>Nainstalujte Solr pomocí sady .NET SDK
V tématu [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). Ukázka ukazuje, jak nainstalovat Spark pomocí sady .NET SDK. Je nutné přizpůsobit skript, který chcete použít [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="see-also"></a>Další informace najdete v tématech
* [Na nainstalovat a používat Solr clusterů systému HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Vytvoření clusterů systému Hadoop v HDInsight](hdinsight-provision-clusters.md): Obecné informace o vytváření clusterů HDInsight.
* [Přizpůsobení clusteru HDInsight pomocí akce skriptu][hdinsight-cluster-customize]: Obecné informace o přizpůsobení clusterů HDInsight pomocí akce skriptu.
* [Vývoj skriptů akce skriptu pro HDInsight](hdinsight-hadoop-script-actions.md).
* [Nainstalovat a používat Spark v clusterech HDInsight][hdinsight-install-spark]: akce skriptu vzorku o instalaci Spark.
* [Nainstalujte Giraph clustery HDInsight](hdinsight-hadoop-giraph-install.md): akce skriptu vzorku o instalaci Giraph.

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
