---
title: Transformace dat pomocí toku mapových dat
description: Tento kurz obsahuje podrobné pokyny pro použití Azure Data Factory k transformaci dat pomocí toku dat mapování
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: e6ca8007a96cc63b51b4f79b69029cbf0799e71c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979179"
---
# <a name="transform-data-using-mapping-data-flows"></a>Transformace dat pomocí mapových toků dat

Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

V tomto kurzu použijete uživatelské rozhraní Azure Data Factory (UX) k vytvoření kanálu, který kopíruje a transformuje data ze zdroje Gen2 služby Azure Data Lake Storage (ADLS) do jímky ADLS Gen2 pomocí toku dat mapování. Vzor konfigurace v tomto kurzu lze rozbalit při transformaci dat pomocí toku dat mapování

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvořte kanál s aktivitou toku dat.
> * Vytvořte tok dat mapování se čtyřmi transformacemi.
> * Testovací spuštění kanálu
> * Sledování aktivity toku dat

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet úložiště Azure**. Úložiště ADLS se používá jako *zdrojové* úložiště a úložiště dat *jímky.* Pokud účet úložiště nemáte, přečtěte si téma [Vytvoření účtu služby Azure Storage](../storage/common/storage-account-create.md), kde najdete postup jeho vytvoření.

Soubor, který transformujeme v tomto kurzu je MoviesDB.csv, který lze nalézt [zde](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Chcete-li soubor načíst z GitHubu, zkopírujte obsah do textového editoru podle vašeho výběru a uložte místně jako soubor .csv. Pokud chcete soubor nahrát do svého účtu úložiště, přečtěte [si tématu Nahrávání objektů BLOB s Portálem Azure](../storage/blobs/storage-quickstart-blobs-portal.md). Příklady budou odkazovat na kontejner s názvem "ukázková data".

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

V tomto kroku vytvoříte továrnu dat a otevřete ux datové továrny a vytvoříte kanál v datové továrně.

1. Otevřete **Microsoft Edge** nebo **Google Chrome**. V současné době je uživatelské rozhraní Data Factory podporováno pouze ve webových prohlížečích Microsoft Edge a Google Chrome.
2. V levé nabídce vyberte Vytvořit**datovou továrnu****Analytics** >  **:** > 

   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**.

   Název objektu pro vytváření dat Azure musí být *globálně jedinečný*. Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. (například yournameADFTutorialDataFactory). Pravidla pro pojmenovávání artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).

     ![Nová datová továrna](./media/doc-common-process/name-not-available-error.png)
4. Vyberte **předplatné** Azure, v rámci kterého chcete datovou továrnu vytvořit.
5. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

    a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **Vytvořit nový**a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md). 
6. Jako **Verzi** vyberte **V2**.
7. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například Azure Storage a SQL Database) a výpočetní prostředky (například Azure HDInsight) používané v datové továrně můžou být v jiných oblastech.
8. Vyberte **Vytvořit**.
9. Po dokončení vytváření se zobrazí oznámení v centru oznámení. Chcete-li přejít **na zdroj,** přejděte na stránku Datové toky.
10. Vyberte **Vytvořit a monitorovat**. Na samostatné kartě se spustí uživatelské rozhraní služby Data Factory.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Vytvoření kanálu s aktivitou toku dat

V tomto kroku vytvoříte kanál, který obsahuje aktivitu toku dat.

1. Na stránce **Začínáme** vyberte **Vytvořit kanál**.

   ![Vytvoření kanálu](./media/doc-common-process/get-started-page.png)

1. Na kartě **Obecné** pro kanál zadejte **TransformMovies** pro **název** kanálu.
1. V horním panelu výroby posuňte jezdec **ladění Tok dat.** Režim ladění umožňuje interaktivní testování transformační logiky proti živému clusteru Spark. Clustery toku dat se zahřívají 5 až 7 minut a uživatelům se doporučuje nejprve zapnout ladění, pokud plánují vývoj toku dat. Další informace naleznete [v tématu Režim ladění](concepts-data-flow-debug-mode.md).

    ![Aktivita toku dat](media/tutorial-data-flow/dataflow1.png)
1. V podokně **Aktivity** rozbalte akordeon **Přesunout a Transformovat.** Přetáhněte aktivitu **toku dat** z podokna na plátno kanálu.

    ![Aktivita toku dat](media/tutorial-data-flow/activity1.png)
