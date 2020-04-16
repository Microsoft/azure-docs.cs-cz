---
title: Použití rozšířeného serveru historie Spark k ladění aplikací – Apache Spark v Azure Synapse
description: Rozšířený server historie Spark slouží k ladění a diagnostice aplikací Spark v Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4f03033942517f4778192e0b12f84610df8fd469
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429210"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Použití rozšířeného historického serveru Apache Spark k ladění a diagnostice aplikací Apache Spark

Tento článek obsahuje pokyny, jak používat rozšířený server historie Apache Spark k ladění a diagnostice dokončených a spuštěných aplikací Spark.

Rozšíření obsahuje kartu dat, kartu grafu a kartu **Data** diagnostiky. Karta **Graf** zobrazuje tok dat a přehrání grafu úloh. Karta **Diagnostika** zobrazuje **zkosení dat**, **časové zkosení**a **analýzu využití prováděcího modulu**.

## <a name="access-the-apache-spark-history-server"></a>Přístup k historickému serveru Apache Spark

Server historie Apache Spark je webové uživatelské rozhraní pro dokončené a spuštěné aplikace Spark. Webové rozhraní serveru historie Apache Spark můžete otevřít z Azure Synapse Analytics.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Otevření webového uživatelského uživatelského uživatelského uživatelského nastavení serveru Spark History Server z uzlu aplikací Apache spark

