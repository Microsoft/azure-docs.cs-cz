---
title: Transformace dat pomocí Azure Data Factory spravovaného toku dat mapování virtuální sítě
description: V tomto kurzu najdete podrobné pokyny pro použití Azure Data Factory k transformaci dat pomocí toku dat s mapováním.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/19/2019
ms.openlocfilehash: 5515f6c4dfbc5d3c0e391373e763597d7fdc89ed
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531322"
---
# <a name="transform-data-securely-using-mapping-data-flows"></a>Zabezpečená transformace dat pomocí mapování toků dat

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).

V tomto kurzu použijete uživatelské rozhraní Azure Data Factory (UX) k vytvoření kanálu, který kopíruje a transformuje data **Azure Data Lake Storage ze zdroje Gen2 (adls) do adls Gen2 jímky (povolením přístupu pouze k vybraným sítím)** pomocí mapování toku dat v [Azure Data Factorych spravovaných Virtual Network](managed-virtual-network-private-endpoint.md). Konfigurační vzor v tomto kurzu se dá při transformaci dat pomocí mapování toku dat rozbalit.

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
>
> * Vytvoření datové továrny
> * Vytvořte kanál s aktivitou toku dat.
> * Sestavte datový tok mapování se čtyřmi transformacemi.
> * Testovací spuštění kanálu
> * Monitorování aktivity toku dat

## <a name="prerequisites"></a>Předpoklady
* **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet služby Azure Storage**. ADLS Storage použijete jako *zdrojová* úložiště a úložiště dat *jímky* . Pokud účet úložiště nemáte, přečtěte si téma [Vytvoření účtu služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal), kde najdete postup jeho vytvoření. **Ujistěte se, že účet úložiště povoluje přístup jenom z vybraných sítí.** 

Soubor, který v tomto kurzu transformuje, je MoviesDB.csv, který najdete [tady](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Pokud chcete načíst soubor z GitHubu, zkopírujte obsah do textového editoru, který si zvolíte, a uložte ho místně jako soubor. csv. Postup nahrání souboru do účtu úložiště najdete v tématu [nahrání objektů BLOB pomocí Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Příklady budou odkazovat na kontejner s názvem Sample-data.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

V tomto kroku vytvoříte datovou továrnu a otevřete Data Factory UX pro vytvoření kanálu v datové továrně.

1. Otevřete **Microsoft Edge** nebo **Google Chrome**. V současné době je Data Factory uživatelské rozhraní podporováno pouze ve webových prohlížečích Microsoft Edge a Google Chrome.
2. V nabídce vlevo vyberte **vytvořit**  >  **Analytics**  >  **Data Factory**analýzy prostředků.
3. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**.

   Název objektu pro vytváření dat Azure musí být *globálně jedinečný*. Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. (například yournameADFTutorialDataFactory). Pravidla pro pojmenovávání artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).

4. Vyberte **předplatné** Azure, v rámci kterého chcete datovou továrnu vytvořit.
5. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

    a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **vytvořit novou**a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md). 
6. Jako **Verzi** vyberte **V2**.
7. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou mohou být v jiných oblastech.

8. Vyberte **Vytvořit**.

9. Po dokončení vytváření se zobrazí oznámení v centru oznámení. Vyberte **Přejít k prostředku** a přejděte na stránku Datová továrna.
10. Vyberte **Vytvořit a monitorovat**. Na samostatné kartě se spustí uživatelské rozhraní služby Data Factory.

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>Vytvoření Azure Integration Runtime ve spravovaném ADF Virtual Network
V tomto kroku vytvoříte Azure Integration Runtime a povolíte spravované Virtual Network.

