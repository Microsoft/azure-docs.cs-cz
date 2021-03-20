---
title: Delta Lake ETL s toky dat
description: V tomto kurzu najdete podrobné pokyny pro použití toků dat pro transformaci a analýzu dat v rozdílových Lake.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 02/09/2021
ms.openlocfilehash: cb8d44353e826df14ed3baab2c4ca66ffed4a569
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417287"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>Transformace dat v rozdílových Lake pomocí mapování toků dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

V tomto kurzu použijete plátno toku dat k vytvoření toků dat, které vám umožní analyzovat a transformovat data v Azure Data Lake Storage (ADLS) Gen2 a uložit je v rozdílových Lake.

## <a name="prerequisites"></a>Předpoklady
* **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet služby Azure Storage**. ADLS Storage použijete jako *zdrojová* úložiště a úložiště dat *jímky* . Pokud účet úložiště nemáte, přečtěte si téma [Vytvoření účtu služby Azure Storage](../storage/common/storage-account-create.md), kde najdete postup jeho vytvoření.

Soubor, který v tomto kurzu transformuje, je MoviesDB.csv, který najdete [tady](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv). Pokud chcete načíst soubor z GitHubu, zkopírujte obsah do textového editoru, který si zvolíte, a uložte ho místně jako soubor. csv. Postup nahrání souboru do účtu úložiště najdete v tématu [nahrání objektů BLOB pomocí Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Příklady budou odkazovat na kontejner s názvem Sample-data.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

V tomto kroku vytvoříte datovou továrnu a otevřete Data Factory UX pro vytvoření kanálu v datové továrně.

1. Otevřete **Microsoft Edge** nebo **Google Chrome**. V současné době je Data Factory uživatelské rozhraní podporováno pouze ve webových prohlížečích Microsoft Edge a Google Chrome.
1. V nabídce vlevo vyberte **vytvořit**  >    >  **Data Factory** integrace prostředků.
1. Na stránce **Nová datová továrna** v části **název** zadejte **ADFTutorialDataFactory** .
1. Vyberte **předplatné** Azure, v rámci kterého chcete datovou továrnu vytvořit.
1. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

    a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **vytvořit novou** a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md). 
1. Jako **Verzi** vyberte **V2**.
1. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou mohou být v jiných oblastech.
1. Vyberte **Vytvořit**.
1. Po dokončení vytváření se zobrazí oznámení v centru oznámení. Vyberte **Přejít k prostředku** a přejděte na stránku Datová továrna.
1. Vyberte **Vytvořit a monitorovat**. Na samostatné kartě se spustí uživatelské rozhraní služby Data Factory.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Vytvoření kanálu s aktivitou toku dat

V tomto kroku vytvoříte kanál, který obsahuje aktivitu toku dat.

1. Na stránce **Začínáme** vyberte **Vytvořit kanál**.

   ![Vytvoření kanálu](./media/doc-common-process/get-started-page.png)

1. Na kartě **Obecné** pro kanál zadejte **DeltaLake** pro **název** kanálu.
1. V horním panelu pro vytváření snímků nastavte posuvník **ladění toku dat** . Režim ladění umožňuje interaktivní testování logiky transformace proti clusteru Live Spark. Clustery toku dat zabírají 5-7 minut a uživatelům se doporučuje zapnout ladění jako první, pokud chtějí provádět vývoj toku dat. Další informace naleznete v tématu [režim ladění](concepts-data-flow-debug-mode.md).

    ![Aktivita toku dat](media/tutorial-data-flow/dataflow1.png)
1. V podokně **aktivity** rozbalte možnost **přesunout a transformovat** . Přetáhněte aktivitu **toku dat** z podokna na plátno kanálu.

    ![Snímek obrazovky zobrazující plátno kanálu, ve kterém můžete aktivitu toku dat vyřadit.](media/tutorial-data-flow/activity1.png)