1. Otevřete [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Klepněte na **tlačítko Sledovat**a vyberte **položku Apache Spark Applications**.

    ![Klepněte na tlačítko Monitor a vyberte aplikaci pro jiskru.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Vyberte aplikaci a kliknutím na něj otevřete **protokolovací dotaz.**

    ![Otevřete okno dotazu protokolu.](./media/apache-spark-history-server/open-application-window.png)

4. Vyberte **server historie Spark**, pak se zobrazí webové uživatelské nastavení Serveru historie spark.

    ![Otevřete server historie jiskry.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>Otevření webového uživatelského uživatelského uživatelského uživatelského nastavení serveru Historie Spark z datového uzlu

1. Z poznámkového bloku Azure Synapse Studio vyberte **server historie Spark** z výstupní buňky spuštění úlohy nebo ze stavového panelu v dolní části dokumentu poznámkového bloku. Vyberte **Podrobnosti o relaci**.

   ![Spuštění serveru historie Spark](./media/apache-spark-history-server/launch-history-server2.png "Spuštění serveru historie Spark")

2. Z panelu vysouvání vyberte **server historie Spark.**

   ![Spuštění serveru historie Spark](./media/apache-spark-history-server/launch-history-server.png "Spuštění serveru historie Spark")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Seznamte se s kartou Data na serveru historie Spark

Vyberte ID úlohy pro úlohu, kterou chcete zobrazit. Pak vyberte **Data** v nabídce nástroje, abyste získali zobrazení dat. V této části se zobrazí, jak na kartě Data dělat různé úkoly.

* Zaškrtnutím karet vyberte **položky vstupy**, **výstupy**a **operace tabulky.**

    ![Data pro karty aplikací Spark](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Zkopírujte všechny řádky výběrem **možnosti Kopírovat**.

    ![Data pro kopírování aplikace Spark](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Uložte všechna data jako soubor CSV výběrem **csv**.

    ![Data pro uložení aplikace Spark](./media/apache-spark-history-server/apache-spark-data-save.png)

* Hledat zadáním klíčových slov do pole **Hledat**. Výsledky hledání se zobrazí okamžitě.

    ![Data pro vyhledávání aplikací Spark](./media/apache-spark-history-server/apache-spark-data-search.png)

* Vyberte záhlaví sloupce pro řazení tabulky, vyberte znaménko plus, chcete-li rozbalit řádek, chcete-li zobrazit další podrobnosti, nebo vyberte znaménko mínus, chcete-li řádek sbalit.

    ![Data pro tabulku aplikací Spark](./media/apache-spark-history-server/apache-spark-data-table.png)

* Stáhněte si jeden soubor výběrem **možnosti Částečné stažení**. Vybraný soubor se stáhne do místního. Pokud soubor již neexistuje, zobrazí se nová karta s chybovou zprávou.

    ![Data pro řádek stažení aplikace Spark](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Chcete-li zkopírovat úplnou nebo relativní cestu, vyberte volby **Kopírovat úplnou cestu** nebo **Kopírovat relativní cestu,** které se rozbalí z rozevírací nabídky. U souborů Azure Data Lake Storage **otevře Open v Azure Storage Explorer** u>>tu Azure Storage Explorer a vyhledá složku, když jste přihlášení.

    ![Data pro cestu ke kopírování aplikace Spark](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* Vyberte čísla stránek pod tabulkou pro navigaci na stránkách, pokud je na jedné stránce příliš mnoho řádků.

    ![Data pro stránku aplikace Spark](./media/apache-spark-history-server/apache-spark-data-page.png)

* Najeďte na otazník vedle **data,** chcete-li zobrazit popisek, nebo vyberte otazník, abyste získali další informace.

    ![Data pro aplikaci Spark více informací](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Pošlete zpětnou vazbu s problémy výběrem **poskytnout nám zpětnou vazbu**.

    ![Spark graf nám opět poskytne zpětnou vazbu](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Karta Graf na serveru historie Apache Spark

Vyberte ID úlohy pro úlohu, kterou chcete zobrazit. Potom vyberte **Graf** v nabídce nástroje, abyste získali zobrazení grafu úloh.

### <a name="overview"></a>Přehled

Přehled úlohy si můžete prohlédnout v grafu generované úlohy. Ve výchozím nastavení graf zobrazuje všechny úlohy. Toto zobrazení můžete filtrovat podle **ID úlohy**.

![ID úlohy aplikace Spark a grafu úlohy](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Displej

Ve výchozím nastavení je vybráno zobrazení **Průběh.** Tok dat můžete zkontrolovat výběrem **možnosti Číst** nebo **Zapsat** v rozevíracím seznamu **Zobrazit.**

![Zobrazení aplikace Spark a grafu úloh](./media/apache-spark-history-server/sparkui-graph-display.png)

Uzel grafu zobrazuje barvy zobrazené v legendě heatmapy.

![Aplikace Spark a heatmapa grafů úloh](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Přehrávání

Chcete-li úlohu přehrát, vyberte **možnost Přehrávání**. Chcete-li zastavit, můžete kdykoli vybrat **možnost Zastavit.** Barvy úkolů při přehrávání zobrazují různé stavy:

|Barvy|Význam|
|-|-|
|Green|Úspěšné: Úloha byla úspěšně dokončena.|
|Orange|Opakování: Instance úloh, které se nezdařily, ale nemají vliv na konečný výsledek úlohy. Tyto úkoly měly duplicitní nebo opakovat instance, které mohou být úspěšné později.|
|Blue|Spuštěno: Úloha je spuštěna.|
|White|Čekání nebo přeskočeno: Úloha čeká na spuštění nebo je fáze přeskočena.|
|Červený|Nezdařilo se: Úloha se nezdařila.|

Následující obrázek znázorňuje zelené, oranžové a modré stavové barvy.

![Ukázka barvy aplikace Spark a grafu úloh, spuštěná](./media/apache-spark-history-server/sparkui-graph-color-running.png)

Následující obrázek znázorňuje zelené a bílé stavové barvy.

![Ukázka barvy aplikace Spark a grafu úlohy, přeskočení](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

Následující obrázek znázorňuje červené a zelené stavové barvy.

![Ukázka barvy aplikace Spark a grafu úlohy se nezdařila](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> Přehrávání pro každou úlohu je povoleno. U neúplných úloh není přehrávání podporováno.

### <a name="zoom"></a>Zoom

Pomocí posouvání myší přibližte a oddalujte zobrazení v grafu úlohy nebo vyberte **možnost Lupa, aby se vešla** na obrazovku.

![Aplikace Spark a zvětšení grafu úloh y](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Popisy

Najeďte na uzel grafu, abyste viděli popisek, když došlo k neúspěšným úkolům, a vyberte vymezenou plochu, která otevře stránku vymezené plochy.

![Popisek aplikace Spark a grafu úlohy](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

Na kartě graf úlohy mají fáze popisek a malou ikonu zobrazenou, pokud mají úkoly, které splňují následující podmínky:

|Podmínka|Popis|
|-|-|
|Zkosení dat|velikost čtení dat > průměrná velikost čtení dat všech úkolů v této fázi * 2 a velikost čtení dat > 10 MB|
|Časové zkosení|doba provádění > průměrná doba provádění všech úkolů v této fázi * 2 a doba provádění > 2 minuty|
   
![Ikona zkosení aplikace Spark a grafu úloh](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Popis uzlu grafu

Uzel grafu úloh y zobrazuje následující informace o každé fázi:

  * Id.
  * Název nebo popis.
  * Celkové číslo úkolu
  * Čtení dat: součet vstupní velikosti a velikosti náhodného čtení.
  * Zápis dat: součet velikosti výstupu a velikosti zapamatovávaných zápisů.
  * Čas spuštění: čas mezi časem zahájení prvního pokusu a časem dokončení posledního pokusu.
  * Počet řádků: součet vstupních záznamů, výstupních záznamů, náhodného čtení záznamů a náhodného zápisu záznamů.
  * Pokroku.

    > [!NOTE]  
    > Ve výchozím nastavení se v uzlu grafu úlohy zobrazují informace z posledního pokusu každé fáze (s výjimkou doby provádění fáze). Během přehrávání však uzel grafu zobrazuje informace o každém pokusu.
    >  
    > Velikost dat čtení a zápisu je 1 MB = 1000 KB = 1000 * 1000 bajtů.

### <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Pošlete zpětnou vazbu s problémy výběrem **poskytnout nám zpětnou vazbu**.

![Zpětná vazba aplikace Spark a graf úloh](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Prozkoumejte kartu Diagnostika na serveru historie Apache Spark

Chcete-li získat přístup k kartě Diagnóza, vyberte ID úlohy. Pak v yberte **Diagnóza** v nabídce nástroje, abyste získali zobrazení Diagnostika úlohy. Karta Diagnostika obsahuje **zkosení dat**, **časové zkosení**a **analýzu využití prováděcího modulu**.

Výběrem karet zaškrtněte analýzu využití **data,** **časové zkosení**a **vykonavatele.**

![Karta Zkosení diagnostických dat SparkUI znovu](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Zkosení dat

Když vyberete kartu **Zkosení dat,** zobrazí se odpovídající zkosené úkoly na základě zadaných parametrů.

* **Zadejte parametry** - V první části se zobrazí parametry, které se používají ke zjištění zkosení dat. Výchozí pravidlo je: Čtení dat úloh je větší než třikrát přečtivá data průměrné úlohy a čtení dat úloh je více než 10 MB. Pokud chcete definovat vlastní pravidlo pro zkosené úkoly, můžete zvolit parametry, sekce **Zkosená plocha** a **Zkosení char** se odpovídajícím způsobem aktualizují.

* **Zkosená plocha** - Druhý oddíl zobrazuje fáze, které mají zkosené úkoly splňující výše uvedená kritéria. Pokud je ve fázi více než jeden zkosený úkol, zobrazí se v zkosené tabulce vymezené plochy pouze nejvíce zkosený úkol (například největší data pro zkosení dat).

    ![sparkui diagnostika data zkosení kartu](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Zkosení grafu** – Když je vybrán řádek v tabulce zkosení fáze, zkosený graf zobrazí další podrobnosti distribuce úkolů na základě čtení dat a času spuštění. Zkosené úkoly jsou označeny červeně a normální úkoly jsou označeny modře. Graf zobrazuje až 100 ukázkových úloh a podrobnosti o úkolu jsou zobrazeny v pravém dolním panelu.

    ![sparkui zkosení graf pro fázi 10](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Zkosení času

Karta **Zkosení času** zobrazuje zkosené úkoly na základě času provedení úkolu.

* **Zadejte parametry** - V první části se zobrazí parametry, které se používají k detekci časového zkosení. Výchozí kritéria pro detekci časového zkosení je: doba provádění úloh je větší než třikrát průměrná doba provádění a doba provádění úloh je větší než 30 sekund. Parametry můžete změnit podle svých potřeb. **Zkosená plocha** a **zkosený graf** zobrazují odpovídající fáze a informace o úkolech stejně jako na kartě **Zkosení dat** výše.

* Vyberte **Možnost Zkosení času**, pak se filtrovaný výsledek zobrazí v části **Zkosená vymezená plocha** podle parametrů nastavených v části Zadat **parametry**. Vyberte jednu položku v části **Zkosená plocha,** pak se odpovídající graf zpracuje v sekci3 a podrobnosti úkolu se zobrazí v pravém dolním panelu.

    ![sparkui diagnostika čas zkosení sekce](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Analýza využití prováděcího modulu

Graf využití prováděcího modulu vizualizuje přidělení a stav spuštění vykonavatele úlohy Spark.  

1. Vyberte **analýzu využití prováděcího modulu**, pak jsou vypracovány čtyři křivky typů týkající se využití prováděcího modulu, včetně **přidělených prováděcích modulů**, **spuštěných prováděcích modulů**, **nečinných exekutorů**a **maximálních instancí prováděcích modulů**. Pokud jde o přidělené vykonavatele, každá událost "Exekutor přidán" nebo "Exekutor odstraněn" zvyšuje nebo snižuje přidělené vykonavatele. Můžete zkontrolovat "Časová osa událostí" v záložce "Úlohy" pro další porovnání.

   ![sparkui diagnóza vykonavatelé kartu](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Vyberte ikonu barvy, kterou chcete vybrat, nebo zrušte výběr odpovídajícího obsahu ve všech pracovních úkosech.

    ![sparkui diagnostikuje vybrat graf](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Známé problémy

Vstupní a výstupní data pomocí odolných distribuovaných datových sad (RDD) se na kartě dat nezobrazují.

## <a name="next-steps"></a>Další kroky

- [Azure Synapse Analytics](../overview-what-is.md)
- [.NET pro dokumentaci Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

