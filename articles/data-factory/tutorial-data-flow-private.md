---
title: Transformace dat pomocí Azure Data Factory spravovaného toku dat mapování virtuální sítě
description: V tomto kurzu najdete podrobné pokyny pro použití Azure Data Factory k transformaci dat pomocí toků mapování dat.
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: ad101bee84256662d1436ba8d8a49304aecb9129
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518269"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>Zabezpečená transformace dat pomocí mapování toku dat

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](./introduction.md).

V tomto kurzu použijete Data Factory uživatelské rozhraní (UI) k vytvoření kanálu, který kopíruje a transformuje data *ze zdroje Azure Data Lake Storage Gen2 do data Lake Storage Gen2 jímky (povolením přístupu pouze k vybraným sítím)* pomocí mapování toku dat v [Data Factory spravovaných Virtual Network](managed-virtual-network-private-endpoint.md). Při transformaci dat pomocí mapování toku dat můžete v tomto kurzu rozbalit konfigurační vzor.

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
>
> * Vytvoření datové továrny
> * Vytvořte kanál s aktivitou toku dat.
> * Sestavte datový tok mapování se čtyřmi transformacemi.
> * Testovací spuštění kanálu
> * Monitorování aktivity toku dat.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet služby Azure Storage**. Používáte Data Lake Storage jako *zdrojová* úložiště a úložiště dat *jímky* . Pokud účet úložiště nemáte, přečtěte si téma [Vytvoření účtu služby Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal), kde najdete postup jeho vytvoření. *Ujistěte se, že účet úložiště povoluje přístup jenom z vybraných sítí.* 

Soubor, který v tomto kurzu transformuje, je moviesDB.csv, který najdete na tomto [webu obsahu GitHubu](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Pokud chcete načíst soubor z GitHubu, zkopírujte obsah do textového editoru, který si zvolíte, a uložte ho místně jako soubor. csv. Postup nahrání souboru do účtu úložiště najdete v tématu [nahrání objektů BLOB pomocí Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Příklady budou odkazovat na kontejner s názvem **Sample-data**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

V tomto kroku vytvoříte datovou továrnu a otevřete Data Factory uživatelské rozhraní pro vytvoření kanálu v datové továrně.

1. Otevřete Microsoft Edge nebo Google Chrome. V současné době podporuje Data Factory uživatelské rozhraní pouze webové prohlížeče Microsoft Edge a Google Chrome.
1. V nabídce vlevo vyberte **vytvořit**  >    >  **Data Factory** analýzy prostředků.
1. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**.

   Název datové továrny musí být *globálně jedinečný*. Pokud se zobrazí chybová zpráva s názvem hodnota, zadejte jiný název objektu pro vytváření dat (například yournameADFTutorialDataFactory). Pravidla pro pojmenovávání artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).

1. Vyberte **předplatné** Azure, v rámci kterého chcete datovou továrnu vytvořit.
1. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

    * Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.
    * Vyberte **vytvořit novou** a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md). 
1. Jako **Verzi** vyberte **V2**.
1. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například Azure Storage a Azure SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou mohou být v jiných oblastech.

1. Vyberte **Vytvořit**.
1. Po dokončení vytváření se zobrazí oznámení v centru oznámení. Vyberte **Přejít k prostředku** a přejít na stránku **Data Factory** .
1. Vyberte **Vytvořit a monitorovat**. Na samostatné kartě se spustí uživatelské rozhraní služby Data Factory.

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Vytvoření Azure IR v Data Factory spravovaném Virtual Network

V tomto kroku vytvoříte Azure IR a povolíte Data Factory spravované Virtual Network.