1. V portálu ADF přejděte na **Spravovat centrum** a klikněte na **Nový** a vytvořte nový Azure Integration runtime.
   ![Vytvořit nový Azure Integration Runtime](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. Vyberte, chcete-li vytvořit Integration Runtime **Azure** .
   ![Nový Azure Integration Runtime](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. Povolte **Virtual Network**.
   ![Nový Azure Integration Runtime](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. Vyberte **Vytvořit**.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Vytvoření kanálu s aktivitou toku dat

V tomto kroku vytvoříte kanál, který obsahuje aktivitu toku dat.

1. Na stránce **Začínáme** vyberte **Vytvořit kanál**.

   ![Vytvoření kanálu](./media/doc-common-process/get-started-page.png)

1. V podokně **vlastnosti** kanálu zadejte **TransformMovies** pro **název** kanálu.
1. V horním panelu pro vytváření snímků nastavte posuvník **ladění toku dat** . Režim ladění umožňuje interaktivní testování logiky transformace proti clusteru Live Spark. Clustery toku dat zabírají 5-7 minut a uživatelům se doporučuje zapnout ladění jako první, pokud chtějí provádět vývoj toku dat. Další informace naleznete v tématu [režim ladění](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-debug-mode).

    ![Ladění toku dat](media/tutorial-data-flow-private/dataflow-debug.png)
1. V podokně **aktivity** rozbalte možnost **přesunout a transformovat** . Přetáhněte aktivitu **toku dat** z podokna na plátno kanálu.

1. V místní nabídce **Přidat tok dat** vyberte **vytvořit nový tok dat** a pak vyberte **mapování toku dat**. Po dokončení klikněte na **OK** .

    ![Mapování toku dat](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Pojmenujte svůj **TransformMovies** toku dat v podokně Vlastnosti.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Sestavení logiky transformace v plátně toku dat

Po vytvoření toku dat se automaticky pošle na plátno toku dat. V tomto kroku sestavíte tok dat, který přebírá moviesDB.csv v úložišti ADLS a agreguje Průměrné hodnocení mikrohodnot z 1910 do 2000. Pak tento soubor zapíšete zpátky do úložiště ADLS.

### <a name="add-the-source-transformation"></a>Přidat zdrojovou transformaci

V tomto kroku nastavíte Azure Data Lake Storage Gen2 jako zdroj.

1. V plátně toku dat přidejte zdroj kliknutím do pole **Přidat zdroj** .

1. Pojmenujte své zdrojové **MoviesDB**. Kliknutím na **Nový** vytvořte novou zdrojovou datovou sadu.

1. Vyberte **Azure Data Lake Storage Gen2**. Klikněte na Pokračovat.

1. Vyberte **DelimitedText**. Klikněte na Pokračovat.

1. Pojmenujte datovou sadu **MoviesDB**. V rozevíracím seznamu propojená služba vyberte možnost **Nový**.

1. Na obrazovce pro vytvoření propojené služby pojmenujte ADLS propojené služby Gen2 pro **ADLSGen2** a zadejte metodu ověřování. Pak zadejte přihlašovací údaje pro připojení. V tomto kurzu používáme klíč účtu pro připojení k našemu účtu úložiště. 

1. Ujistěte se, že jste povolili **interaktivní vytváření**. Povolení může trvat přibližně 1 minutu.

    ![Interaktivní vytváření](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Vyberte **Test připojení**, který by neměl selhat, protože účet úložiště nepovoluje přístup k němu bez vytváření a schvalování privátního koncového bodu. V chybové zprávě byste měli vidět odkaz pro vytvoření **privátního koncového bodu** , pomocí kterého můžete vytvořit spravovaný privátní koncový bod. *Alternativou je přejít přímo na kartu Správa a postupovat podle pokynů v [této části](#create-a-managed-private-endpoint) a vytvořit spravovaný privátní koncový bod.*

1. Nechejte dialogové okno otevřené a pak klikněte na účet úložiště, který jste vybrali výše.

1. Postupujte podle pokynů v [této části](#approval-of-a-private-link-in-storage-account) pro schválení privátního odkazu.

1. Vraťte se do dialogového okna. **Otestujte připojení** znovu a vyberte **vytvořit** a nasaďte propojenou službu.

1. Až budete zpátky na obrazovce vytváření datové sady, zadejte, kde se soubor nachází v poli **cesta k souboru** . V tomto kurzu se soubor moviesDB.csv nachází v části Container Sample-data. V případě, že soubor obsahuje záhlaví, zaregistrujte **první řádek jako záhlaví**. Vyberte možnost **z připojení/Store** a importujte schéma hlaviček přímo ze souboru v úložišti. Po dokončení klikněte na OK.

    ![Zdrojová cesta](media/tutorial-data-flow-private/source-file-path.png)

1. Pokud je váš cluster ladění spuštěný, přejděte na kartu **Náhled dat** ve zdrojové transformaci a kliknutím na **aktualizovat** Získejte snímek dat. K ověření, zda je transformace nakonfigurována správně, můžete použít náhled dat.

    ![Náhled dat](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Vytvoření spravovaného privátního koncového bodu

Pokud jste nepoužili odkaz na hypertextový odkaz při testování výše uvedeného připojení, použijte následující cestu. Teď je potřeba vytvořit spravovaný privátní koncový bod, ke kterému se připojíte přes propojenou službu vytvořenou výše.

1. Přejít na kartu spravovat.
> [!NOTE]
> Karta spravovat nemusí být k dispozici pro všechny instance služby Data Factory. Pokud ho nevidíte, můžete i nadále přistupovat k privátním koncovým bodům prostřednictvím karty**Autor**– >**připojení**-->**privátního koncového bodu**.
1. Přejít do oddílu spravované privátní koncové body.
1. V části spravované privátní koncové body vyberte **+ Nový** .

    ![Nový spravovaný privátní koncový bod](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. V seznamu Vyberte dlaždici Azure Data Lake Storage Gen2 a vyberte **pokračovat**.
1. Zadejte název účtu úložiště, který jste vytvořili výše.
1. Vyberte **Vytvořit**.
1. Měli byste vidět, že se po chvíli čekání na vytvoření privátního odkazu vyžaduje schválení.
1. Vyberte privátní koncový bod, který jste vytvořili výše. Zobrazí se hypertextový odkaz, který vás provede schválením privátního koncového bodu na úrovni účtu úložiště.

    ![Správa privátního koncového bodu](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>Schválení privátního odkazu v účtu úložiště

1. V účtu úložiště v části Nastavení klikněte na **připojení privátního koncového bodu** .

1. Zaznačte si privátní koncový bod, který jste vytvořili, a vyberte **schválit**.

    ![Schválit soukromý koncový bod](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Přidejte popis a klikněte na **Ano**.
1. Vraťte se do části **spravované soukromé koncové body** na kartě **Spravovat** v Azure Data Factory.
1. Může trvat přibližně 1 minutu, aby se schválení projevilo pro váš soukromý koncový bod.

### <a name="add-the-filter-transformation"></a>Přidat transformaci filtru

1. Vedle zdrojového uzlu na plátně toku dat klikněte na ikonu Plus a přidejte novou transformaci. První transformace, kterou přidáváte, je **Filtr**.

    ![Přidat filtr](media/tutorial-data-flow-private/add-filter.png)
1. Pojmenujte transformaci filtru **FilterYears**. Kliknutím na pole výrazu vedle **filtru** otevřete Tvůrce výrazů. Tady zadáte podmínku filtrování.

    ![Filtrovat roky](media/tutorial-data-flow-private/filter-years.png)
1. Tvůrce výrazů toku dat umožňuje interaktivně vytvářet výrazy pro použití v různých transformacích. Výrazy mohou zahrnovat předdefinované funkce, sloupce ze vstupního schématu a uživatelsky definované parametry. Další informace o tom, jak sestavit výrazy, najdete v tématu [Tvůrce výrazů toku dat](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder).

    * V tomto kurzu chcete filtrovat filmy komedie žánrů, které se nacházely mezi roky 1910 a 2000. Protože rok je aktuálně řetězec, je nutné jej převést na celé číslo pomocí ```toInteger()``` funkce. Použijte operátory větší než nebo rovno (>=) a menší než nebo rovno (<=) pro porovnání s hodnotami literálového roku 1910 a 200-. Sjednotte tyto výrazy spolu s operátorem and (&&). Výraz se vychází takto:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Chcete-li zjistit, které filmy jsou tyto, můžete použít ```rlike()``` funkci pro vyhledání vzoru "komedie" ve sloupcích žánrů. Sjednotí výraz rlike s porovnáním roků pro získání:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Pokud máte aktivní cluster ladění, můžete svoji logiku ověřit kliknutím na **aktualizovat** , aby se zobrazil výstup výrazu v porovnání s použitými vstupy. Existuje více než jedna správná odpověď na to, jak tuto logiku můžete dosáhnout pomocí jazyka výrazů toku dat.

        ![Výraz filtru](media/tutorial-data-flow-private/filter-expression.png)

    * Až budete s vaším výrazem hotovi, klikněte na **Uložit a dokončit** .

1. Načte **Náhled dat** , aby se ověřilo, že filtr funguje správně.

    ![Náhled dat filtru](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Přidat agregovanou transformaci

1. Další transformace, kterou přidáte, je **agregovaná** transformace v rámci **modifikátoru schématu**.

    ![Přidat agregaci](media/tutorial-data-flow-private/add-aggregate.png)
1. Pojmenujte agregovanou transformaci **AggregateComedyRatings**. Na kartě **Seskupit podle** vyberte z rozevíracího seznamu možnost **rok** a seskupte je podle roku, ve kterém byl film vydaný.

    ![Agregovaná skupina](media/tutorial-data-flow-private/group-by-year.png)
1. Přejít na kartu **agregace** . V levém textovém poli pojmenujte agregovaný sloupec **AverageComedyRating**. Klikněte na pravé pole výrazu a zadejte agregační výraz pomocí Tvůrce výrazů.

    ![Název agregovaného sloupce](media/tutorial-data-flow-private/name-column.png)
1. Chcete-li získat průměr **hodnocení**sloupce, použijte ```avg()``` agregační funkci. Protože **hodnocení** je řetězec a ```avg()``` přebírá numerické vstupy, je nutné hodnotu převést na číslo prostřednictvím ```toInteger()``` funkce. Výraz vypadá takto:

    ```avg(toInteger(Rating))```

    Po dokončení klikněte na **Uložit a dokončit** .

    ![Uložit agregované](media/tutorial-data-flow-private/save-aggregate.png)
1. Výstup transformace zobrazíte tak, že přejdete na kartu **Náhled dat** . Všimněte si, že jsou k dispozici pouze dva sloupce, **rok** a **AverageComedyRating**.

### <a name="add-the-sink-transformation"></a>Přidat transformaci jímky

1. Dále chcete přidat transformaci **jímky** do **umístění cíl**.

    ![Přidat jímku](media/tutorial-data-flow-private/add-sink.png)
1. Pojmenujte **jímku**jímky. Kliknutím na **Nový** Vytvořte datovou sadu jímky.

    ![Vytvořit jímku](media/tutorial-data-flow-private/create-sink.png)
1. Na stránce Nová datová sada vyberte možnost **Azure Data Lake Storage Gen2**. Klikněte na Pokračovat.

1. Na stránce vybrat formát zvolte možnost **DelimitedText**. Klikněte na Pokračovat.

1. Pojmenujte datovou sadu jímky **MoviesSink**. Pro propojenou službu vyberte stejnou propojenou službu ADLSGen2, kterou jste vytvořili pro transformaci zdroje. Zadejte výstupní složku, do které se budou zapisovat data. V tomto kurzu píšete do složky ' Output ' v kontejneru ' Sample-data '. Složka nemusí být předem k dispozici a je možné ji vytvořit dynamicky. Nastavte **první řádek jako záhlaví** jako true a pro **Import schématu**vyberte **None (žádné** ). Klikněte na tlačítko OK.

    ![Cesta jímky](media/tutorial-data-flow-private/sink-file-path.png)

Nyní jste dokončili vytváření toku dat. Jste připraveni ho spustit ve vašem kanálu.

## <a name="running-and-monitoring-the-data-flow"></a>Spuštění a monitorování toku dat

Kanál můžete ladit před jeho publikováním. V tomto kroku budete aktivovat ladicí běh kanálu toku dat. I když data Preview nezápisují data, spuštění ladění zapíše data do cíle jímky.

1. Přejít na plátno kanálu. Klikněte na **ladit** a aktivujte ladicí běh.

1. Ladění kanálu aktivity toku dat používá aktivní ladicí cluster, ale při inicializaci trvá aspoň minutu. Průběh můžete sledovat přes kartu **výstup** . Po úspěšném spuštění klikněte na ikonu brýlí, kde najdete podrobnosti o spuštění.

1. Na stránce podrobností vidíte počet řádků a čas strávený v každém kroku transformace.

    ![Sledování spuštění](media/tutorial-data-flow-private/run-details.png)
1. Kliknutím na transformaci získáte podrobné informace o sloupcích a vytváření oddílů dat.

Pokud jste postupovali podle tohoto kurzu správně, měli byste do složky jímky zapsat 83 řádků a 2 sloupce. Správnost dat můžete ověřit kontrolou úložiště objektů BLOB.

## <a name="summary"></a>Souhrn

V tomto kurzu použijete uživatelské rozhraní Azure Data Factory (UX) k vytvoření kanálu, který kopíruje a transformuje data **Azure Data Lake Storage ze zdroje Gen2 (adls) do adls Gen2 jímky (povolením přístupu pouze k vybraným sítím)** pomocí mapování toku dat v [Azure Data Factorych spravovaných Virtual Network](managed-virtual-network-private-endpoint.md).
