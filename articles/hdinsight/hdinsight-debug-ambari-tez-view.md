---
title: Použijte zobrazení Ambari Tez s HDInsight - Azure | Microsoft Docs
description: Další informace o použití zobrazení Ambari Tez k ladění úlohách Tez v HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 9c39ea56-670b-4699-aba0-0f64c261e411
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 98874377f31a435e7dd9736410c123ef623928d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401586"
---
# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>Použití zobrazení Ambari k ladění úlohách Tez v HDInsight

Webové uživatelské rozhraní Ambari pro HDInsight obsahuje Tez zobrazení, které můžete použít k pochopení a ladění úlohy, které používají Tez. Zobrazení Tez umožňuje vizualizovat úlohu jako graf připojených položek, přejdete na každou položku a načíst informace o protokolování a statistiky.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují clusteru služby HDInsight, který používá Linux. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Správa verzí komponenty HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Požadavky

* Cluster HDInsight se systémem Linux. Pokyny týkající se vytvoření clusteru, najdete v části [začněte používat HDInsight se systémem Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Moderní webový prohlížeč, který podporuje HTML5.

## <a name="understanding-tez"></a>Principy Tez

Tez je rozšiřitelná architektura pro zpracování dat v Hadoop, která poskytuje vyšší rychlosti než tradiční zpracování prostředí MapReduce. Clustery HDInsight se systémem Linux je výchozí modul pro Hive.

Tez vytvoří směrované Acyklické grafu (DAG) popisující pořadí akce požadované úlohami. Jednotlivé akce se nazývají vrcholy a provést část celkového úlohy. Skutečné provádění pracovní popsaného vrchol je volána úloha a mohou být distribuovány mezi několika uzly v clusteru.

### <a name="understanding-the-tez-view"></a>Principy zobrazení Tez

Zobrazení Tez poskytuje historické informace a informace na procesy, které jsou spuštěné. Tyto informace ukazuje, jak je úlohu rozdělené mezi clustery. Zobrazí se také čítače používané úlohy a vrcholy a informace o chybě související s úlohy. Vám může nabídnout užitečné informace v následujících scénářích:

* Monitorování dlouho běžící procesy, zobrazení průběhu mapy a snížit úlohy.
* Analýza historické údaje o úspěšném nebo neúspěšném procesy, které se dozvíte, jak lze zlepšit zpracování nebo proč se nezdařilo.

## <a name="generate-a-dag"></a>Generovat DAG

Zobrazení Tez obsahuje pouze data, pokud úlohu, která používá modul Tez běží v současné době nebo byl byla dříve spuštěna. Jednoduché dotazů Hive, bez použití Tez lze vyřešit. Složitější dotazy tento filtrování proveďte seskupení, řazení, spojení, atd. Použití modulu Tez.

Pro spouštění dotazů Hive, který používá Tez použijte následující postup:

1. Ve webovém prohlížeči, přejděte na https://CLUSTERNAME.azurehdinsight.net, kde **CLUSTERNAME** je název clusteru HDInsight.

2. V nabídce v horní části stránky, vyberte **zobrazení** ikonu. Tato ikona vypadá jako řadu kvadratických hodnot. V rozevírací nabídce, která se zobrazí, vyberte **Hive zobrazení**.

    ![Výběr zobrazení Hive](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Při načtení zobrazení Hive, vložte následující dotaz do editoru dotazů a pak klikněte na tlačítko **provést**.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    Po dokončení úlohy byste měli vidět výstup v zobrazí **výsledky zpracování dotazu** části. Výsledky by mělo být podobné následujícímu:

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. Vyberte **protokolu** kartě. Zobrazí informace podobná následující text:

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Uložit **id aplikace** hodnota, protože tato hodnota se používá v další části.

## <a name="use-the-tez-view"></a>Pomocí zobrazení Tez

1. V nabídce v horní části stránky, vyberte **zobrazení** ikonu. V rozevírací nabídce, která se zobrazí, vyberte **Tez zobrazení**.

    ![Výběr zobrazení Tez](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Při načtení zobrazení Tez, uvidíte, že seznam dotazů hive, které jsou aktuálně spuštěné, nebo byla spuštěna v clusteru.

    ![Všechny DAG](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. Pokud máte pouze jednu položku, je pro dotaz, který jste spustili v předchozí části. Pokud máte více položek, může hledat pomocí pole v horní části stránky.

4. Vyberte **ID dotazu** pro dotaz Hive. Zobrazí se informace o dotazu.

    ![Podrobnosti o DAG](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. Karty na této stránce můžete zobrazit následující informace:

    * **Dotaz na podrobnosti**: Podrobnosti o dotaz Hive.
    * **Časová osa**: informace o tom, jak dlouho trvalo každé fáze zpracování.
    * **Konfigurace**: Konfigurace použitá pro tento dotaz.

    Z __dotazu podrobnosti__ můžete pomocí odkazů můžete získat informace o __aplikace__ nebo __DAG__ pro tento dotaz.
    
    * __Aplikace__ odkaz zobrazí informace o aplikaci YARN pro tento dotaz. Odsud můžete přejít do aplikačních protokolů YARN.
    * __DAG__ odkaz zobrazí informace o necyklicky pro tento dotaz. Odtud můžete zobrazit grafické reprezentace DAG. Můžete také získat informace o vrcholy v rámci DAG.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili použití Tez zobrazení, další informace o [pomocí Hive v HDInsight](hadoop/hdinsight-use-hive.md).

Další podrobné technické informace o Tez naleznete v tématu [Tez stránku v Hortonworks](http://hortonworks.com/hadoop/tez/).

Další informace o používání Ambari s HDInsight najdete v tématu [Správa clusterů HDInsight pomocí Ambari webového uživatelského rozhraní](hdinsight-hadoop-manage-ambari.md)
