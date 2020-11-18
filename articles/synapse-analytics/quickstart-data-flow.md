---
title: 'Rychlý Start: transformace dat pomocí toku dat mapování'
description: V tomto kurzu najdete podrobné pokyny pro použití analýzy Azure Synapse k transformaci dat pomocí toku dat s mapováním.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/03/2020
ms.openlocfilehash: 207679ad5b508b687c9cad372d144839fcaa501d
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94743843"
---
# <a name="quickstart-transform-data-using-mapping-data-flows"></a>Rychlý Start: transformace dat pomocí toků mapování dat

V tomto rychlém startu použijete Azure synapse Analytics k vytvoření kanálu, který transformuje data ze zdroje Azure Data Lake Storage Gen2 (ADLS Gen2) na ADLS Gen2 jímka pomocí mapování toku dat. Konfigurační vzor v tomto rychlém startu se dá rozbalit při transformaci dat pomocí toku dat mapování.

V tomto rychlém startu provedete následující kroky:

> [!div class="checklist"]
> * Vytvořte kanál s aktivitou toku dat ve službě Azure synapse Analytics.
> * Sestavte datový tok mapování se čtyřmi transformacemi.
> * Testovací spuštění kanálu
> * Monitorování aktivity toku dat

## <a name="prerequisites"></a>Předpoklady

