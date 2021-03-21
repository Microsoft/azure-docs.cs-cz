---
title: Použití serveru rozšířených historií Spark k ladění aplikací
description: Pomocí rozšířeného serveru pro historii Spark můžete ladit a diagnostikovat aplikace Spark v Azure synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4471fed7b423fe05147db30afe57f6c845fe640e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670694"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Použití serveru historie rozšířených Apache Spark k ladění a diagnostice Apache Spark aplikací

V tomto článku najdete pokyny k použití serveru historie rozšířených Apache Spark k ladění a diagnostice dokončených a spuštěných aplikací Spark.

Rozšíření obsahuje kartu data, kartu grafu a kartu Diagnostika. Na kartě **data** můžete kontrolovat vstupní a výstupní data úlohy Spark. Na kartě **graf** se zobrazuje tok dat a přehrávání grafu úloh. Na kartě **Diagnostika** se zobrazuje  **zešikmení dat**, **zkosení času** a **Analýza využití prováděcího modulu**.

## <a name="access-the-apache-spark-history-server"></a>Přístup k serveru historie Apache Sparku

Server historie Apache Spark je webové uživatelské rozhraní pro dokončené a běžící aplikace Spark. Z analýzy Azure synapse můžete otevřít webové rozhraní serveru historie Apache Spark.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Otevřete webové uživatelské rozhraní serveru historie Sparku z uzlu aplikace Apache Spark.

