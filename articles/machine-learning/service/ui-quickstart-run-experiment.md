---
title: 'Rychlý start: Příprava a vizualizace dat bez psaní kódu'
titleSuffix: Azure Machine Learning service
description: Vytvoření experimentu můžete připravit a vizualizovat data pomocí přetahování myší uživatelské rozhraní služby machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 07b86138a95853673b5d54e272b40af41d58f418
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475948"
---
# <a name="quickstart-prepare-and-visualize-data-without-writing-code-in-azure-machine-learning"></a>Rychlý start: Příprava a vizualizace dat, aniž byste museli psát kód ve službě Azure Machine Learning

Příprava a vizualizace dat v rozhraní visual drag drop (preview) pro Azure Machine Learning. Data, která budete používat obsahuje záznamy různých automobilů, včetně informací o značce, modelu, technických specifikací a cena. Po dokončení tohoto rychlého startu budete připraveni na základě těchto dat předpovídat cenu automobilu představuje jeden. 

Před tréninku model strojového učení, musíte pochopit a připravit data.  V tomto rychlém startu budete:

- Vytvoření prvního experimentu přidat a zobrazit náhled dat
- Příprava dat tak, že odeberete chybějící hodnoty
- Spusťte experiment.
- Vizualizujte Výsledná data