* **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Pracovní prostor Azure synapse**: Vytvořte pracovní prostor Synapse pomocí Azure Portal podle pokynů v tématu [rychlý Start: vytvoření pracovního prostoru synapse](quickstart-create-workspace.md).
* **Účet úložiště Azure**: jako úložiště dat o *zdrojovém* a *jímky* použijte úložiště adls. Pokud účet úložiště nemáte, přečtěte si téma [Vytvoření účtu služby Azure Storage](../storage/common/storage-account-create.md), kde najdete postup jeho vytvoření.

    Soubor, který v tomto kurzu transformuje, je MoviesDB.csv, který najdete [tady](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Pokud chcete načíst soubor z GitHubu, zkopírujte obsah do textového editoru, který si zvolíte, a uložte ho místně jako soubor. csv. Postup nahrání souboru do účtu úložiště najdete v tématu [nahrání objektů BLOB pomocí Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Příklady budou odkazovat na kontejner s názvem Sample-data.

### <a name="navigate-to-the-synapse-studio"></a>Přejít na synapse Studio

Po vytvoření pracovního prostoru Azure synapse máte dva způsoby, jak otevřít synapse Studio:

* Otevřete pracovní prostor synapse ve [Azure Portal](https://ms.portal.azure.com/#home). V části Začínáme vyberte **otevřít** na kartě otevřít synapse Studio.
* Otevřete [Azure synapse Analytics](https://web.azuresynapse.net/) a přihlaste se ke svému pracovnímu prostoru.

V tomto rychlém startu používáme jako příklad pracovní prostor s názvem "adftest2020". Automaticky vás přejde na domovskou stránku synapse studia.

![Domovská stránka synapse Studio](media/doc-common-process/synapse-studio-home.png)

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Vytvoření kanálu s aktivitou toku dat

Kanál obsahuje logický tok pro spuštění sady aktivit. V této části vytvoříte kanál, který obsahuje aktivitu toku dat.

1. Přejít na kartu **integrace** . Vyberte ikonu plus vedle záhlaví kanály a vyberte kanál.

   ![Vytvořit nový kanál](media/doc-common-process/new-pipeline.png)

1. Na stránce nastavení **vlastností** kanálu zadejte **TransformMovies** pro **název**.

1. V části *přesunout a transformovat* v podokně *aktivity* přetáhněte **tok dat** na plátno kanálu.

1. V místní nabídce **Přidat stránku toku dat** vyberte možnost **vytvořit nový**  ->  **tok dat** toku dat. Po dokončení klikněte na **OK** .

   ![Vytvoření toku dat](media/quickstart-data-flow/new-data-flow.png)

1. Pojmenujte svůj datový tok **TransformMovies** na stránce **vlastností** .

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Sestavení logiky transformace v plátně toku dat

Po vytvoření toku dat se automaticky pošle na plátno toku dat. V tomto kroku sestavíte tok dat, který přebírá MoviesDB.csv v úložišti ADLS a agreguje Průměrné hodnocení mikrohodnot z 1910 do 2000. Pak tento soubor zapíšete zpátky do úložiště ADLS.

1. Nad plátnem toku dat se Posuňte posuvníkem **ladění toku dat** . Režim ladění umožňuje interaktivní testování logiky transformace proti clusteru Live Spark. Clustery toku dat zabírají 5-7 minut a uživatelům se doporučuje zapnout ladění jako první, pokud chtějí provádět vývoj toku dat. Další informace naleznete v tématu [režim ladění](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-debug-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

    ![Posunutí ladění na](media/quickstart-data-flow/debug-on.png)

1. V plátně toku dat přidejte zdroj kliknutím do pole **Přidat zdroj** .

1. Pojmenujte své zdrojové **MoviesDB**. Kliknutím na **Nový** vytvořte novou zdrojovou datovou sadu.

    ![Vytvořit novou zdrojovou datovou sadu](media/quickstart-data-flow/new-source-dataset.png)

1. Vyberte **Azure Data Lake Storage Gen2**. Klikněte na Pokračovat.

    ![Zvolit Azure Data Lake Storage Gen2](media/quickstart-data-flow/select-source-dataset.png)

1. Vyberte **DelimitedText**. Klikněte na Pokračovat.

1. Pojmenujte datovou sadu **MoviesDB**. V rozevíracím seznamu propojená služba vyberte možnost **Nový**.

1. Na obrazovce pro vytvoření propojené služby pojmenujte ADLS Gen2 propojených služeb **ADLSGen2** a zadejte metodu ověřování. Pak zadejte přihlašovací údaje pro připojení. V tomto rychlém startu používáme klíč účtu pro připojení k našemu účtu úložiště. Kliknutím na **Test připojení** můžete ověřit, že vaše přihlašovací údaje byly zadány správně. Po dokončení klikněte na **vytvořit** .

    ![Vytvoření propojené služby zdroje](media/quickstart-data-flow/adls-gen2-linked-service.png)

1. Až budete zpátky na obrazovce vytváření datové sady, v poli **cesta k souboru** zadejte, kde se nachází soubor. V tomto rychlém startu je soubor "MoviesDB.csv" umístěný v kontejneru "Sample-data". V případě, že soubor obsahuje záhlaví, zaregistrujte **první řádek jako záhlaví**. Vyberte možnost **z připojení/Store** a importujte schéma hlaviček přímo ze souboru v úložišti. Po dokončení klikněte na **OK** .

    ![Nastavení zdrojové datové sady](media/quickstart-data-flow/source-dataset-properties.png)

1. Pokud je váš cluster ladění spuštěný, přejděte na kartu **Náhled dat** ve zdrojové transformaci a kliknutím na **aktualizovat** Získejte snímek dat. K ověření, zda je transformace nakonfigurována správně, můžete použít náhled dat.

    ![Náhled dat](media/quickstart-data-flow/data-preview.png)

1. Vedle zdrojového uzlu na plátně toku dat klikněte na ikonu Plus a přidejte novou transformaci. První transformace, kterou přidáváte, je **Filtr**.

    ![Přidání filtru](media/quickstart-data-flow/add-filter.png)

1. Pojmenujte transformaci filtru **FilterYears**. Kliknutím na pole výrazu vedle **filtru** otevřete Tvůrce výrazů. Tady zadáte podmínku filtrování.

1. Tvůrce výrazů toku dat umožňuje interaktivně vytvářet výrazy pro použití v různých transformacích. Výrazy mohou zahrnovat předdefinované funkce, sloupce ze vstupního schématu a uživatelsky definované parametry. Další informace o tom, jak sestavit výrazy, najdete v tématu [Tvůrce výrazů toku dat](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

    V tomto rychlém startu chcete filtrovat filmy komedie žánrů, které se nacházely mezi roky 1910 a 2000. Protože rok je aktuálně řetězec, je nutné jej převést na celé číslo pomocí ```toInteger()``` funkce. Použijte operátory větší než nebo rovno (>=) a menší než nebo rovno (<=) pro porovnání s hodnotami literálového roku 1910 a 200-. Sjednotte tyto výrazy spolu s operátorem and (&&). Výraz se vychází takto:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Chcete-li zjistit, které filmy jsou tyto, můžete použít ```rlike()``` funkci pro vyhledání vzoru "komedie" ve sloupcích žánrů. Sjednotí výraz rlike s porovnáním roků pro získání:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    ![Zadat podmínku filtrování](media/quickstart-data-flow/visual-expression-builder.png)

    Pokud máte aktivní cluster ladění, můžete svoji logiku ověřit kliknutím na **aktualizovat** , aby se zobrazil výstup výrazu v porovnání s použitými vstupy. Existuje více než jedna správná odpověď na to, jak tuto logiku můžete dosáhnout pomocí jazyka výrazů toku dat.

    Až budete s vaším výrazem hotovi, klikněte na **Uložit a dokončit** .

1. Načte **Náhled dat** , aby se ověřilo, že filtr funguje správně.

1. Další transformace, kterou přidáte, je **agregovaná** transformace v rámci **modifikátoru schématu**.

    ![Přidat agregaci](media/quickstart-data-flow/add-aggregate.png)

1. Pojmenujte agregovanou transformaci **AggregateComedyRatings**. Na kartě **Seskupit podle** vyberte z rozevíracího seznamu možnost **rok** a seskupte je podle roku, ve kterém byl film vydaný.

    ![Agregovaná nastavení 1](media/quickstart-data-flow/aggregate-settings.png)

1. Přejít na kartu **agregace** . V levém textovém poli pojmenujte agregovaný sloupec **AverageComedyRating**. Klikněte na pravé pole výrazu a zadejte agregační výraz pomocí Tvůrce výrazů.

    ![Agregovaná nastavení 2](media/quickstart-data-flow/aggregate-settings-2.png)

1. Chcete-li získat průměr **hodnocení** sloupce, použijte ```avg()``` agregační funkci. Protože **hodnocení** je řetězec a ```avg()``` přebírá numerické vstupy, je nutné hodnotu převést na číslo prostřednictvím ```toInteger()``` funkce. Tento výraz vypadá takto:

    ```avg(toInteger(Rating))```

    Po dokončení klikněte na **Uložit a dokončit** .

    ![Průměrné hodnocení komedie](media/quickstart-data-flow/average-comedy-rating.png)

1. Výstup transformace zobrazíte tak, že přejdete na kartu **Náhled dat** . Všimněte si, že jsou k dispozici pouze dva sloupce, **rok** a **AverageComedyRating**.

    ![Náhled agregovaných dat](media/quickstart-data-flow/transformation-output.png)

1. Dále chcete přidat transformaci **jímky** do **umístění cíl**.

    ![Přidat jímku](media/quickstart-data-flow/add-sink.png)

1. Pojmenujte **jímku** jímky. Kliknutím na **Nový** Vytvořte datovou sadu jímky.

1. Vyberte **Azure Data Lake Storage Gen2**. Klikněte na Pokračovat.

1. Vyberte **DelimitedText**. Klikněte na Pokračovat.

1. Pojmenujte datovou sadu jímky **MoviesSink**. V části propojená služba vyberte propojenou službu ADLS Gen2, kterou jste vytvořili v kroku 7. Zadejte výstupní složku, do které se budou zapisovat data. V tomto rychlém startu zapisujeme do složky ' Output ' v kontejneru ' Sample-data '. Složka nemusí být předem k dispozici a je možné ji vytvořit dynamicky. Nastavte **první řádek jako záhlaví** jako true a pro **Import schématu** vyberte **None (žádné** ). Po dokončení klikněte na **OK** .

    ![Vlastnosti datové sady jímky](media/quickstart-data-flow/sink-dataset-properties.png)

Nyní jste dokončili vytváření toku dat. Jste připraveni ho spustit ve vašem kanálu.

## <a name="running-and-monitoring-the-data-flow"></a>Spuštění a monitorování toku dat

Kanál můžete ladit před jeho publikováním. V tomto kroku budete aktivovat ladicí běh kanálu toku dat. I když data Preview nezápisují data, spuštění ladění zapíše data do cíle jímky.

1. Přejít na plátno kanálu. Klikněte na **ladit** a aktivujte ladicí běh.

    ![Kanál ladění](media/quickstart-data-flow/debug-pipeline.png)

1. Ladění kanálu aktivity toku dat používá aktivní ladicí cluster, ale při inicializaci trvá aspoň minutu. Průběh můžete sledovat přes kartu **výstup** . Po úspěšném spuštění klikněte na ikonu brýlí a otevřete podokno monitorování.

    ![Výstup ladění](media/quickstart-data-flow/debugging-output.png)

1. V podokně monitorování můžete zobrazit počet řádků a čas strávený v každém kroku transformace.

    ![Monitorování transformace](media/quickstart-data-flow/4-transformations.png)

1. Kliknutím na transformaci získáte podrobné informace o sloupcích a vytváření oddílů dat.

    ![Podrobnosti transformace](media/quickstart-data-flow/transformation-details.png)

Pokud jste postupovali podle tohoto rychlého startu správně, měli byste do složky jímky zapsat 83 řádků a 2 sloupce. Data můžete ověřit kontrolou úložiště objektů BLOB.


## <a name="next-steps"></a>Další kroky

V následujících článcích se dozvíte o podpoře Azure synapse Analytics:

> [!div class="nextstepaction"]
> [Kanál a aktivity](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 
>  Přehled toku dat [mapování](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 
>  [Jazyk výrazu toku dat](https://docs.microsoft.com/azure/data-factory/data-flow-expression-functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
