---
title: Použití akce skriptu na clusteru Hadoop – Azure nainstalovat Solr
description: Zjistěte, jak přizpůsobit clusteru HDInsight s využitím akcí skriptů Solr.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 3f382aab5a00030b922fb890e82110454e7f3839
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384492"
---
# <a name="install-and-use-apache-solr-on-windows-based-hdinsight-clusters"></a>Instalace a použití Apache Solr na clusterech HDInsight se systémem Windows

Naučte se přizpůsobit clusteru HDInsight se systémem Windows pomocí Apache Solr pomocí akce skriptu a použití Solru na prohledávat data.

> [!IMPORTANT]  
> Kroky v tomto dokumentu fungovat jenom s clustery HDInsight se systémem Windows. HDInsight je dostupná jenom ve Windows pro verze nižší než HDInsight 3.4. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Informace o použití Solr v clusteru se systémem Linux najdete v tématu [instalace a použití Apache Solr na clusterech HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md).


Solr můžete nainstalovat na libovolný typ clusteru Azure HDInsight (Hadoop, Storm, HBase, Spark) s použitím *akce skriptu*. Ukázkový skript do clusteru služby HDInsight nainstalovat Solr je k dispozici jen pro čtení služby Azure storage blob na [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

Ukázkový skript funguje pouze s verze clusteru HDInsight verze 3.1. Další informace o verzích clusterů HDInsight, naleznete v tématu [verze clusterů HDInsight](hdinsight-component-versioning.md).

Ukázkový skript použít v tomto tématu vytvoří cluster na základě Windows Solr s konkrétní konfigurací. Pokud chcete konfigurovat Solr cluster pomocí různých kolekcí, horizontální oddíly, schémata, repliky a podobně, je třeba upravit skript a binární soubory Solr odpovídajícím způsobem.

**Související články**

* [Nainstalovat a používat Apache Solr na clusterech HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Vytvoření clusterů Hadoop v HDInsight](hdinsight-provision-clusters.md): Obecné informace o vytváření clusterů HDInsight.
* [Přizpůsobení clusteru HDInsight pomocí skriptových akcí][hdinsight-cluster-customize]: Obecné informace o přizpůsobení clusterů HDInsight pomocí skriptových akcí.
* [Vývoj skriptových akcí skriptů pro HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-solr"></a>Co je Solr?
<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> je platforma pro podnikové vyhledávání, která umožňuje výkonné fulltextové vyhledávání na datech. Hadoop umožňuje ukládání a správu obrovských objemů dat, Apache Solr poskytuje funkce vyhledávání rychle načíst data.

## <a name="install-solr-using-portal"></a>Nainstalovat Solr pomocí portálu
1. Začněte s vytvářením clusteru s použitím **vytvořit vlastní** možnosti, jak je popsáno v [vytvořit Apache Hadoop clusterů v HDInsight](hdinsight-provision-clusters.md).
2. Na **akcí skriptů** stránku průvodce, klikněte na tlačítko **přidat akci se skripty** k zajištění podrobných informací o akce skriptu, jak je znázorněno níže:

    ![Přizpůsobení clusteru pomocí akce skriptu](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "přizpůsobení clusteru pomocí akce skriptu")

    <table border='1'>
        <tr><th>Vlastnost</th><th>Hodnota</th></tr>
        <tr><td>Název</td>
            <td>Zadejte název akce skriptu. Například <b>nainstalovat Solr</b>.</td></tr>
        <tr><td>Identifikátor URI skriptu</td>
            <td>Zadejte identifikátor URI (Uniform Resource), která je volána k přizpůsobení clusteru skriptu. Například <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>Typ uzlu</td>
            <td>Zadejte uzly, na kterých běží přizpůsobení skriptu. Můžete zvolit <b>všechny uzly</b>, <b>hlavním uzlům pouze</b>, nebo <b>pracovní uzly pouze</b>.
        <tr><td>Parametry</td>
            <td>Zadejte parametry, pokud je to nutné skript. Skript, který chcete nainstalovat Solr nevyžaduje žádné parametry, takže můžete nechat prázdné.</td></tr>
    </table>

    Můžete přidat více než jednu akci se skripty pro instalaci více součástí clusteru. Po přidání skripty, klikněte na značku zaškrtnutí zahájíte vytváření clusteru.

## <a name="use-solr"></a>Použití Solr
Je nutné začít s indexování Solr pomocí některé datové soubory. Solr pak můžete spustit vyhledávací dotazy na indexovaná data. Proveďte následující kroky a použití Solru v clusteru HDInsight:

1. **Pomocí protokolu RDP (Remote Desktop) na vzdálený do clusteru HDInsight nainstalovat Solr**. Na webu Azure Portal povolte vzdálenou plochu pro cluster, který jste vytvořili pomocí Solr nainstalované a pak vzdálené do clusteru. Pokyny najdete v tématu [připojit ke clusterům HDInsight pomocí protokolu RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Solr index tak, že nahrajete datové soubory**. Indexování Solr vložíte dokumenty, které možná budete muset vyhledat. Index Solr, použitím protokolu RDP pro vzdálené do clusteru, přejděte na ploše, otevřete příkazový řádek systému Hadoop a přejděte do **C:\apps\dist\solr-4.7.2\example\exampledocs**. Spusťte následující příkaz:

        java -jar post.jar solr.xml monitor.xml

    Se zobrazí následující výstup na konzole:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    Nástroj post.jar indexuje Solr s dvě ukázkové dokumenty **solr.xml** a **monitor.xml**. Nástroj post.jar a ukázkové dokumenty jsou k dispozici s instalací Solr.
3. **Pomocí řídicího panelu Solr můžete vyhledávat v indexovaných dokumentů**. V relaci protokolu RDP do clusteru HDInsight, spusťte aplikaci Internet Explorer a spuštění řídicího panelu Solr na **http://headnodehost:8983/solr/#/**. V levém podokně z **Core selektor** rozevíracího seznamu, vyberte **collection1**a v rámci, klikněte na tlačítko **dotazu**. Například pokud chcete vybrat a vrátí všechny dokumenty v Solr, zadejte následující hodnoty:

   * V **q** textové pole, zadejte  **\*:**\*. Vrátí všechny dokumenty, které jsou indexovány v Solr. Pokud chcete vyhledat řetězec specifický v rámci dokumentů, můžete zadat Tento řetězec tady.
   * V **wt** textové pole, vyberte formát výstupu. Výchozí hodnota je **json**. Klikněte na tlačítko **spuštění dotazu**.

     ![Přizpůsobení clusteru pomocí akce skriptu](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "spustit dotaz na řídicí panel Solr")

     Výstup vrací dva dokumenty, které jste použili pro indexování Solr. Výstup vypadá přibližně takto:

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
4. **Doporučené: Proveďte zálohu indexovaná data z Solr do Azure Blob storage přidružené ke clusteru HDInsight**. Dobrým postupem je měli zálohovat indexovaná data z uzlů clusteru Solr do úložiště objektů Blob v Azure. Proveďte následující postup:

   1. Z relace protokolu RDP spusťte aplikaci Internet Explorer a přejděte na následující adresu URL:

           http://localhost:8983/solr/replication?command=backup

       Měli byste vidět odpovědi takto:
            
      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
          <response>
             <lst name="responseHeader">
               <int name="status">0</int>
               <int name="QTime">9</int>
             </lst>
            <str name="status">OK</str>
          </response>
      ```
      
   2. Ve vzdálené relaci, přejděte na {SOLR_HOME}\{kolekce} \data. Pro cluster vytvořeny pomocí ukázkového skriptu, měl by to být `C:\apps\dist\solr-4.7.2\example\solr\collection1\data`. V tomto umístění, měli byste vidět složky snímků s názvem podobný **snímku.\*časové razítko**\*.
   
   3. Složka snímku zkomprimovat a nahrát ho do úložiště objektů Blob v Azure. Z příkazového řádku Hadoopu přejděte do umístění složky snímků pomocí následujícího příkazu:

      ```
      hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data
      ```

   Tento příkaz zkopírování snímku do /example/data a v kontejneru v rámci výchozí účet úložiště, které jsou přidružené ke clusteru.

## <a name="install-solr-using-aure-powershell"></a>Nainstalovat Solr pomocí Azure Powershellu
Zobrazit [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  Vzorek ukazuje, jak nainstalovat Apache Sparkem s využitím Azure Powershellu. Je třeba přizpůsobit skript, který chcete použít [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="install-solr-using-net-sdk"></a>Nainstalovat Solr pomocí sady .NET SDK
Zobrazit [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). Vzorek ukazuje, jak nainstalovat Apache Spark pomocí sady .NET SDK. Je třeba přizpůsobit skript, který chcete použít [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="see-also"></a>Další informace najdete v tématech
* [Nainstalovat a používat Apache Solr na clusterech HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Vytvořte clustery systému Apache Hadoop v HDInsight](hdinsight-provision-clusters.md): Obecné informace o vytváření clusterů HDInsight.
* [Přizpůsobení clusteru HDInsight pomocí skriptových akcí][hdinsight-cluster-customize]: Obecné informace o přizpůsobení clusterů HDInsight pomocí skriptových akcí.
* [Vývoj skriptových akcí skriptů pro HDInsight](hdinsight-hadoop-script-actions.md).
* [Nainstalovat a používat Apache Spark v clusterech HDInsight][hdinsight-install-spark]: Ukázkový skript akce o instalaci Spark.
* [Clustery HDInsight nainstalovat Apache Giraph](hdinsight-hadoop-giraph-install.md): Ukázkový skript akce o instalaci Giraph

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