Pokud jste úplně novým uživatelem ve službě machine learning, série videí [datová věda pro začátečníky](https://docs.microsoft.com/azure/machine-learning/studio/data-science-for-beginners-the-5-questions-data-science-answers) představuje vynikající Úvod do strojového učení.

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

### <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pokud máte pracovní prostor služby Azure Machine Learning service, pokračujte [další části](#start). V opačném případě vytvořte si ho teď.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="start"></a> Otevřít webovou stránku vizuální rozhraní

1. Otevření pracovního prostoru v [webu Azure portal](https://portal.azure.com/).  

1. V pracovním prostoru vyberte **vizuální rozhraní**.  Potom vyberte **vizuální rozhraní spuštění**.  
 
    ![Vizuální rozhraní](./media/ui-quickstart-run-experiment/launch-ui.png)

    Rozhraní webová stránka se otevře v nové stránky prohlížeče.  

## <a name="create-your-first-experiment"></a>Vytvoření prvního experimentu

Nástroj vizuální rozhraní poskytuje interaktivní, visual místo, kde můžete snadno vytvářet, testovat a iterovat model prediktivní analýzy. Můžete datové sady a analýzy moduly přetažení myší na interaktivní plátno, kde je vzájemně do formuláře _experimentovat_.  Vytvoření prvního experimentu.

1. V levém dolním rohu vyberte **přidat nový**.
![Přidat nový experiment](./media/ui-quickstart-run-experiment/add-new.png)

1. Vyberte **prázdný Experiment**.

1. Experimentu se přiřadí výchozí název. Vyberte tento text a přejmenujte jej na "rychlý start zkoumat data." Tento název nemusí být jedinečný.

1. **Mini mapy** v dolní části obrazovky je užitečné při prohlížení velké experimentů.  Nemusíte v rámci tohoto rychlého startu, proto klikněte na šipku v horní části, chcete-li minimalizovat ho.  

    ![Přejmenování experimentu](./media/ui-quickstart-run-experiment/rename.png)

## <a name="add-data"></a>Přidání dat

První věc, kterou potřebujete pro machine learning se data. Existuje několik ukázkových datových sad v tomto rozhraní, které můžete použít, nebo můžete importovat data z mnoha zdrojů. V tomto příkladu použijeme ukázkovou datovou sadou **Automobile price data (Raw)** . 

1. Nalevo od plátna experimentu je paleta datových sad a modulů. Vyberte **uložení datové sady** vyberte **ukázky** Chcete-li zobrazit dostupné ukázkové datové sady.

1. Vyberte datovou sadu, **Automobile price data (raw)** a přetáhněte ji na plátno.

   ![Data přetáhněte na plátno](./media/ui-quickstart-run-experiment/drag-data.png)


## <a name="select-columns"></a>Výběr sloupců

Vyberte sloupce, které dat pro práci s.  Začněte tím konfigurace modulu k zobrazení všech dostupných sloupců.

> [!TIP]
> Pokud znáte název dat nebo modul, který chcete, pomocí panelu hledání v horní části palety ho vždy snadno našli.  Zbývající část tohoto rychlého startu bude používat tento zástupce.

1. Typ **vyberte** do vyhledávacího pole Najít **výběr sloupců v datové sadě** modulu.

1. Klikněte na tlačítko a přetáhněte ji **výběr sloupců v datové sadě** na plátno. Odstranit modul pod datové sady, které jste přidali dříve.

1. Připojení datovou sadu, která **výběr sloupců v datové sadě**: klikněte na výstupní port datové sady, přetáhněte vstupnímu portu **výběr sloupců v datové sadě**, uvolněte tlačítko myši. Datové sady a modul zůstanou připojené i v případě, že přesouváte buď na plátně.

    > [!TIP]
    > Vstupní a výstupní porty datových sad a modulů jsou reprezentované malými kroužky – vstupní porty v horní části, výstupní porty v dolní části. Tok dat prostřednictvím experimentu vytvořit připojte výstupní port jednoho modulu ke vstupnímu portu jiného.
    >
    > Pokud máte potíže s připojením moduly, zkuste přetáhnout úplně do uzlu, ke kterému se připojujete.

    ![Připojte moduly](./media/ui-quickstart-run-experiment/connect-modules.gif) 

    Červený vykřičník označuje, že nebyly nastavení vlastností pro modul dosud. Uděláte to teď.
   
1. Vyberte **výběr sloupců v datové sadě** modulu.

1. V **vlastnosti** podokně napravo od plátna vyberte **upravit sloupce**.

    V **vyberte sloupce, které** dialogového okna, vyberte **všechny sloupce** a zahrnují **všechny funkce**. Dialogové okno by měl vypadat nějak takto:

     ![selektor sloupců](./media/ui-quickstart-run-experiment/select-all.png)

1. Vpravo dole vyberte **OK** zavřete selektor sloupců.

## <a name="run-the-experiment"></a>Spusťte experiment.

V okamžiku klikněte na výstupní port datové sady nebo modulu a prohlédnout si, co vypadá v daném okamžiku v toku dat.  Pokud **vizualizovat** možnost je vypnuta, musíte nejprve spusťte experiment.  Uděláte to teď.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

Po cílové výpočetní prostředí je k dispozici, spustí se experiment. Po dokončení spuštění se zobrazí zelenou značku zaškrtnutí na každý modul.

![Zobrazit stav](./media/ui-quickstart-run-experiment/status1.png)

## <a name="preview-the-data"></a>Zobrazte náhled dat

Teď, když spustíte počáteční experimentu můžete vizualizovat data a Pochopte více o informace, které je nutné pracovat s.

1. Vyberte na výstupní port v dolní části **výběr sloupců v datové sadě** vyberte **vizualizovat**.

1. Klikněte na různé sloupce v okně dat. Chcete-li zobrazit informace o tomto sloupci.  

    V této datové sadě každý řádek představuje automobilu a proměnné přidružené k automobilům se zobrazují jako sloupce.    Existují 205 řádků a sloupců 26 v této datové sadě.

     Pokaždé, když kliknete na sloupce dat, **statistiky** informace a **vizualizace** obrázek sloupce se zobrazí na levé straně.  Například když kliknete na **num dveře** obsahuje jedinečné hodnoty 2 a 2 chybějící hodnoty.  Přejděte dolů a zobrazit hodnoty: dvěma až čtyřmi dveře.

     ![Zobrazte náhled dat](./media/ui-quickstart-run-experiment/preview-data.gif)

1. Kliknutím na jednotlivé sloupce a lépe pochopit, jaké vaše datová sada, rozmyslete si, zda bude předpovídat cenu automobilu užitečné tyto sloupce.

## <a name="prepare-data"></a>Příprava dat

Před analýzou datové sady bývá zpravidla nutné sadu nějakým způsobem předzpracovat. Možná jste si ve sloupcích různých řádků všimli chybějících hodnot. Tyto chybějící hodnoty se musí vyčistit, aby model mohl data správně analyzovat. Odeberete všechny řádky, které chybí některé hodnoty. Také **normalized-losses** sloupec má velkou část chybějící hodnoty, aby tento sloupec z modelu budete vyloučit úplně.

> [!TIP]
> Vyčištění chybějících hodnot ze vstupních dat je pro většinu modulů nutností.  

### <a name="remove-column"></a>Odebrání sloupce

Nejdřív odeberte **normalized-losses** sloupec úplně.

1. Vyberte **výběr sloupců v datové sadě** modulu.

1. V **vlastnosti** podokně napravo od plátna vyberte **upravit sloupce**.

    * Ponechte **s pravidly** a **všechny sloupce** vybrané.

    * V rozevíracích seznamech vyberte **Vyloučit** a **názvy sloupců** a klikněte do textového pole. Typ **normalized-losses**.

    * Vpravo dole vyberte **OK** zavřete selektor sloupců.

    ![Vyloučení sloupce](./media/ui-quickstart-run-experiment/exclude-column.png)
        
    Teď v podokně vlastností pro výběr sloupců v datové sadě označuje, že bude procházet všechny sloupce datové sady kromě **normalized-losses**.
        
    V podokně vlastností ukazuje, že **normalized-losses** sloupec je vyloučený.
        
    ![Podokno vlastností](./media/ui-quickstart-run-experiment/property-pane.png)
        
    Kliknutím dvakrát na modul a zadáním textu je možné přidat k modulu komentář. To vám může pomoci rychle poznat, jaký je účel modulu v experimentu. 

1. Dvakrát klikněte **výběr sloupců v datové sadě** modul a zadejte komentář vyloučit normalized-losses." 
    
    Po zadání komentáře, klikněte na tlačítko mimo modul.  Šipka dolů, zobrazí se, že modul obsahuje komentář.

1. Klikněte na šipku dolů, chcete-li zobrazit komentář.

    Modul se teď zobrazí šipka nahoru Chcete-li skrýt komentář.
        
    ![Komentáře](./media/ui-quickstart-run-experiment/comments.png)

### <a name="clean-missing-data"></a>Vyčištění chybějících dat

Při tréninku modelů, budete muset udělat něco o datech, která chybí.  V tomto případě přidáte modul a všechny zbývající řádky, ve kterých chybějí data.  

1. Typ **Vyčistit** do vyhledávacího pole Najít **vyčištění chybějících dat** modulu.

1. Přetáhněte **vyčištění chybějících dat** plátno modul do experimentu a propojte jej s **výběr sloupců v datové sadě** modulu. 

1. V podokně vlastností, vyberte **odstranit celý řádek** pod **režim čištění**.

    Tyto možnosti s přímým přístupem **vyčištění chybějících dat** k vyčištění dat odstraněním řádků, které mají chybí některé hodnoty.

1. Klikněte dvakrát na modul a zadejte komentář Odstranění řádků s chybějícími hodnotami.
 
    ![Odebrat řádky](./media/ui-quickstart-run-experiment/remove-rows.png)

    Experiment by teď měl vypadat přibližně takto:
    
    ![Vyberte sloupec](./media/ui-quickstart-run-experiment/experiment-clean.png)

## <a name="visualize-the-results"></a>Vizualizace výsledků

Vzhledem k tomu, že jste provedli změny moduly do experimentu, se změnila stav na "V návrhu".  K vizualizaci nových vyčištění dat, budete muset nejprve spustit experiment znovu.

1. Vyberte **spustit** v dolní části pro spuštění testu.

    Nyní můžete znovu použít cílové výpočetní prostředí, které jste vytvořili dříve.  

1. Vyberte **spustit** v dialogovém okně.

   ![Spusťte experiment](./media/ui-quickstart-run-experiment/select-compute.png)

1. Po dokončení spuštění, klikněte pravým tlačítkem na **vyčištění chybějících dat** modulu můžete vizualizovat nové vyčištění dat.  

    ![Vizualizujte vyčištění dat](./media/ui-quickstart-run-experiment/visualize-cleaned.png)

1. Klikněte na různé sloupce v okně vyčištěnou dat. Pokud chcete zobrazit, jak se data změnila.  

    ![Vizualizujte vyčištění dat](media/ui-quickstart-run-experiment/visualize-result.png)

    Nejsou nyní 193 řádků a sloupců 25.

    Po kliknutí na **num dveře** uvidíte stále má 2 jedinečné hodnoty, ale teď má chybějící hodnoty 0. Proklikejte se prostřednictvím rest sloupce, které chcete zobrazit, že neexistují žádné chybějící hodnoty left v datové sadě. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste se naučili:

- Vytvoření prvního experimentu přidat a zobrazit náhled dat
- Příprava dat tak, že odeberete chybějící hodnoty
- Vizualizace dat připravené

Pokračujte na kurz na základě těchto dat předpovídat cenu automobilu.

> [!div class="nextstepaction"]
> [Kurz: Předvídání cen automobilů vizuální rozhraní](ui-tutorial-automobile-price-train-score.md)
