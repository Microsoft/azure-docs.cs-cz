---
title: Osvědčené postupy pro zápis do souborů do data Lake pomocí toků dat
description: Tento kurz poskytuje osvědčené postupy pro zápis do souborů do data Lake pomocí toků dat.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 04/01/2021
ms.openlocfilehash: 8010f3f95c9358714b659df5821a375bd8488ad8
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582211"
---
# <a name="best-practices-for-writing-to-files-to-data-lake-with-data-flows"></a>Osvědčené postupy pro zápis do souborů do data Lake pomocí toků dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

V tomto kurzu se seznámíte s osvědčenými postupy, které se dají použít při psaní souborů do ADLS Gen2 nebo Azure Blob Storage pomocí toků dat. Budete potřebovat přístup k účtu Azure Blob Storage nebo účtu Azure Data Lake Store Gen2 pro čtení souboru Parquet a následnému uložení výsledků do složek.

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet služby Azure Storage**. ADLS Storage použijete jako *zdrojová* úložiště a úložiště dat *jímky* . Pokud účet úložiště nemáte, přečtěte si téma [Vytvoření účtu služby Azure Storage](../storage/common/storage-account-create.md), kde najdete postup jeho vytvoření.

V krocích v tomto kurzu se předpokládá, že máte 

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

V tomto kroku vytvoříte datovou továrnu a otevřete Data Factory UX pro vytvoření kanálu v datové továrně.

1. Otevřete **Microsoft Edge** nebo **Google Chrome**. V současné době je Data Factory uživatelské rozhraní podporováno pouze ve webových prohlížečích Microsoft Edge a Google Chrome.
1. V nabídce vlevo vyberte **vytvořit**  >    >  **Data Factory** integrace prostředků.
1. Na stránce **Nová datová továrna** v části **název** zadejte **ADFTutorialDataFactory** .
1. Vyberte **předplatné** Azure, v rámci kterého chcete datovou továrnu vytvořit.
1. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

    a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.
    
    b. Vyberte **vytvořit novou** a zadejte název skupiny prostředků. Další informace o skupinách prostředků najdete v tématu [použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).
    
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

Budete přebírat všechna zdrojová data (v tomto kurzu použijeme Parquet zdroj souborů) a pomocí transformace jímky vydáte data ve formátu Parquet s využitím nejúčinnějších mechanismů pro data Lake ETL.

![Konečný tok](media/data-flow/parts-final.png "Konečný tok")

### <a name="tutorial-objectives"></a>Cíle kurzu

1. Výběr libovolné ze zdrojových datových sad v novém toku dat
1. Použití toků dat k efektivnímu vytvoření oddílů datové sady jímky
1. Rozložit rozdělená data do složek ADLS Gen2 Lake

### <a name="start-from-a-blank-data-flow-canvas"></a>Spuštění z prázdného plátna toku dat

Nejdřív nastavili jsme prostředí toku dat pro každý z mechanismů popsaných níže pro vypsání dat v ADLS Gen2

1. Klikněte na transformaci zdroje.
1. Klikněte na tlačítko Nový vedle pole datová sada na dolním panelu.
1. Vyberte datovou sadu nebo vytvořte novou. V této ukázce použijeme datovou sadu Parquet s názvem uživatelská data.
1. Přidejte transformaci odvozeného sloupce. Použijeme ho jako způsob, jak dynamicky nastavit názvy požadovaných složek.
1. Přidejte transformaci jímky.
   
### <a name="hierarchical-folder-output"></a>Výstup hierarchické složky

Ve vašich datech je velmi běžné použít jedinečné hodnoty k vytváření hierarchií složek pro vytvoření oddílů dat v Lake. To je velmi ideální způsob, jak organizovat a zpracovávat data v Lake a ve Sparku (výpočetní modul na datových tocích). Tímto způsobem ale budete mít k dispozici malé náklady na výkon pro uspořádání výstupu. Očekává se, že se v celkovém výkonu kanálu při použití tohoto mechanismu v jímky zobrazí malý pokles.

1. Vraťte se do návrháře toku dat a upravte výše vytvořený tok dat. Klikněte na transformaci jímky.
1. Klikněte na optimalizovat > nastavit > klíč pro dělení.
1. Vyberte sloupce, které chcete použít k nastavení hierarchické struktury složek.
1. Všimněte si, že následující příklad používá rok a měsíc jako sloupec pro pojmenovávání složek. Výsledky budou složky formuláře ```releaseyear=1990/month=8``` .
1. Při přístupu k oddílům dat ve zdroji toku dat budete odkazovat na složku nejvyšší úrovně výše ```releaseyear``` a pro každou další složku použít vzor zástupného znaku, např.: ```**/**/*.parquet```
1. Pokud chcete manipulovat s hodnotami dat, nebo i když potřebujete generovat syntetické hodnoty pro názvy složek, použijte transformaci odvozeného sloupce k vytvoření hodnot, které chcete použít v názvech složek.

![Dělení klíčů](media/data-flow/key-parts.png "Dělení klíčů")
   
### <a name="name-folder-as-data-values"></a>Název složky jako hodnoty dat

Mírně lepší provádění techniky jímky pro Lake data pomocí ADLS Gen2, které nenabízejí stejnou výhodu jako dělení klíč/hodnota, je ```Name folder as column data``` . Vzhledem k tomu, že klíčový styl rozdělení hierarchické struktury vám umožní snadněji zpracovávat datové řezy, tato technika je plochá struktura složek, která může zapisovat data rychleji.

1. Vraťte se do návrháře toku dat a upravte výše vytvořený tok dat. Klikněte na transformaci jímky.
1. Klikněte na optimalizovat > nastavit dělení > použít aktuální dělení.
1. Klikněte na nastavení > složka název jako data sloupce.
1. Vyberte sloupec, který chcete použít pro generování názvů složek.
1. Pokud chcete manipulovat s hodnotami dat, nebo i když potřebujete generovat syntetické hodnoty pro názvy složek, použijte transformaci odvozeného sloupce k vytvoření hodnot, které chcete použít v názvech složek.

![Možnost složky](media/data-flow/folders.png "Složky")

### <a name="name-file-as-data-values"></a>Název souboru jako hodnoty dat

Techniky uvedené v předchozích kurzech jsou vhodné pro vytváření kategorií složek v Data Lake. Výchozí schéma pojmenovávání souborů, které tyto techniky používá, je použití ID úlohy prováděcího modulu Spark. Někdy můžete chtít nastavit název výstupního souboru v jímky textu toku dat. Tato technika je navržena pouze pro použití s malými soubory. Proces slučování souborů oddílů do jednoho výstupního souboru je dlouhotrvající proces.

1. Vraťte se do návrháře toku dat a upravte výše vytvořený tok dat. Klikněte na transformaci jímky.
1. Klikněte na optimalizovat > nastavte dělení > jednotlivé oddíly. Jedná se o tento požadavek na jeden oddíl, který v procesu spouštění vytvoří kritické místo pro sloučení souborů. Tato možnost se doporučuje jenom pro malé soubory.
1. Klikněte na nastavení > název souboru jako data sloupce.
1. Vyberte sloupec, který chcete použít pro generování názvů souborů.
1. Pokud chcete manipulovat s hodnotami dat, nebo i když potřebujete generovat syntetické hodnoty pro názvy souborů, použijte transformaci odvozeného sloupce k vytvoření hodnot, které chcete použít v názvech souborů.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [jímka toku dat](data-flow-sink.md).
