---
title: Použití Apache Ambari Tez zobrazení s HDInsight – Azure
description: Další informace o použití zobrazení Apache Ambari Tez k ladění úloh Tez v HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 312b476f8809d1d6375cc20035901d8d11c32173
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012347"
---
# <a name="use-apache-ambari-views-to-debug-apache-tez-jobs-on-hdinsight"></a>Použití zobrazení Apache Ambari k ladění úloh Tez Apache v HDInsight

[Apache Ambari](https://ambari.apache.org/) obsahuje webové uživatelské rozhraní pro HDInsight [Apache TEZ](https://tez.apache.org/) zobrazení, které můžete použít k pochopení a ladění úloh, které používají Tez. Zobrazení Tez umožňuje vizualizovat úlohy jako graf připojené položky, jednotlivé položky Přejít k podrobnostem a získat statistiky a informace o protokolování.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují cluster HDInsight s Linuxem. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Správa verzí komponenty HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Požadavky

* Cluster HDInsight se systémem Linux. Pokyny týkající se vytvoření clusteru, najdete v článku [začněte používat HDInsight se systémem Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Moderní webový prohlížeč, který podporuje HTML5.

## <a name="understanding-apache-tez"></a>Principy Apache Tez

Tez je rozšiřitelná platforma pro zpracování dat v Apache Hadoop, která poskytuje vyšší rychlostí než tradiční MapReduce zpracování. Pro clustery HDInsight založené na Linuxu je výchozí modul pro Hive.

Tez vytvoří přesměruje acyklický graf (DAG), který popisuje pořadí akce požadované úlohami. Jednotlivé akce se nazývají vrcholy a spustit část celkové úlohy. Aktuální provádění práce popsané ve vrcholu je volána úloha a mohou být distribuovány napříč několika uzly v clusteru.

### <a name="understanding-the-tez-view"></a>Principy zobrazení Tez

Zobrazení Tez poskytuje historické informace a informace o procesy, které jsou spuštěny. Tyto informace zobrazují, jak se úlohy distribuovat napříč clustery. Zobrazí se také čítače používané úlohy a vrcholy a informace o chybách souvisejících s úlohou. Vám může nabídnout užitečné informace v následujících scénářích:

* Sledování dlouho běžící procesy, zobrazení průběhu mapy a omezení úloh.
* Analýza historických dat pro úspěšné nebo neúspěšné procesy se dozvíte, jak může zlepšit zpracování nebo proč se nezdařilo.

## <a name="generate-a-dag"></a>Generovat DAG

Zobrazení Tez obsahuje pouze data, pokud úlohu, která používá modul Tez aktuálně běží, nebo byl byly dříve spuštěny. Bez použití Tez lze přeložit jednoduchých dotazů Hive. Složitější dotazy, které filtrování seskupení, řazení, spojení, atd. Použití modulu Tez.

Ke spuštění dotazu Hive, který používá Tez, postupujte následovně:

1. Ve webovém prohlížeči přejděte na https://CLUSTERNAME.azurehdinsight.net, kde **CLUSTERNAME** je název vašeho clusteru HDInsight.

2. V nabídce v horní části stránky vyberte **zobrazení** ikonu. Tato ikona vypadá jako řadu čtverce. V rozevíracím seznamu, který se zobrazí, vyberte **Hive zobrazení**.

    ![Výběr zobrazení Hive](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Při načtení zobrazení Hive, vložte do editoru dotazů následující dotaz a pak klikněte na tlačítko **provést**.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    Až se úloha dokončí, byste měli vidět výstup zobrazí v **výsledky zpracování dotazu** oddílu. Výsledky by měl být podobný následujícímu textu:

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. Vyberte **protokolu** kartu. Zobrazí informace podobné následujícímu textu:

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Uložit **id aplikace** hodnotu, protože tato hodnota se používá v další části.

## <a name="use-the-tez-view"></a>Použití zobrazení Tez

1. V nabídce v horní části stránky vyberte **zobrazení** ikonu. V rozevíracím seznamu, který se zobrazí, vyberte **Tez zobrazení**.

    ![Výběr zobrazení Tez](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Při načtení zobrazení Tez, zobrazí se seznam dotazů hive, které jsou aktuálně spuštěné, nebo byla spuštěna v clusteru.

    ![Všechny DAG](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. Pokud máte pouze jednu položku, je pro dotaz, který jste spustili v předchozí části. Pokud máte více položek, můžete hledat pomocí pole v horní části stránky.

4. Vyberte **ID dotazu** pro dotaz Hive. Zobrazí se informace o dotazu.

    ![Podrobnosti o skupině DAG](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. Karty na této stránce umožňují zobrazit následující informace:

    * **Podrobnosti dotazu**: Podrobnosti o dotazu Hive.
    * **Časová osa**: informace o tom, jak dlouho trvalo každá fáze zpracování.
    * **Konfigurace**: Konfigurace použitá pro tento dotaz.

    Z __podrobnosti dotazu__ můžete pomocí odkazů můžete získat informace o __aplikace__ nebo __DAG__ pro tento dotaz.
    
    * __Aplikace__ odkaz zobrazí informace o aplikaci YARN pro tento dotaz. Odsud můžete přístup k protokolům aplikace YARN.
    * __DAG__ odkaz zobrazí informace o orientovaného acyklického grafu pro tento dotaz. Odsud můžete zobrazit grafická reprezentace tomto orientovaném acyklickém grafu. Můžete také najít informace o vrcholy v tomto orientovaném acyklickém grafu.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak používat zobrazení Apache Tez, další informace o [pomocí Apache Hive s HDInsight](hadoop/hdinsight-use-hive.md).

Podrobné technické informace o Apache Tez, najdete v článku [Apache Tez stránku na Hortonworks](https://hortonworks.com/hadoop/tez/).

Další informace o používání Apache Ambari se službou HDInsight najdete v tématu [HDInsight Správa clusterů pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md)
