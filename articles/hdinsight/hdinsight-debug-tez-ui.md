---
title: Pomocí uživatelského rozhraní Tez založené na Windows HDInsight – Azure
description: Další informace o použití uživatelského rozhraní Tez k ladění úloh Tez na HDInsight HDInsight založené na Windows.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: f54cc60f9490b8a5ca1872a290c3895ea8b0c5e4
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590897"
---
# <a name="use-the-tez-ui-to-debug-tez-jobs-on-windows-based-hdinsight"></a>Použití uživatelského rozhraní Tez k ladění úloh Tez na HDInsight se systémem Windows
Uživatelského rozhraní Tez lze použít k ladění úloh Hive, které používají jako prováděcí modul Tez. Uživatelského rozhraní Tez vizualizuje úlohy graf připojené položky, můžete přejít k podrobnostem jednotlivých položek a získání statistik a informace o protokolování.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují clusteru služby HDInsight, používající Windows. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Požadavky
* Cluster HDInsight se systémem Windows. Pokyny k vytvoření nového clusteru, najdete v článku [začněte používat HDInsight se systémem Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

  > [!IMPORTANT]
  > Uživatelského rozhraní Tez je dostupná pouze na clustery HDInsight se systémem Windows, které jsou vytvořené po 8. února 2016.
  >
  >
* Klient vzdálené plochy se systémem Windows.

## <a name="understanding-tez"></a>Principy Tez
Tez je rozšiřitelná platforma pro zpracování dat v systému Hadoop a poskytuje vyšší rychlostí než tradiční MapReduce zpracování. Můžete povolit Tez zahrnutím následujícího textu jako součást dotazu Hive:

    set hive.execution.engine=tez;

Tez vytvoří přesměruje acyklický graf (DAG), který popisuje pořadí provádění akcí požadovaných úlohou. Jednotlivé akce se nazývají vrcholy a spustit část celkové úlohy. Aktuální provádění práce popsané ve vrcholu je volána úloha a mohou být distribuovány napříč několika uzly v clusteru.

### <a name="understanding-the-tez-ui"></a>Principy uživatelského rozhraní Tez
Uživatelského rozhraní Tez je že na webové stránce poskytuje informace o procesy, které používají Tez. Vám může nabídnout užitečné informace v následujících scénářích:

* Sledování dlouho běžící procesy, zobrazení průběhu mapy a omezení úloh.
* Analýza historických dat pro úspěšné nebo neúspěšné procesy se dozvíte, jak může zlepšit zpracování nebo proč se nezdařilo.

## <a name="generate-a-dag"></a>Generovat DAG
Uživatelského rozhraní Tez obsahuje data, pokud úlohu, která používá modul Tez aktuálně běží, nebo byl spuštěn v minulosti. Jednoduché dotazy Hive můžete obvykle možné přeložit bez použití Tez. Složitější dotazy, které filtrováním, seskupení, řazení, spojení a podobně vyžadují Tez.

Použijte následující postup ke spuštění dotazu Hive, který používá Tez.

1. Ve webovém prohlížeči přejděte na https://CLUSTERNAME.azurehdinsight.net, kde **CLUSTERNAME** je název vašeho clusteru HDInsight.
2. V nabídce v horní části stránky vyberte **Hive Editor**. Zobrazí se stránka s následující příklad dotazu.

        Select * from hivesampletable

    Vymazat příklad dotazu a nahraďte ji následujícím kódem.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
3. Vyberte **odeslat** tlačítko. **Relaci úloh** části v dolní části stránky zobrazí stav dotazu. Jakmile se stav změní na **dokončeno**, vyberte **zobrazit podrobnosti o** odkaz k zobrazení výsledků. **Výstup úlohy** by měl vypadat přibližně takto:

        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## <a name="use-the-tez-ui"></a>Použití uživatelského rozhraní Tez
> [!NOTE]
> Uživatelského rozhraní Tez je dostupné z desktopu hlavní uzly clusteru, pouze je nutné použít vzdálené plochy pro připojení k hlavním uzlům.
>
>

1. Z [webu Azure portal](https://portal.azure.com), vyberte svůj cluster HDInsight. V horní části okna HDInsight, vyberte **vzdálené plochy** ikonu. Tento odkaz zobrazí okně vzdálené plochy

    ![Ikona vzdálené plochy](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)
2. V okně Vzdálená plocha vyberte **připojit** pro připojení k hlavnímu uzlu clusteru. Po zobrazení výzvy použijte cluster vzdálené plochy uživatelské jméno a heslo pro ověření připojení.

    ![Ikona připojení ke vzdálené ploše](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

   > [!NOTE]
   > Pokud jste ještě nepovolili připojení ke vzdálené ploše, zadejte uživatelské jméno, heslo a datum vypršení platnosti a pak vyberte **povolit** povolit vzdálenou plochu. Jakmile bylo povoleno, použijte pro připojení v předchozích krocích.
   >
   >
3. Po připojení se na vzdálené plochy spusťte aplikaci Internet Explorer, vyberte ikonu ozubeného kolečka v pravém horním rohu prohlížeče a pak vyberte **nastavení kompatibilního zobrazení**.
4. V dolní části **nastavení kompatibilního zobrazení**, zrušte zaškrtnutí políčka pro **zobrazení intranetových serverů v kompatibilní zobrazení** a **seznamy kompatibility použít Microsoft**, a potom vyberte **Zavřít**.
5. V Internet Exploreru přejděte na http://headnodehost:8188/tezui/#/. Zobrazí se uživatelského rozhraní Tez

    ![Uživatelského rozhraní tez](./media/hdinsight-debug-tez-ui/tezui.png)

    Při načtení uživatelského rozhraní Tez, zobrazí se seznam DAG, které jsou aktuálně spuštěné, nebo byla spuštěna v clusteru. Výchozí zobrazení obsahuje název DAG, Id, odesílatel, stav, čas zahájení, čas ukončení, doba trvání, ID aplikace a fronty. Pomocí ikony ozubeného kolečka v pravém rohu stránky lze přidat další sloupce.

    Pokud máte pouze jednu položku, je pro dotaz, který jste spustili v předchozí části. Pokud máte více položek, můžete vyhledat zadáním kritérií hledání v polích nad DAG, stačí stisknout **Enter**.
6. Vyberte **Dag název** nejnovější položky DAG. Tento odkaz zobrazí informace o tomto orientovaném acyklickém grafu, stejně jako možnost stáhnout zazipované soubory JSON, které obsahují informace o tomto orientovaném acyklickém grafu.

    ![Podrobnosti o skupině DAG](./media/hdinsight-debug-tez-ui/dagdetails.png)
7. Výše **DAG podrobnosti** je i několik odkazů, které slouží k zobrazení informací o tomto orientovaném acyklickém grafu.

   * **Čítače DAG** zobrazuje čítače informace o tomto orientovaném acyklickém grafu.
   * **Grafické zobrazení** zobrazí grafické reprezentace tomto orientovaném acyklickém grafu.
   * **Všechny vrcholy** zobrazí seznam vrcholů v tomto orientovaném acyklickém grafu.
   * **Všechny úlohy** zobrazí seznam úloh pro všechny vrcholy v tomto orientovaném acyklickém grafu.
   * **Všechny TaskAttempts** zobrazí informace o pokusy o spuštění úlohy pro tomto orientovaném acyklickém grafu.

     > [!NOTE]
     > Pokud posunete zobrazení sloupců pro vrcholy, úkoly a TaskAttempts, Všimněte si, že jsou odkazů pro zobrazení **čítače** a **zobrazení nebo stažení protokolů** pro každý řádek.
     >
     >

     Pokud došlo k chybě s úlohou, zobrazovat podrobnosti DAG stav nebyl úspěšný, spolu s odkazy na informace o neúspěšné úloze. Diagnostické informace se zobrazí pod podrobnosti DAG.
8. Vyberte **grafické zobrazení**. Zobrazí se grafická reprezentace tomto orientovaném acyklickém grafu. Každý vrchol, v zobrazení pro zobrazení informací o něm můžete umístit ukazatel myši.

    ![Grafické zobrazení](./media/hdinsight-debug-tez-ui/dagdiagram.png)
9. Kliknutím na vrcholu načte **podrobnosti vrcholu** pro danou položku. Klikněte na **mapování 1** vrchol, chcete-li zobrazit podrobnosti pro tuto položku. Vyberte **potvrdit** potvrďte navigaci.

    ![Podrobnosti vrcholu](./media/hdinsight-debug-tez-ui/vertexdetails.png)
10. Všimněte si, že máte nyní odkazy v horní části stránky, které se vztahují k vrcholů a úkoly.

    > [!NOTE]
    > Můžete také dostanete na této stránce tak, že přejdete zpět **DAG podrobnosti**, kde vyberou **podrobnosti vrcholu**a pak vyberete **mapování 1** vrcholu.
    >
    >

    * **Vrchol čítače** zobrazí informace o čítači pro tento vrchol.
    * **Úlohy** zobrazuje úlohy pro tento vrchol.
    * **Úloha pokusy** zobrazí informace o pokusy o spuštění úlohy pro tento vrchol.
    * **Zdroje a jímky** zobrazí zdroje a jímky pro tento vrchol.

      > [!NOTE]
      > Jako s předchozí nabídky můžete procházet zobrazení sloupců pro úlohy, pokusy o spuštění úkolu a zdrojů a Sinks__ zobrazíte odkazy na další informace pro každou položku.
      >
      >
11. Vyberte **úlohy**a potom vyberte položku s názvem **00_000000**. Tento odkaz zobrazí **podrobnosti úlohy** pro tuto úlohu. Na této obrazovce můžete zobrazit **úloh čítače** a **pokusy o spuštění úkolu**.

    ![Detail úlohy](./media/hdinsight-debug-tez-ui/taskdetails.png)

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili, jak používat zobrazení Tez, další informace o [používání Hive s HDInsight](hadoop/hdinsight-use-hive.md).

Podrobné technické informace na Tez, najdete v článku [Tez stránku na Hortonworks](http://hortonworks.com/hadoop/tez/).