1. Na portálu Data Factory klikněte na **Spravovat** a vyberte **Nový** , aby se vytvořila nová Azure IR.

   ![Snímek obrazovky, který ukazuje vytvoření nového Azure IR.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Na stránce **instalace prostředí Integration runtime** vyberte, který modul runtime integrace se má vytvořit na základě požadovaných možností. V tomto kurzu vyberte **Azure, v** místním prostředí a potom klikněte na **pokračovat**. 
1. Vyberte **Azure** a potom kliknutím na **pokračovat** vytvořte prostředí Azure Integration runtime.

   ![Snímek obrazovky, který zobrazuje novou Azure IR.](./media/tutorial-copy-data-portal-private/azure-ir.png)

1. V části **Konfigurace virtuální sítě (Preview)** vyberte **Povolit**.

   ![Snímek obrazovky, který ukazuje povolení nového Azure IR.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)

1. Vyberte **Vytvořit**.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Vytvoření kanálu s aktivitou toku dat

V tomto kroku vytvoříte kanál, který obsahuje aktivitu toku dat.

1. Na stránce **Začínáme** vyberte **Vytvořit kanál**.

   ![Snímek obrazovky, který ukazuje vytvoření kanálu.](./media/doc-common-process/get-started-page.png)

1. V podokně vlastnosti kanálu zadejte **TransformMovies** pro název kanálu.
1. V podokně **aktivity** rozbalte možnost **přesunout a transformovat**. Přetáhněte aktivitu **toku dat** z podokna na plátno kanálu.

1. V místní nabídce **Přidat tok dat** vyberte **vytvořit nový tok dat** a pak vyberte **mapování toku dat**. Až budete hotovi, vyberte **OK** .

    ![Snímek obrazovky zobrazující tok dat mapování](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Pojmenujte svůj datový tok **TransformMovies** v podokně Vlastnosti.
1. V horním panelu plátna kanálu posunutí posuvníku **ladění toku dat** zapnuto. Režim ladění umožňuje interaktivní testování logiky transformace proti clusteru Live Spark. Clustery toku dat zabírají 5-7 minut a uživatelům se doporučuje zapnout ladění jako první, pokud chtějí provádět vývoj toku dat. Další informace naleznete v tématu [režim ladění](concepts-data-flow-debug-mode.md).

    ![Snímek obrazovky zobrazující posuvník ladění toku dat](media/tutorial-data-flow-private/dataflow-debug.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Sestavení logiky transformace v plátně toku dat

Po vytvoření toku dat se automaticky pošle na plátno toku dat. V tomto kroku vytvoříte tok dat, který převezme soubor moviesDB.csv v Data Lake Storage a agreguje Průměrné hodnocení podílů z 1910 na 2000. Potom tento soubor zapíšete zpět do Data Lake Storage.

### <a name="add-the-source-transformation"></a>Přidat zdrojovou transformaci

V tomto kroku nastavíte Data Lake Storage Gen2 jako zdroj.

1. V plátně toku dat přidejte zdroj výběrem pole **Přidat zdroj** .

1. Pojmenujte své zdrojové **MoviesDB**. Pokud chcete vytvořit novou zdrojovou datovou sadu, vyberte **Nový** .

1. Vyberte **Azure Data Lake Storage Gen2** a pak vyberte **pokračovat**.

1. Vyberte **DelimitedText** a pak vyberte **pokračovat**.

1. Pojmenujte datovou sadu **MoviesDB**. V rozevíracím seznamu propojená služba vyberte **Nový**.

1. Na obrazovce pro vytvoření propojené služby pojmenujte Data Lake Storage Gen2 propojených služeb **ADLSGen2** a zadejte metodu ověřování. Pak zadejte přihlašovací údaje pro připojení. V tomto kurzu používáme **klíč účtu** pro připojení k našemu účtu úložiště. 

1. Ujistěte se, že jste povolili **interaktivní vytváření**. Povolení může trvat minutu.

    ![Snímek obrazovky zobrazující interaktivní vytváření obsahu](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Vyberte **Test připojení**. Nemělo by to selhat, protože účet úložiště nepovoluje přístup k němu bez vytvoření a schválení privátního koncového bodu. V chybové zprávě byste měli vidět odkaz pro vytvoření privátního koncového bodu, pomocí kterého můžete vytvořit spravovaný privátní koncový bod. Alternativou je přejít přímo na kartu **Správa** a postupovat podle pokynů v [této části](#create-a-managed-private-endpoint) a vytvořit spravovaný privátní koncový bod.

1. Nechejte dialogové okno otevřené a pak na svém účtu úložiště.

1. Postupujte podle pokynů v [této části](#approval-of-a-private-link-in-a-storage-account) pro schválení privátního odkazu.

1. Vraťte se do dialogového okna. Znovu vyberte **Test připojení** a vyberte **vytvořit** a nasaďte propojenou službu.

1. Na obrazovce vytváření datové sady zadejte, kde se nachází soubor v poli **cesta k souboru** . V tomto kurzu se soubor moviesDB.csv nachází v **ukázce kontejneru – data**. Vzhledem k tomu, že soubor obsahuje záhlaví, zaškrtněte políčko **první řádek jako záhlaví** . Vyberte možnost **z připojení/Store** a importujte schéma hlaviček přímo ze souboru v úložišti. Až budete hotovi, vyberte **OK** .

    ![Snímek obrazovky zobrazující cestu ke zdroji](media/tutorial-data-flow-private/source-file-path.png)

1. Pokud je váš cluster ladění spuštěný, přejdete na kartu **Náhled dat** ve zdrojové transformaci a výběrem možnosti **aktualizovat** získáte snímek dat. K ověření, zda je transformace nakonfigurována správně, můžete použít náhled dat.

    ![Snímek obrazovky zobrazující kartu náhled dat](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Vytvoření spravovaného privátního koncového bodu

Pokud jste nepoužili hypertextový odkaz, když jste otestovali předchozí připojení, postupujte podle cesty. Teď je potřeba vytvořit spravovaný privátní koncový bod, ke kterému se připojíte, abyste se připojili k propojené službě, kterou jste vytvořili.

1. Přejít na kartu **Spravovat** .

   > [!NOTE]
   > Karta **Spravovat** nemusí být k dispozici pro všechny instance Data Factory. Pokud ho nevidíte, můžete získat přístup k privátním koncovým bodům výběrem možnosti **vytvořit**  >  **připojení**  >  **soukromý koncový bod**.

1. Přejít do oddílu **spravované privátní koncové body** .
1. V části **spravované privátní koncové body** vyberte **+ Nový** .

    ![Snímek obrazovky, který zobrazuje nové tlačítko spravované soukromé koncové body.](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. V seznamu Vyberte dlaždici **Azure Data Lake Storage Gen2** a vyberte **pokračovat**.
1. Zadejte název účtu úložiště, který jste vytvořili.
1. Vyberte **Vytvořit**.
1. Po několika sekundách byste měli vidět, že vytvoření privátního odkazu vyžaduje schválení.
1. Vyberte privátní koncový bod, který jste vytvořili. Zobrazí se hypertextový odkaz, který vás provede schválením privátního koncového bodu na úrovni účtu úložiště.

    ![Snímek obrazovky, který zobrazuje podokno Správa privátního koncového bodu.](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Schválení privátního odkazu v účtu úložiště

1. V účtu úložiště v části **Nastavení** použijte **připojení privátního koncového bodu** .

1. Zaškrtněte políčko pomocí privátního koncového bodu, který jste vytvořili, a vyberte **schválit**.

    ![Snímek obrazovky, který zobrazuje tlačítko pro schválení privátního koncového bodu.](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Přidejte popis a vyberte **Ano**.
1. Vraťte se do části **spravované soukromé koncové body** na kartě **Spravovat** v Data Factory.
1. Po uplynutí minuty byste měli vidět, že se schválení zobrazí pro váš soukromý koncový bod.

### <a name="add-the-filter-transformation"></a>Přidat transformaci filtru

1. Vedle zdrojového uzlu na plátně toku dat vyberte ikonu se symbolem plus a přidejte novou transformaci. První transformace, kterou přidáte, je **Filtr**.

    ![Snímek obrazovky, který ukazuje přidání filtru.](media/tutorial-data-flow-private/add-filter.png)
1. Pojmenujte transformaci filtru **FilterYears**. Vyberte pole výrazu vedle **filtrování** a otevřete Tvůrce výrazů. Tady zadáte podmínku filtrování.

    ![Snímek obrazovky zobrazující FilterYears](media/tutorial-data-flow-private/filter-years.png)
1. Tvůrce výrazů toku dat umožňuje interaktivně vytvářet výrazy pro použití v různých transformacích. Výrazy mohou zahrnovat předdefinované funkce, sloupce ze vstupního schématu a uživatelsky definované parametry. Další informace o tom, jak sestavit výrazy, najdete v tématu [Tvůrce výrazů toku dat](./concepts-data-flow-expression-builder.md).

    * V tomto kurzu chcete filtrovat filmy v komedie žánru, který jste nahlásili mezi roky 1910 a 2000. Vzhledem k tomu, že rok je aktuálně řetězec, je nutné jej převést na celé číslo pomocí ```toInteger()``` funkce. Použijte operátory větší než nebo rovno (>=) a menší než nebo rovno (<=) pro porovnání s hodnotami roku literálu 1910 a 2000. Sjednotte tyto výrazy spolu s operátorem and (&&). Výraz se vychází takto:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Chcete-li zjistit, které filmy jsou tyto, můžete pomocí ```rlike()``` funkce najít vzor "komedie" ve žánrech sloupce. Sjednotí výraz rlike s porovnáním roků pro získání:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Pokud máte aktivní cluster ladění, můžete svoji logiku ověřit výběrem možnosti **aktualizovat** , aby se zobrazil výstup výrazu v porovnání s použitými vstupy. Existuje více než jedna správná odpověď na způsob, jakým lze tuto logiku provést pomocí jazyka výrazů toku dat.

        ![Snímek obrazovky, který zobrazuje výraz filtru.](media/tutorial-data-flow-private/filter-expression.png)

    * Až skončíte s vaším výrazem, vyberte **Uložit a dokončit** .

1. Načte **Náhled dat** , aby se ověřilo, že filtr funguje správně.

    ![Snímek obrazovky zobrazující filtrovaná data ve verzi Preview](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Přidat agregovanou transformaci

1. Další transformace, kterou přidáte, je **agregovaná** transformace v rámci **modifikátoru schématu**.

    ![Snímek obrazovky, který ukazuje přidání agregace.](media/tutorial-data-flow-private/add-aggregate.png)
1. Pojmenujte agregovanou transformaci **AggregateComedyRating**. Na kartě **Seskupit podle** v rozevíracím seznamu vyberte možnost **year (rok** ) a seskupte seskupení podle roku, ve kterém byl film vydaný.

    ![Snímek obrazovky znázorňující agregovanou skupinu.](media/tutorial-data-flow-private/group-by-year.png)
1. Přejít na kartu **agregace** . V levém textovém poli pojmenujte agregovaný sloupec **AverageComedyRating**. Vyberte pravé pole výrazu a zadejte agregační výraz pomocí Tvůrce výrazů.

    ![Snímek obrazovky zobrazující název agregovaného sloupce](media/tutorial-data-flow-private/name-column.png)
1. Chcete-li získat průměr **hodnocení** sloupce, použijte ```avg()``` agregační funkci. Vzhledem k tomu, že **hodnocení** je řetězec a ```avg()``` přebírá numerické vstupy, je nutné hodnotu převést na číslo prostřednictvím ```toInteger()``` funkce. Tento výraz vypadá takto:

    ```avg(toInteger(Rating))```

1. Až budete hotovi, vyberte **Uložit a dokončit** .

    ![Snímek obrazovky, který ukazuje uložení agregace.](media/tutorial-data-flow-private/save-aggregate.png)
1. Výstup transformace zobrazíte tak, že přejdete na kartu **Náhled dat** . Všimněte si, že jsou k dispozici pouze dva sloupce, **rok** a **AverageComedyRating**.

### <a name="add-the-sink-transformation"></a>Přidat transformaci jímky

1. Dále chcete přidat transformaci **jímky** do **umístění cíl**.

    ![Snímek obrazovky, který ukazuje přidání jímky.](media/tutorial-data-flow-private/add-sink.png)
1. Pojmenujte **jímku** jímky. Vyberte **Nový** a vytvořte datovou sadu jímky.

    ![Snímek obrazovky, který ukazuje vytvoření jímky.](media/tutorial-data-flow-private/create-sink.png)
1. Na stránce **Nová datová sada** vyberte **Azure Data Lake Storage Gen2** a pak vyberte **pokračovat**.

1. Na stránce **Vybrat formát** vyberte **DelimitedText** a pak vyberte **pokračovat**.

1. Pojmenujte datovou sadu jímky **MoviesSink**. Pro propojenou službu vyberte stejnou propojenou službu **ADLSGen2** , kterou jste vytvořili pro transformaci zdroje. Zadejte výstupní složku, do které se budou zapisovat data. V tomto kurzu zapisujeme do **výstupu** složky v kontejneru **Sample-data**. Složka nemusí být předem k dispozici a je možné ji vytvořit dynamicky. Zaškrtněte políčko **první řádek jako záhlaví** a vyberte možnost **žádné** pro **schéma importu**. Vyberte **OK**.

    ![Snímek obrazovky zobrazující cestu jímky](media/tutorial-data-flow-private/sink-file-path.png)

Nyní jste dokončili vytváření toku dat. Jste připraveni ho spustit ve vašem kanálu.

## <a name="run-and-monitor-the-data-flow"></a>Spuštění a sledování toku dat

Kanál můžete ladit před jeho publikováním. V tomto kroku aktivujete ladicí běh kanálu toku dat. I když náhled dat nepíše data, spustí se při ladění data do cíle jímky.

1. Přejít na plátno kanálu. Vyberte **ladit** a aktivujte ladicí běh.

1. Ladění kanálu aktivity toku dat používá aktivní ladicí cluster, ale při inicializaci trvá alespoň minutu. Průběh můžete sledovat přes kartu **výstup** . Po úspěšném spuštění vyberte ikonu brýlí pro podrobnosti o spuštění.

1. Na stránce s podrobnostmi uvidíte počet řádků a dobu trvání každého kroku transformace.

    ![Snímek obrazovky, který ukazuje běh monitorování.](media/tutorial-data-flow-private/run-details.png)
1. Pokud chcete získat podrobné informace o sloupcích a vytváření oddílů dat, vyberte transformaci.

Pokud jste postupovali podle tohoto kurzu správně, měli byste do složky jímky zapsat 83 řádků a 2 sloupce. Správnost dat můžete ověřit kontrolou úložiště objektů BLOB.

## <a name="summary"></a>Souhrn

V tomto kurzu jste použili Data Factory uživatelské rozhraní k vytvoření kanálu, který kopíruje a transformuje data ze zdroje Data Lake Storage Gen2 do jímky Data Lake Storage Gen2 (povolením přístupu pouze k vybraným sítím) pomocí mapování toku dat v [Data Factory spravovaných Virtual Network](managed-virtual-network-private-endpoint.md).