1. V rozbalovacím seznamu **Přidat tok dat** vyberte **vytvořit nový tok dat** a potom pojmenujte svůj datový tok **DeltaLake**. Po dokončení klikněte na Dokončit.

    ![Snímek obrazovky, který ukazuje, kde se má tok dat pojmenovat při vytváření nového toku dat.](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Sestavení logiky transformace v plátně toku dat

V tomto kurzu budete generovat dva toky dat. Tok dat Fist je jednoduchý zdroj pro jímku, který vygeneruje nový rozdílový Lake ze souboru ve formátu CSV. Nakonec vytvoříte tento návrh toku, abyste mohli aktualizovat data v rozdílových Lake.

![Konečný tok](media/data-flow/data-flow-tutorial-6.png "Konečný tok")

### <a name="tutorial-objectives"></a>Cíle kurzu

1. Využijte zdroj datové sady MoviesCSV výše, z něho vytvořte nový rozdílový Lake.
1. Sestavte logiku pro aktualizace hodnocení pro 1988 filmů na 1.
1. Odstranit všechny filmy z 1950
1. Vložte nové filmy pro 2021 duplikováním filmů z 1960.

### <a name="start-from-a-blank-data-flow-canvas"></a>Spuštění z prázdného plátna toku dat

1. Klikněte na transformaci zdroje
1. V dolním panelu 1 klikněte na nový vedle datové sady. vytvoří se nová propojená služba pro ADLS Gen2.
1. Zvolit text s oddělovači pro typ datové sady
1. Pojmenujte datovou sadu "MoviesCSV" 
1. Přejděte na soubor MoviesCSV, který jste nahráli do úložiště výše.
1. Nastavte, aby byla oddělená čárkou a zahrnout záhlaví na prvním řádku. 
1. Přejděte na kartu zdrojová projekce a klikněte na možnost rozpoznat datové typy.
1. Jakmile budete mít nastavenou projekci, můžete pokračovat 
1. Přidat transformaci jímky
1. Rozdíl je vložený typ datové sady. Bude nutné nasměrovat na účet úložiště ADLS Gen2.
   
   ![Vložená datová sada](media/data-flow/data-flow-tutorial-5.png "Vložená datová sada")

1. Vyberte název složky v kontejneru úložiště, ve kterém chcete vytvořit pomocí ADF, aby se vytvořil rozdílový Lake.
1. Vraťte se zpět do návrháře kanálů a kliknutím na ladit spusťte kanál v režimu ladění, který se nachází pouze s touto aktivitou toku dat na plátně. Tím se vygeneruje nový rozdílový Lake v ADLS Gen2.
1. V nabídce prostředky továrny klikněte na nový > tok dat. 
1. Použijte znovu MoviesCSV jako zdroj a klikněte na "detekovat datové typy" znovu.
1. Přidání transformace filtru do zdrojové transformace v grafu
1. Povolí pouze filmové řádky, které se shodují se třemi roky, se kterými budete pracovat, které budou 1950, 1988 a 1960.
1. Aktualizujte hodnocení pro každý 1988 film na ' 1 ' tím, že přidáte odvozenou transformaci sloupce do transformace filtru.
1. V tomto stejném odvozeném sloupci vytvořte filmy pro 2021, a to tak, že zaberete stávající rok a změníte rok na 2021. Pojďme vybrat 1960.
1. To znamená, že vaše tři odvozené sloupce budou vypadat jako

   ![Odvozený sloupec](media/data-flow/data-flow-tutorial-2.png "Odvozený sloupec")
   
1. ```Update, insert, delete, and upsert``` zásady se vytvářejí v transformaci ALTER Row. Přidejte transformaci ALTER Row po odvozený sloupec.
1. Zásady pro změnu řádků by měly vypadat takto.

   ![Změna řádku](media/data-flow/data-flow-tutorial-3.png "Změna řádku")
   
1. Teď, když jste nastavili správné zásady pro každý typ ALTER Row, ověřte, že se pro transformaci jímky nastavila správná pravidla aktualizace.

   ![Jímka](media/data-flow/data-flow-tutorial-4.png "Jímka")
   
1. V tomto případě používáme pro ADLS Gen2 Data Lake rozdílovou jímku a povolujeme vkládání, aktualizace a odstraňování. 
1. Všimněte si, že klíčové sloupce jsou složený klíč, který se skládá ze sloupce primárního klíče videa a sloupce rok. Důvodem je to, že jsme vytvořili falešné 2021 filmů duplikováním 1960 řádků. Tím se vyhnete kolizím při vyhledávání stávajících řádků poskytnutím jedinečnosti.

### <a name="download-completed-sample"></a>Ukázka dokončeného stažení
[Tady je ukázkové řešení pro rozdílové kanály s tokem dat pro řádky aktualizovat/odstranit v Lake:](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [jazyce výrazů toku dat](data-flow-expression-functions.md).
