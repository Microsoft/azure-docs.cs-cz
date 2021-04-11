---
title: Transformace dat pomocí toku dat mapování
description: V tomto kurzu najdete podrobné pokyny pro použití Azure Data Factory k transformaci dat pomocí toku dat s mapováním.
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2021
ms.openlocfilehash: 2363afc4c84ee7606410ceecd6819c12d0333dbb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563289"
---
# <a name="transform-data-using-mapping-data-flows"></a>Transformace dat s využitím mapování toků dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

V tomto kurzu použijete uživatelské rozhraní Azure Data Factory (UX) k vytvoření kanálu, který kopíruje a transformuje data Azure Data Lake Storage ze zdroje Gen2 (ADLS) do ADLS Gen2 jímky pomocí mapování toku dat. Konfigurační vzor v tomto kurzu se dá rozbalit při transformaci dat pomocí toku dat mapování.

 >[!NOTE]
   >Tento kurz je určen pro běžné mapování datových toků. Datové toky jsou k dispozici v Azure Data Factory i kanálech synapse. Pokud v kanálech Azure synapse začínáte toky dat, sledujte prosím [tok dat pomocí kanálů Azure synapse](../synapse-analytics/concepts-data-flow-overview.md) . 
   
V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvořte kanál s aktivitou toku dat.
> * Sestavte datový tok mapování se čtyřmi transformacemi.
> * Testovací spuštění kanálu
> * Monitorování aktivity toku dat

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet služby Azure Storage**. ADLS Storage použijete jako *zdrojová* úložiště a úložiště dat *jímky* . Pokud účet úložiště nemáte, přečtěte si téma [Vytvoření účtu služby Azure Storage](../storage/common/storage-account-create.md), kde najdete postup jeho vytvoření.