1. V rozbalovacím panelu **Přidání toku dat** vyberte **Vytvořit nový tok dat** a pojmenujte tok dat **TransformMovies**. Po dokončení klikněte na Dokončit.

    ![Aktivita toku dat](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Vytvoření transformační logiky na plátně toku dat

Jakmile vytvoříte tok dat, budete automaticky odesláni na plátno toku dat. V tomto kroku vytvoříte tok dat, který přebírá moviesDB.csv v úložišti ADLS a agreguje průměrné hodnocení komedií od roku 1910 do roku 2000. Potom zapíšete tento soubor zpět do úložiště ADLS.

1. Na plátně toku dat přidejte zdroj kliknutím na pole **Přidat zdroj.**

    ![Plátno toku dat](media/tutorial-data-flow/dataflow2.png)
1. Pojmenujte zdroj **MoviesDB**. Kliknutím na **Nový** vytvořte novou zdrojovou datovou sadu.

    ![Plátno toku dat](media/tutorial-data-flow/dataflow3.png)
1. Zvolte **Azure Data Lake Storage Gen2**. Klikněte na Pokračovat.

    ![Datová sada](media/tutorial-data-flow/dataset1.png)
1. Zvolte **Oddělovač textu**. Klikněte na Pokračovat.

    ![Datová sada](media/tutorial-data-flow/dataset2.png)
1. Pojmenujte svou datovou sadu **MoviesDB**. V rozevíracím souboru propojené služby zvolte **Nový**.

    ![Datová sada](media/tutorial-data-flow/dataset3.png)
1. Na obrazovce vytvoření propojené služby pojmenujte propojenou službu ADLS gen2 **ADLSGen2** a zadejte metodu ověřování. Potom zadejte přihlašovací údaje k připojení. V tomto kurzu používáme klíč účtu pro připojení k našemu účtu úložiště. Klepnutím na tlačítko **Testovat připojení** ověřte, zda byla pověření zadána správně. Po dokončení klikněte na Vytvořit.

    ![Propojená služba](media/tutorial-data-flow/ls1.png)
1. Až se vrátíte na obrazovku pro vytváření datové sady, zadejte, kde se soubor nachází, pod polem **Cesta k souboru.** V tomto kurzu soubor moviesDB.csv je umístěn v ukázkových datech kontejneru. Vzhledem k tomu, že soubor obsahuje záhlaví, zaškrtněte **políčko První řádek jako záhlaví**. Chcete-li importovat schéma záhlaví přímo ze souboru v úložišti, vyberte **možnost Z připojení nebo úložiště.** Po dokončení klepněte na tlačítko OK.

    ![Datové sady](media/tutorial-data-flow/dataset4.png)
1. Pokud byl váš ladicí cluster spuštěn, přejděte na kartu **Náhled dat** zdrojové transformace a kliknutím na **Aktualizovat** získáte snímek dat. Náhled dat můžete použít k ověření správné konfigurace transformace.

    ![Plátno toku dat](media/tutorial-data-flow/dataflow4.png)
1. Vedle zdrojového uzlu na plátně toku dat klikněte na ikonu plus a přidejte novou transformaci. První transformace, kterou přidáváte, je **filtr**.

    ![Plátno toku dat](media/tutorial-data-flow/dataflow5.png)
1. Pojmenujte transformaci filtru **FilterYears**. Kliknutím na pole výrazu vedle **možnosti Filtr** na otevřete tvůrce výrazů. Zde zadáte podmínku filtrování.

    ![Filtr](media/tutorial-data-flow/filter1.png)
1. Tvůrce výrazů toku dat umožňuje interaktivně vytvářet výrazy pro použití v různých transformacích. Výrazy mohou obsahovat vestavěné funkce, sloupce ze vstupního schématu a parametry definované uživatelem. Další informace o vytváření výrazů naleznete v [tématu Tvůrce výrazů toku dat](concepts-data-flow-expression-builder.md).

    V tomto tutoriálu chcete filtrovat filmy žánrové komedie, která vyšla mezi lety 1910 a 2000. Jako rok je v současné době řetězec, je třeba převést na celé číslo pomocí ```toInteger()``` funkce. Použijte operátory větší než nebo rovno (>=) a menší nebo rovno (<=) operátory k porovnání s literálovými hodnotami roku 1910 a 200-. Unie tyto výrazy společně s a (&&) operátor. Výraz vyjde takto:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Chcete-li zjistit, které filmy jsou ```rlike()``` komedie, můžete použít funkci najít vzor 'Komedie' ve sloupci žánrů. Unie rlike výraz s rokem srovnání získat:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Pokud máte aktivní ladicí cluster, můžete ověřit svou logiku klepnutím na **tlačítko Aktualizovat** a zobrazit výstup výrazu ve srovnání s použitými vstupy. Existuje více než jedna správná odpověď na to, jak můžete tuto logiku provést pomocí jazyka toku dat.

    ![Filtr](media/tutorial-data-flow/filter2.png)

    Až budete s výrazem hotovi, klikněte na **Uložit a dokončit.**

1. Načtením **náhledu dat** ověříte, zda filtr funguje správně.

    ![Filtr](media/tutorial-data-flow/filter3.png)
1. Další transformace, kterou přidáte, je **agregační** transformace pod **modifikátorem schématu**.

    ![Agregace](media/tutorial-data-flow/agg1.png)
1. Pojmenujte svou agregotní transformaci **AggregateComedyRatings**. V kartě **Seskupit** podle vyberte **rok** z rozbalovací ho skupování agregací podle roku, kdy film vyšel.

    ![Agregace](media/tutorial-data-flow/agg2.png)
1. Přejděte na kartu **Agregace.** V levém textovém poli pojmenujte agregační sloupec **AverageComedyRating**. Kliknutím na pravé pole výrazu zadejte agregační výraz prostřednictvím tvůrce výrazů.

    ![Agregace](media/tutorial-data-flow/agg3.png)
1. Chcete-li získat **Rating**průměr sloupce ```avg()``` Hodnocení , použijte agregační funkci. Vzhledem k **tomu, že hodnocení** je řetězec a ```avg()``` přijímá číselný ```toInteger()``` vstup, musíme hodnotu převést na číslo prostřednictvím funkce. Toto je výraz vypadá takto:

    ```avg(toInteger(Rating))```

    Po dokončení klikněte na **Uložit a dokončit.**

    ![Agregace](media/tutorial-data-flow/agg4.png)
1. Přejděte na kartu **Náhled dat** a zobrazte výstup transformace. Všimněte si, že jsou tam pouze dva sloupce, **rok** a **AverageComedyRating**.

    ![Agregace](media/tutorial-data-flow/agg3.png)
1. Dále chcete přidat transformace **jímky** v části **Cíl**.

    ![Jímka](media/tutorial-data-flow/sink1.png)
1. Pojmenujte dřez **umyvadlo**. Chcete-li vytvořit datovou sadu jímky, klepněte na **tlačítko Nový.**

    ![Jímka](media/tutorial-data-flow/sink2.png)
1. Zvolte **Azure Data Lake Storage Gen2**. Klikněte na Pokračovat.

    ![Datová sada](media/tutorial-data-flow/dataset1.png)
1. Zvolte **Oddělovač textu**. Klikněte na Pokračovat.

    ![Datová sada](media/tutorial-data-flow/dataset2.png)
1. Pojmenujte datovou sadu jímky **MoviesSink**. Pro propojené služby zvolte propojenou službu ADLS gen2, kterou jste vytvořili v kroku 6. Zadejte výstupní složku, do které chcete zapisovat data. V tomto kurzu píšeme do složky 'výstup' v kontejneru 'ukázková data'. Složka nemusí existovat předem a může být dynamicky vytvořena. Nastavte **první řádek jako záhlaví** jako true a pro schéma **importu**vyberte **žádný** . Klikněte na Dokončit.

    ![Jímka](media/tutorial-data-flow/sink3.png)

Nyní jste dokončili vytváření toku dat. Jste připraveni spustit v potrubí.

## <a name="running-and-monitoring-the-data-flow"></a>Spuštění a sledování toku dat

Před publikováním můžete ladit kanál. V tomto kroku se chystáte spustit ladění kanálu toku dat. Zatímco náhled dat nezapisuje data, spuštění ladění zapíše data do cíle jímky.

1. Přejděte na plátno potrubí. Chcete-li spustit ladění, klepněte na tlačítko **Ladění.**

    ![Kanál](media/tutorial-data-flow/pipeline1.png)
1. Ladění kanálu aktivit toku dat používá aktivní ladicí cluster, ale přesto trvá alespoň minutu inicializovat. Průběh můžete sledovat na kartě **Výstup.** Jakmile je běh úspěšný, klikněte na ikonu brýlí a otevřete podokno monitorování.

    ![Kanál](media/tutorial-data-flow/pipeline2.png)
1. V podokně monitorování můžete zobrazit počet řádků a čas strávený v každém kroku transformace.

    ![Monitorování](media/tutorial-data-flow/pipeline3.png)
1. Kliknutím na transformaci získáte podrobné informace o sloupcích a rozdělení dat.

    ![Monitorování](media/tutorial-data-flow/pipeline4.png)

Pokud jste postupovali podle tohoto kurzu správně, měli byste mít zapsány 83 řádků a 2 sloupce do složky jímky. Můžete ověřit, že data jsou správná kontrolou úložiště objektů blob.

## <a name="next-steps"></a>Další kroky

Kanál v tomto kurzu spustí tok dat, který agreguje průměrné hodnocení komedie od 1910 do 2000 a zapíše data do ADLS. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvořte kanál s aktivitou toku dat.
> * Vytvořte tok dat mapování se čtyřmi transformacemi.
> * Testovací spuštění kanálu
> * Sledování aktivity toku dat

Další informace o [jazyce výrazu toku dat](data-flow-expression-functions.md).
