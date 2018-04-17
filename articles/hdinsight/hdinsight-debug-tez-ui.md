---
title: Použijte uživatelské rozhraní Tez s HDInsight se systémem Windows - Azure | Microsoft Docs
description: Zjistěte, jak pomocí uživatelského rozhraní Tez k ladění úlohách Tez na HDInsight HDInsight se systémem Windows.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: a55bccb9-7c32-4ff2-b654-213a2354bd5c
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 4201fb76ef9b0e711fd48972db86c356d72e6671
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-the-tez-ui-to-debug-tez-jobs-on-windows-based-hdinsight"></a>Chcete-li ladit úlohách Tez na HDInsight se systémem Windows pomocí uživatelského rozhraní Tez
Rozhraní Tez lze použít k ladění úlohy Hive, které používají Tez jako modul provádění. Rozhraní Tez vizualizuje úlohy, jako graf připojených položek, můžete přejít k podrobnostem jednotlivých položek a načíst informace o protokolování a statistiky.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují clusteru HDInsight se systémem Windows. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Požadavky
* Cluster HDInsight se systémem Windows. Pokyny týkající se vytvoření nového clusteru, najdete v části [začněte používat HDInsight se systémem Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

  > [!IMPORTANT]
  > Rozhraní Tez je dostupná pouze na clustery HDInsight se systémem Windows, které jsou vytvořené po 8. únoru 2016.
  >
  >
* Klient vzdálené plochy se systémem Windows.

## <a name="understanding-tez"></a>Principy Tez
Tez je rozšiřitelná architektura pro zpracování dat v Hadoop a poskytuje vyšší rychlosti než tradiční zpracování prostředí MapReduce. Můžete povolit Tez zahrnutím následující text jako součást dotazu Hive:

    set hive.execution.engine=tez;

Tez vytvoří směrované Acyklické grafu (DAG) popisující pořadí provádění akcí požadovaných úlohou. Jednotlivé akce se nazývají vrcholy a provést část celkového úlohy. Skutečné provádění pracovní popsaného vrchol je volána úloha a mohou být distribuovány mezi několika uzly v clusteru.

### <a name="understanding-the-tez-ui"></a>Vysvětlení rozhraní Tez
Rozhraní Tez je, že na webové stránce poskytuje informace o procesy, které používají Tez. Vám může nabídnout užitečné informace v následujících scénářích:

* Monitorování dlouho běžící procesy, zobrazení průběhu mapy a snížit úlohy.
* Analýza historické údaje o úspěšném nebo neúspěšném procesy, které se dozvíte, jak lze zlepšit zpracování nebo proč se nezdařilo.

## <a name="generate-a-dag"></a>Generovat DAG
Rozhraní Tez obsahuje data, pokud úlohu, která používá modul Tez běží v současné době nebo byl byla spuštěna v minulosti. Jednoduché dotazů Hive obvykle lze přeložit bez použití Tez. Složitější dotazy, které provádějí filtrování, seskupování, řazení, spojení, atd., vyžadují Tez.

Pomocí následujících kroků spouštění dotazů Hive, který používá Tez.

1. Ve webovém prohlížeči, přejděte na https://CLUSTERNAME.azurehdinsight.net, kde **CLUSTERNAME** je název clusteru HDInsight.
2. V nabídce v horní části stránky, vyberte **Hive Editor**. Zobrazí se stránka s následující příklad dotazu.

        Select * from hivesampletable

    Erase – příklad dotazu a nahraďte ji následujícím textem.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
3. Vyberte **odeslání** tlačítko. **Úlohy relace** v dolní části stránky zobrazí stav dotazu. Jakmile se stav změní na **dokončeno**, vyberte **zobrazit podrobnosti** odkaz zobrazíte výsledky. **Výstup úlohy** by mělo být podobné následujícímu:

        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## <a name="use-the-tez-ui"></a>Pomocí uživatelského rozhraní Tez
> [!NOTE]
> Rozhraní Tez je dostupná pouze z plochy hlavních uzlech clusteru, je nutné použít vzdálené plochy pro připojení k hlavnímu uzlu.
>
>

1. Z [portál Azure](https://portal.azure.com), vyberte clusteru HDInsight. Z horní části okna HDInsight, vyberte **vzdálené plochy** ikonu. Tento odkaz zobrazí okno Vzdálené plochy

    ![Ikona vzdálené plochy](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)
2. V okně připojení ke vzdálené ploše vyberte **Connect** pro připojení k hlavnímu uzlu clusteru. Pokud budete vyzváni, použijte clusteru vzdálené plochy uživatelské jméno a heslo k ověření připojení.

    ![Ikona připojení vzdálené plochy](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

   > [!NOTE]
   > Pokud jste nepovolili připojení vzdálené plochy, zadejte uživatelské jméno, heslo a datum vypršení platnosti a pak vyberte **povolit** k povolení služby Vzdálená plocha. Poté, co byla povolena, použijte předchozí kroky pro připojení.
   >
   >
3. Po připojení otevřete Internet Explorer na vzdálenou plochu, vyberte ikonu ozubené kolečko v pravém horním rohu stránky prohlížeče a pak vyberte **nastavení kompatibilního zobrazení**.
4. V dolní části **nastavení kompatibilního zobrazení**, zrušte zaškrtnutí políčka pro **zobrazit intranetové servery v kompatibilního zobrazení** a **použití Microsoft kompatibility seznamy**, a potom vyberte **Zavřít**.
5. V Internet Exploreru přejděte do http://headnodehost:8188/tezui/#/. Zobrazí se uživatelské rozhraní Tez

    ![Tez uživatelského rozhraní](./media/hdinsight-debug-tez-ui/tezui.png)

    Pokud rozhraní Tez načte, uvidíte, že seznam DAG, které jsou aktuálně spuštěné, nebo byla spuštěna v clusteru. Výchozí zobrazení zahrnuje DAG název, Id, odesílatel, stav, čas spuštění, čas ukončení, doba trvání, ID aplikace a fronty. Pomocí ikony ozubené kolečko na pravé straně stránky lze přidat více sloupců.

    Pokud máte pouze jednu položku, je pro dotaz, který jste spustili v předchozí části. Pokud máte více položek, můžete vyhledat tak, že zadáte kritéria hledání v polích nad DAG a pak stiskněte tlačítko **Enter**.
6. Vyberte **Dag název** u nejnovější položky DAG. Tento odkaz zobrazí informace o DAG, a také možnost stáhnout zip soubory JSON, které obsahují informace o DAG.

    ![Podrobnosti o DAG](./media/hdinsight-debug-tez-ui/dagdetails.png)
7. Výše **DAG podrobnosti** je několik odkazů, které lze použít k zobrazení informací o DAG.

   * **Čítače DAG** zobrazí informace o čítačích pro tento DAG.
   * **Grafické zobrazení** zobrazuje grafické reprezentace této DAG.
   * **Všechny vrcholy** zobrazí seznam vrcholy v této DAG.
   * **Všechny úlohy** zobrazí seznam úloh pro všechny vrcholy v této DAG.
   * **Všechny TaskAttempts** zobrazí informace o pokusí spustit úlohy pro tuto DAG.

     > [!NOTE]
     > Pokud se posunete zobrazení sloupce pro vrcholy, úlohy a TaskAttempts, Všimněte si, že jsou odkazů pro zobrazení **čítače** a **zobrazení nebo stažení protokolů** pro každý řádek.
     >
     >

     Pokud došlo k selhání s úlohou, podrobnosti DAG zobrazovat stav se nezdařilo, spolu s odkazy na informace o neúspěšné úloze. Diagnostické informace se zobrazí pod podrobnosti DAG.
8. Vyberte **grafické zobrazení**. Zobrazí se grafické reprezentace DAG. Myši můžete umístit každý vrchol, v zobrazení pro zobrazení informací o něm.

    ![Grafické zobrazení](./media/hdinsight-debug-tez-ui/dagdiagram.png)
9. Kliknutím na vrchol načte **vrchol podrobnosti** pro tuto položku. Klikněte na **mapy 1** vrchol si můžete zobrazit podrobnosti pro tuto položku. Vyberte **potvrdit** potvrďte navigaci.

    ![Vrchol podrobnosti](./media/hdinsight-debug-tez-ui/vertexdetails.png)
10. Upozorňujeme ale, nyní se odkazy v horní části stránky, která se vztahují k vrcholy a úloh.

    > [!NOTE]
    > Můžete se na tuto stránku tak, že přejdete zpět na také doručení **DAG podrobnosti**, vyberete **vrchol podrobnosti**a potom výběrem **mapy 1** vrchol.
    >
    >

    * **Vrchol čítače** zobrazí čítače informace pro tento vrchol.
    * **Úlohy** zobrazuje úlohy, které pro tento vrchol.
    * **Úloha pokusy o** zobrazí informace o pokusí spustit úlohy pro tuto vrchol.
    * **Zdroje & jímky** zobrazí zdroje dat a pro tento vrchol jímky.

      > [!NOTE]
      > Jako s předchozí nabídky můžete posuňte zobrazení sloupce pro úlohy, pokusy o úloh a zdroje & Sinks__ zobrazíte odkazy na další informace pro každou položku.
      >
      >
11. Vyberte **úlohy**a potom vyberte položku s názvem **00_000000**. Tento odkaz zobrazí **podrobnosti úlohy** pro tuto úlohu. Na této obrazovce můžete zobrazit **úloh čítače** a **úloh pokusy**.

    ![Detail úlohy](./media/hdinsight-debug-tez-ui/taskdetails.png)

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili použití Tez zobrazení, další informace o [pomocí Hive v HDInsight](hadoop/hdinsight-use-hive.md).

Další podrobné technické informace o Tez naleznete v tématu [Tez stránku v Hortonworks](http://hortonworks.com/hadoop/tez/).