Soubor, který v tomto kurzu transformuje, je MoviesDB.csv, který najdete [tady](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Pokud chcete načíst soubor z GitHubu, zkopírujte obsah do textového editoru, který si zvolíte, a uložte ho místně jako soubor. csv. Postup nahrání souboru do účtu úložiště najdete v tématu [nahrání objektů BLOB pomocí Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Příklady budou odkazovat na kontejner s názvem Sample-data.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

V tomto kroku vytvoříte datovou továrnu a otevřete Data Factory UX pro vytvoření kanálu v datové továrně.

1. Otevřete **Microsoft Edge** nebo **Google Chrome**. V současné době je Data Factory uživatelské rozhraní podporováno pouze ve webových prohlížečích Microsoft Edge a Google Chrome.
2. V nabídce vlevo vyberte vytvořit data Factory **pro**  >  **integraci** prostředků  >  :

   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**.

   Název objektu pro vytváření dat Azure musí být *globálně jedinečný*. Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. (například yournameADFTutorialDataFactory). Pravidla pro pojmenovávání artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nová chybová zpráva objektu pro vytváření dat pro duplicitní název.":::
4. Vyberte **předplatné** Azure, v rámci kterého chcete datovou továrnu vytvořit.
5. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

    a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **vytvořit novou** a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md). 
6. Jako **Verzi** vyberte **V2**.
7. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou mohou být v jiných oblastech.
8. Vyberte **Vytvořit**.
9. Po dokončení vytváření se zobrazí oznámení v centru oznámení. Vyberte **Přejít k prostředku** a přejděte na stránku Datová továrna.
10. Vyberte **Vytvořit a monitorovat**. Na samostatné kartě se spustí uživatelské rozhraní služby Data Factory.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Vytvoření kanálu s aktivitou toku dat

V tomto kroku vytvoříte kanál, který obsahuje aktivitu toku dat.

1. Na stránce **Začínáme** vyberte **Vytvořit kanál**.

   ![Vytvoření kanálu](./media/doc-common-process/get-started-page.png)

1. Na kartě **Obecné** pro kanál zadejte **TransformMovies** pro **název** kanálu.
1. V horním panelu pro vytváření snímků nastavte posuvník **ladění toku dat** . Režim ladění umožňuje interaktivní testování logiky transformace proti clusteru Live Spark. Clustery toku dat zabírají 5-7 minut a uživatelům se doporučuje zapnout ladění jako první, pokud chtějí provádět vývoj toku dat. Další informace naleznete v tématu [režim ladění](concepts-data-flow-debug-mode.md).

    ![Aktivita toku dat](media/tutorial-data-flow/dataflow1.png)
1. V podokně **aktivity** rozbalte možnost **přesunout a transformovat** . Přetáhněte aktivitu **toku dat** z podokna na plátno kanálu.

    ![Snímek obrazovky zobrazující plátno kanálu, ve kterém můžete aktivitu toku dat vyřadit.](media/tutorial-data-flow/activity1.png)
1. V rozbalovacím seznamu **Přidat tok dat** vyberte **vytvořit nový tok dat** a potom pojmenujte svůj datový tok **TransformMovies**. Po dokončení klikněte na Dokončit.

    ![Snímek obrazovky, který ukazuje, kde se má tok dat pojmenovat při vytváření nového toku dat.](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Sestavení logiky transformace v plátně toku dat

Po vytvoření toku dat se automaticky pošle na plátno toku dat. V tomto kroku sestavíte tok dat, který přebírá moviesDB.csv v úložišti ADLS a agreguje Průměrné hodnocení mikrohodnot z 1910 do 2000. Pak tento soubor zapíšete zpátky do úložiště ADLS.

1. V plátně toku dat přidejte zdroj kliknutím do pole **Přidat zdroj** .

    ![Snímek obrazovky, který zobrazuje pole přidat zdroj](media/tutorial-data-flow/dataflow2.png)
1. Pojmenujte své zdrojové **MoviesDB**. Kliknutím na **Nový** vytvořte novou zdrojovou datovou sadu.

    ![Snímek obrazovky, který ukazuje, kde při pojmenování zdroje vyberete nový.](media/tutorial-data-flow/dataflow3.png)
1. Vyberte **Azure Data Lake Storage Gen2**. Klikněte na Pokračovat.

    ![Snímek obrazovky, který zobrazuje dlaždici Azure Data Lake Storage Gen2.](media/tutorial-data-flow/dataset1.png)
1. Vyberte **DelimitedText**. Klikněte na Pokračovat.

    ![Snímek obrazovky, který zobrazuje dlaždici DelimitedText](media/tutorial-data-flow/dataset2.png)
1. Pojmenujte datovou sadu **MoviesDB**. V rozevíracím seznamu propojená služba vyberte možnost **Nový**.

    ![Snímek obrazovky, který zobrazuje rozevírací seznam propojených služeb.](media/tutorial-data-flow/dataset3.png)
1. Na obrazovce pro vytvoření propojené služby pojmenujte ADLS propojené služby Gen2 pro **ADLSGen2** a zadejte metodu ověřování. Pak zadejte přihlašovací údaje pro připojení. V tomto kurzu používáme klíč účtu pro připojení k našemu účtu úložiště. Kliknutím na **Test připojení** můžete ověřit, že vaše přihlašovací údaje byly zadány správně. Po dokončení klikněte na vytvořit.

    ![Propojená služba](media/tutorial-data-flow/ls1.png)
1. Až budete zpátky na obrazovce vytváření datové sady, zadejte, kde se soubor nachází v poli **cesta k souboru** . V tomto kurzu se soubor moviesDB.csv nachází v části Container Sample-data. V případě, že soubor obsahuje záhlaví, zaregistrujte **první řádek jako záhlaví**. Vyberte možnost **z připojení/Store** a importujte schéma hlaviček přímo ze souboru v úložišti. Po dokončení klikněte na OK.

    ![Datové sady](media/tutorial-data-flow/dataset4.png)
1. Pokud je váš cluster ladění spuštěný, přejděte na kartu **Náhled dat** ve zdrojové transformaci a kliknutím na **aktualizovat** Získejte snímek dat. K ověření, zda je transformace nakonfigurována správně, můžete použít náhled dat.

    ![Snímek obrazovky, který ukazuje, kde můžete zobrazit náhled vašich dat a ověřit, jestli je správně nakonfigurovaná transformace](media/tutorial-data-flow/dataflow4.png)
1. Vedle zdrojového uzlu na plátně toku dat klikněte na ikonu Plus a přidejte novou transformaci. První transformace, kterou přidáváte, je **Filtr**.

    ![Plátno toku dat](media/tutorial-data-flow/dataflow5.png)
1. Pojmenujte transformaci filtru **FilterYears**. Kliknutím na pole výrazu vedle **filtru** otevřete Tvůrce výrazů. Tady zadáte podmínku filtrování.

    ![Snímek obrazovky, který zobrazuje pole Filtr na výrazu.](media/tutorial-data-flow/filter1.png)
1. Tvůrce výrazů toku dat umožňuje interaktivně vytvářet výrazy pro použití v různých transformacích. Výrazy mohou zahrnovat předdefinované funkce, sloupce ze vstupního schématu a uživatelsky definované parametry. Další informace o tom, jak sestavit výrazy, najdete v tématu [Tvůrce výrazů toku dat](concepts-data-flow-expression-builder.md).

    V tomto kurzu chcete filtrovat filmy komedie žánrů, které se nacházely mezi roky 1910 a 2000. Protože rok je aktuálně řetězec, je nutné jej převést na celé číslo pomocí ```toInteger()``` funkce. Použijte operátory větší než nebo rovno (>=) a menší než nebo rovno (<=) pro porovnání s hodnotami literálového roku 1910 a 200-. Sjednotte tyto výrazy spolu s operátorem and (&&). Výraz se vychází takto:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Chcete-li zjistit, které filmy jsou tyto, můžete použít ```rlike()``` funkci pro vyhledání vzoru "komedie" ve sloupcích žánrů. Sjednotí výraz rlike s porovnáním roků pro získání:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Pokud máte aktivní cluster ladění, můžete svoji logiku ověřit kliknutím na **aktualizovat** , aby se zobrazil výstup výrazu v porovnání s použitými vstupy. Existuje více než jedna správná odpověď na to, jak tuto logiku můžete dosáhnout pomocí jazyka výrazů toku dat.

    ![Filtrovat](media/tutorial-data-flow/filter2.png)

    Až budete s vaším výrazem hotovi, klikněte na **Uložit a dokončit** .

1. Načte **Náhled dat** , aby se ověřilo, že filtr funguje správně.

    ![Snímek obrazovky zobrazující náhled dat, který jste načetli.](media/tutorial-data-flow/filter3.png)
1. Další transformace, kterou přidáte, je **agregovaná** transformace v rámci **modifikátoru schématu**.

    ![Snímek obrazovky, který zobrazuje modifikátor agregovaného schématu.](media/tutorial-data-flow/agg1.png)
1. Pojmenujte agregovanou transformaci **AggregateComedyRatings**. Na kartě **Seskupit podle** vyberte z rozevíracího seznamu možnost **rok** a seskupte je podle roku, ve kterém byl film vydaný.

    ![Snímek obrazovky zobrazující možnost Year (rok) na kartě seskupit podle v části agregované nastavení.](media/tutorial-data-flow/agg2.png)
1. Přejít na kartu **agregace** . V levém textovém poli pojmenujte agregovaný sloupec **AverageComedyRating**. Klikněte na pravé pole výrazu a zadejte agregační výraz pomocí Tvůrce výrazů.

    ![Snímek obrazovky zobrazující možnost roku na kartě agregace v části agregované nastavení.](media/tutorial-data-flow/agg3.png)
1. Chcete-li získat průměr **hodnocení** sloupce, použijte ```avg()``` agregační funkci. Protože **hodnocení** je řetězec a ```avg()``` přebírá numerické vstupy, je nutné hodnotu převést na číslo prostřednictvím ```toInteger()``` funkce. Výraz vypadá takto:

    ```avg(toInteger(Rating))```

    Po dokončení klikněte na **Uložit a dokončit** .

    ![Snímek obrazovky zobrazující uložený výraz](media/tutorial-data-flow/agg4.png)
1. Výstup transformace zobrazíte tak, že přejdete na kartu **Náhled dat** . Všimněte si, že jsou k dispozici pouze dva sloupce, **rok** a **AverageComedyRating**.

    ![Agregace](media/tutorial-data-flow/agg3.png)
1. Dále chcete přidat transformaci **jímky** do **umístění cíl**.

    ![Snímek obrazovky, který ukazuje, kde přidat transformaci jímky v cíli](media/tutorial-data-flow/sink1.png)
1. Pojmenujte **jímku** jímky. Kliknutím na **Nový** Vytvořte datovou sadu jímky.

    ![Snímek obrazovky, který ukazuje, kde můžete pojmenovat jímku a vytvořit novou datovou sadu jímky.](media/tutorial-data-flow/sink2.png)
1. Vyberte **Azure Data Lake Storage Gen2**. Klikněte na Pokračovat.

    ![Snímek obrazovky zobrazující Azure Data Lake Storage Gen2 dlaždici, kterou si můžete vybrat.](media/tutorial-data-flow/dataset1.png)
1. Vyberte **DelimitedText**. Klikněte na Pokračovat.

    ![Datová sada](media/tutorial-data-flow/dataset2.png)
1. Pojmenujte datovou sadu jímky **MoviesSink**. V části propojená služba vyberte propojenou službu ADLS Gen2, kterou jste vytvořili v kroku 6. Zadejte výstupní složku, do které se budou zapisovat data. V tomto kurzu píšete do složky ' Output ' v kontejneru ' Sample-data '. Složka nemusí být předem k dispozici a je možné ji vytvořit dynamicky. Nastavte **první řádek jako záhlaví** jako true a pro **Import schématu** vyberte **None (žádné** ). Klikněte na Dokončit.

    ![Jímka](media/tutorial-data-flow/sink3.png)

Nyní jste dokončili vytváření toku dat. Jste připraveni ho spustit ve vašem kanálu.

## <a name="running-and-monitoring-the-data-flow"></a>Spuštění a monitorování toku dat

Kanál můžete ladit před jeho publikováním. V tomto kroku budete aktivovat ladicí běh kanálu toku dat. I když data Preview nezápisují data, spuštění ladění zapíše data do cíle jímky.

1. Přejít na plátno kanálu. Klikněte na **ladit** a aktivujte ladicí běh.

    ![Snímek obrazovky zobrazující plátno kanálu se zvýrazněným laděním](media/tutorial-data-flow/pipeline1.png)
1. Ladění kanálu aktivity toku dat používá aktivní ladicí cluster, ale při inicializaci trvá aspoň minutu. Průběh můžete sledovat přes kartu **výstup** . Po úspěšném spuštění klikněte na ikonu brýlí a otevřete podokno monitorování.

    ![Kanál](media/tutorial-data-flow/pipeline2.png)
1. V podokně monitorování můžete zobrazit počet řádků a čas strávený v každém kroku transformace.

    ![Snímek obrazovky, který zobrazuje podokno monitorování, kde vidíte počet řádků a čas strávený v jednotlivých krocích transformace.](media/tutorial-data-flow/pipeline3.png)
1. Kliknutím na transformaci získáte podrobné informace o sloupcích a vytváření oddílů dat.

    ![Monitorování](media/tutorial-data-flow/pipeline4.png)

Pokud jste postupovali podle tohoto kurzu správně, měli byste do složky jímky zapsat 83 řádků a 2 sloupce. Správnost dat můžete ověřit kontrolou úložiště objektů BLOB.

## <a name="next-steps"></a>Další kroky

Kanál v tomto kurzu spustí tok dat, který agreguje Průměrné hodnocení podílů z 1910 na 2000 a zapisuje data do ADLS. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvořte kanál s aktivitou toku dat.
> * Sestavte datový tok mapování se čtyřmi transformacemi.
> * Testovací spuštění kanálu
> * Monitorování aktivity toku dat

Přečtěte si další informace o [jazyce výrazů toku dat](data-flow-expression-functions.md).