1. Otevřete [Azure synapse Analytics](https://web.azuresynapse.net/).

2. Vyberte **monitorování** a pak vyberte **Apache Spark aplikace**.

    ![Vyberte monitorování a pak vyberte aplikace Spark.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Vyberte aplikaci a pak otevřete **dotaz protokolu** tak, že ho vyberete.

    ![Otevřete okno dotazu protokolu.](./media/apache-spark-history-server/open-application-window.png)

4. Vyberte možnost **Server historie Spark** a pak se zobrazí webové uživatelské rozhraní serveru historie Spark.

    ![Otevřete Server historie Spark.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>Otevření webového uživatelského rozhraní serveru historie Sparku z datového uzlu

1. Z vašeho poznámkového bloku Azure synapse Studio vyberte z výstupní buňky spuštění úlohy možnost **Server historie Spark** nebo z panelu stav na konci dokumentu poznámkového bloku. Vyberte **Podrobnosti o relaci**.

   ![Spustit historii Spark serveru 1](./media/apache-spark-history-server/launch-history-server2.png "Spustit server historie Sparku")

2. Z panelu snímku vyberte možnost **Server historie Spark** .

   ![Spustit historii Spark serveru 2](./media/apache-spark-history-server/launch-history-server.png "Spustit server historie Sparku")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Prozkoumat kartu data na serveru historie Spark

Vyberte ID úlohy pro úlohu, kterou chcete zobrazit. Pak v nabídce Nástroje vyberte **data** , aby se zobrazila data. V této části se dozvíte, jak provádět různé úkoly na kartě data.

* Zkontrolujte **vstupy** a **výstupy** a **operace s tabulkami** , a to tak, že karty vyberete samostatně.

    ![Data pro karty aplikace Spark](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Zkopírujte všechny řádky výběrem možnosti **Kopírovat**.

    ![Data pro kopii aplikace Spark](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Uložte všechna data jako soubor CSV tak, že vyberete **CSV**.

    ![Data pro uložení aplikace Spark](./media/apache-spark-history-server/apache-spark-data-save.png)

* Hledejte zadáním klíčových slov do pole **Hledat**. Výsledky hledání se zobrazí okamžitě.

    ![Data pro vyhledávání aplikace Spark](./media/apache-spark-history-server/apache-spark-data-search.png)

* Vyberte záhlaví sloupce k seřazení tabulky, vyberte znaménko plus pro rozbalení řádku, aby se zobrazily další podrobnosti, nebo vyberte znaménko mínus pro sbalení řádku.

    ![Data pro tabulku aplikace Spark](./media/apache-spark-history-server/apache-spark-data-table.png)

* Výběrem **částečného stahování** Stáhněte jeden soubor. Vybraný soubor je stažen do místní. Pokud soubor už neexistuje, zobrazí se nová karta s chybovou zprávou.

    ![Data pro řádek pro stažení aplikace Spark](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Chcete-li zkopírovat úplnou cestu nebo relativní cestu, vyberte možnosti **Kopírovat úplnou cestu** nebo **Kopírovat relativní cestu** , která se rozbalí z rozevírací nabídky. Pro Azure Data Lake Storage soubory **otevřete v Průzkumník služby Azure Storage** spustí Průzkumník služby Azure Storage a po přihlášení se složka vyhledá.

    ![Data pro kopírovací cestu aplikace Spark](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* Vyberte čísla stránek pod tabulkou, pokud chcete zobrazit stránky, když je na jedné stránce zobrazeno příliš mnoho řádků.

    ![Data pro stránku aplikace Spark](./media/apache-spark-history-server/apache-spark-data-page.png)

* Najeďte myší na otazník vedle položky **data** , abyste zobrazili popis, nebo vyberte otazník a získejte další informace.

    ![Data pro aplikaci Spark další informace](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Sdělte nám svůj názor s problémy tím, že si vyberete poslat **svůj názor**.

    ![Sparkový graf nám zadejte zpětnou vazbu](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Karta graf na serveru historie Apache Spark

Vyberte ID úlohy pro úlohu, kterou chcete zobrazit. Pak v nabídce Nástroje vyberte **graf** , abyste získali zobrazení grafu úlohy.

### <a name="overview"></a>Přehled

Přehled úlohy můžete zobrazit v grafu vygenerované úlohy. Ve výchozím nastavení se v grafu zobrazují všechny úlohy. Toto zobrazení můžete filtrovat podle **ID úlohy**.

![ID úlohy grafu aplikace Spark a grafu úlohy](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Zobrazení

Ve výchozím nastavení je vybraná možnost zobrazení **průběhu** . Tok dat můžete kontrolovat výběrem možnosti **číst** nebo **napsané** v rozevíracím seznamu **zobrazení** .

![Zobrazení aplikace Spark a grafu úlohy](./media/apache-spark-history-server/sparkui-graph-display.png)

Uzel grafu zobrazuje barvy zobrazené v legendě heatmapu.

![Heatmapu grafu aplikací a úloh Spark](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Přehrávání

Pokud chcete úlohu přehrát, vyberte **přehrávání**. Kdykoli můžete zastavit výběr **zastavit** . Barvy úlohy při přehrávání zobrazují různé stavy:

|Barva|Význam|
|-|-|
|Green|Úspěch: úloha se úspěšně dokončila.|
|Oranžový|Opakování: instance úloh, které selhaly, ale neovlivňují konečný výsledek úlohy. Tyto úlohy měly duplicitní nebo opakované instance, které mohou být později úspěšné.|
|Blue|Spuštěno: úloha je spuštěná.|
|White|Čekání nebo přeskočení: úloha čeká na spuštění, nebo se fáze přeskočila.|
|Red|Nezdařilo se: úloha se nezdařila.|

Následující obrázek ukazuje zelenou, oranžovou a modrou stavovou barvu.

![Ukázka barvy grafu aplikace Spark a grafu úlohy, která běží](./media/apache-spark-history-server/sparkui-graph-color-running.png)

Následující obrázek ukazuje zelenou a bílou stavovou barvu.

![Ukázka barvy grafu aplikace Spark a grafu úlohy, přeskočit](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

Na následujícím obrázku jsou červené a zelené stavové barvy.

![Ukázka barvy grafu aplikace Spark a grafu úlohy, selhání](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> Přehrávání jednotlivých úloh je povoleno. Pro nedokončené úlohy se přehrávání nepodporuje.

### <a name="zoom"></a>Zoom

Pomocí rolovacího posuvníku se můžete přiblížit nebo oddálit v grafu úlohy nebo můžete vybrat možnost **přiblížení** , aby se vešlo na obrazovku.

![Přiblížení grafu aplikace Spark a grafu úloh](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Popisy

Když najedete myší na uzel grafu, zobrazí se popis tlačítka, když dojde k neúspěšným úlohám, a vyberte fázi pro otevření stránky fáze.

![Popis aplikace Spark a grafu úlohy](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

Na kartě graf úlohy mají fáze popis a zobrazí se malá ikona, pokud má úlohy, které splňují následující podmínky:

|Podmínka|Description|
|-|-|
|Zkosit data|velikost přečtených dat > Průměrná velikost čtení dat všech úkolů v této fázi * 2 a velikost pro čtení dat > 10 MB|
|Časové zkosení|čas spuštění > Průměrná doba provádění všech úkolů v této fázi * 2 a doba spuštění > 2 minuty|
   
![Ikona zkosení grafu aplikace Spark a grafu úlohy](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Popis uzlu grafu

Uzel grafu úlohy zobrazí následující informace o každé fázi:

  * Účet.
  * Název nebo popis
  * Celkové číslo úlohy
  * Přečtená data: součet velikosti vstupní velikosti a náhodné velikosti čtení.
  * Zápis dat: součet velikosti výstupu a náhodné velikosti zápisů.
  * Doba spuštění: čas mezi časem zahájení prvního pokusu a časem dokončení posledního pokusu.
  * Počet řádků: součet vstupních záznamů, výstupních záznamů, náhodného čtení záznamů a náhodného zápisu záznamů.
  * Přejde.

    > [!NOTE]  
    > Ve výchozím nastavení uzel grafu úlohy zobrazuje informace z posledního pokusu o každou fázi (s výjimkou doby spuštění fáze). Během přehrávání ale uzel grafu zobrazuje informace o každém pokusu.
    >  
    > Velikost dat čtení a zápisu je 1 MB = 1000 KB = 1000 * 1000 bajtů.

### <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Sdělte nám svůj názor s problémy tím, že si vyberete poslat **svůj názor**.

![Zpětná vazba grafu aplikace Spark a úlohy](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Prozkoumat kartu Diagnostika v serveru historie Apache Spark

Chcete-li získat přístup na kartu diagnostika, vyberte ID úlohy. Pak v nabídce Nástroje vyberte **diagnóza** a získejte zobrazení diagnostiky úlohy. Karta Diagnostika zahrnuje analýzu **dat**, **zkosení času** a **analýzy využití prováděcího modulu**.

Výběrem karet v uvedeném pořadí ověřte, jak se mají **Přezkosit data**, **Zkosit** a **analyzovat využití prováděcích** modulů.

![SparkUI karta pro zkosení dat diagnostiky](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Zkosit data

Když vyberete kartu **zkosení dat** , zobrazí se na základě zadaných parametrů odpovídající šikmé úkoly.

* **Zadejte parametry** – první oddíl zobrazuje parametry, které se používají ke zjištění zkosení dat. Výchozí pravidlo je: čtení dat úkolu je více než třikrát průměrně přečtených dat úkolu a přečtených dat úlohy je více než 10 MB. Pokud chcete definovat vlastní pravidlo pro zkreslené úkoly, můžete zvolit parametry. Oddíly **nakloněné fáze** a **zkosení znaků** se odpovídajícím způsobem aktualizují.

* **Zkosený stupeň** – druhý oddíl zobrazuje fáze, které mají úlohy, které splňují kritéria uvedená výše. Pokud je ve fázi více než jeden zkosený úkol, tabulka zkosených fází zobrazí pouze nejvýraznější úlohu (například největší data pro zkosení dat).

    ![sparkui karta pro zkosení dat diagnostiky](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Zkosit graf** – když je vybraný řádek v tabulce fáze zkosení, zobrazí se v grafu zkosení více podrobností distribuce úkolů na základě doby čtení a spuštění dat. Zkreslené úkoly jsou označeny červeně a normální úlohy jsou označeny modře. V grafu se zobrazí ukázkové úkoly až 100 a podrobnosti o úloze se zobrazí v pravém dolním panelu.

    ![graf zkosení sparkui pro fázi 10](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Časové zkosení

Na kartě **časové zkosení** se zobrazují zkreslené úkoly na základě doby spuštění úkolu.

* **Zadejte parametry** – první oddíl zobrazuje parametry, které se používají ke zjištění zešikmení času. Výchozí kritéria pro detekci časového zkosení jsou: doba provádění úlohy je větší než tři časy doby spuštění a doba provádění úlohy je větší než 30 sekund. Parametry můžete změnit podle svých potřeb. Graf **zkosených fází** a **zkosení** zobrazuje odpovídající informace o fázích a úlohách stejně jako karta pro **zkosení dat** výše.

* Vyberte možnost **časové zkosení** a následně filtrovaný výsledek se zobrazí v části **zkosený fáze** podle parametrů nastavených v oddílu **určení parametrů**. Vyberte jednu položku v části **šikmá fáze** . odpovídající graf je koncept v section3 a podrobnosti úlohy se zobrazí v pravém dolním panelu.

    ![oddíl asymetrie doby diagnostiky sparkui](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Analýza využití prováděcího modulu

Graf využití vykonavatele vizualizuje stav přidělení a spuštění prováděcího modulu úlohy Spark.  

1. Vyberte možnost **Analýza využití prováděcího modulu**, pak čtyři typy křivky o použití prováděcího modulu jsou koncepty, včetně **přidělených prováděcích** modulů, **spuštěných** prováděcích modulů, **nečinných prováděcích** modulů a **maximálního počtu instancí prováděcí** V případě přidělených prováděcích modulů se každý "vykonavatel" přidaný "nebo" vykonavatel "odebírá" nebo snižuje přidělené prováděcí moduly. Pro lepší porovnání můžete zaškrtnout "Časová osa události" na kartě úlohy.

   ![karta prováděcích modulů diagnostiky sparkui](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Vyberte ikonu barvy a vyberte nebo zrušte výběr odpovídajícího obsahu ve všech konceptech.

    ![sparkui diagnostikovat výběr grafu](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Známé problémy

Vstupní a výstupní data s využitím odolných datových sad (RDD) se na kartě data nezobrazují.

## <a name="next-steps"></a>Další kroky

- [Azure Synapse Analytics](../overview-what-is.md)
- [Dokumentace k rozhraní .NET pro Apache Spark](/dotnet/spark)

