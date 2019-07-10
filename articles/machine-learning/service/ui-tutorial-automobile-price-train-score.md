---
title: 'Kurz: Předvídání cen automobilů vizuální rozhraní'
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak pro trénování, skóre a nasadit model strojového učení pomocí přetažení vizuální rozhraní. V tomto kurzu je první částí série dvojdílného na předpověď cen automobilů prostřednictvím lineární regrese.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 21f5a2d93b708e93f124bd44177bb7852dfbd86a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720522"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Kurz: Předvídání cen automobilů vizuální rozhraní

V tomto kurzu provedete rozšířené podívat vývoj prediktivního řešení ve vizuální rozhraní služby Azure Machine Learning. Na konci tohoto kurzu budete mít, který může předpovídat cenu za jakékoli automobilu na základě technických specifikací, které odesíláte řešení.

V první části kurzu se dozvíte, jak:

> [!div class="checklist"]
> * Import a vyčištění dat
> * Trénování modelu strojového učení
> * Stanovení skóre a vyhodnocení modelu

V [druhá část](ui-tutorial-automobile-price-deploy.md) kurzu se dozvíte víc o prediktivní model nasadit jako webová služba Azure.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

Úplnou verzi tohoto kurzu je k dispozici jako ukázkový experiment.

Najít, z **experimenty stránky**vyberte **přidat nový**a pak vyberte **ukázka 1 - regrese: Automobilů Prediction(Basic) cena** experimentovat.

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pokud máte pracovní prostor služby Azure Machine Learning service, pokračujte [další části](#open-the-visual-interface-webpage).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>Otevřít webovou stránku vizuální rozhraní

1. Otevření pracovního prostoru v [webu Azure portal](https://portal.azure.com/).

1. V pracovním prostoru vyberte **vizuální rozhraní**. Potom vyberte **vizuální rozhraní spuštění**. 

    ![Snímek obrazovky webu Azure portal ukazující, jak přistupovat k vizuální rozhraní z pracovního prostoru služby Machine Learning](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

## <a name="create-your-first-experiment"></a>Vytvoření prvního experimentu

Nástroj vizuální rozhraní poskytuje interaktivní a vizuální místě k vytvoření prediktivních analytických modelů. Přetáhněte myší datové sady a analýzy moduly na interaktivní plátno a jejich dohromady a vytvoří připojení *experimentovat*.

1. Vytvoření nového experimentu tak, že vyberete **+ nová** v dolní části okna vizuální rozhraní.

    ![Přidat nový experiment](./media/ui-tutorial-automobile-price-train-score/add-new.png)

1. Vyberte **prázdný Experiment**.

1. Vyberte výchozí název experimentu **"experimentovali vytvořené na...** "v horní části plátna a přejmenujte jej na něco smysluplného. Například **predikce ceny**. Název nemusí být jedinečný.

## <a name="add-data"></a>Přidání dat

První věc, kterou potřebujete pro machine learning se data. Existuje několik ukázkových datových sad v tomto rozhraní, které můžete použít. Můžete také importovat data z existujících zdrojů. Pro účely tohoto kurzu použijte ukázkovou datovou sadou **Automobile price data (Raw)** . 

1. Nalevo od plátna experimentu je paleta datových sad a modulů. Vyberte **uložení datové sady** vyberte **ukázky** Chcete-li zobrazit dostupné ukázkové datové sady.

1. Vyberte datovou sadu, **Automobile price data (raw)** a přetáhněte ji na plátno.

   ![Data přetáhněte na plátno](./media/ui-tutorial-automobile-price-train-score/drag-data.png)

## <a name="select-columns"></a>Výběr sloupců

Vyberte sloupce, které dat pro práci s. Začněte tím konfigurace modulu k zobrazení všech dostupných sloupců.

> [!TIP]
> Pokud znáte název dat nebo modul, který chcete, pomocí panelu hledání v horní části palety ho vždy snadno našli. Zbývající část tohoto kurzu budou používat tento zástupce.


1. Typ **vyberte** do vyhledávacího pole Najít **výběr sloupců v datové sadě** modulu.

1. Klikněte na tlačítko a přetáhněte ji **výběr sloupců v datové sadě** na plátno. Odstranit modul pod datové sady, které jste přidali dříve.

1. Připojení datovou sadu, která **výběr sloupců v datové sadě**: klikněte na výstupní port datové sady, přetáhněte vstupnímu portu **výběr sloupců v datové sadě**, uvolněte tlačítko myši. Datové sady a modul zůstanou připojené i v případě, že přesouváte buď na plátně.

    > [!TIP]
    > Vstupní a výstupní porty datových sad a modulů jsou reprezentované malými kroužky – vstupní porty v horní části, výstupní porty v dolní části. Tok dat prostřednictvím experimentu vytvořit připojte výstupní port jednoho modulu ke vstupnímu portu jiného.
    >

    ![Připojte moduly](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    Červený vykřičník označuje, že nebyly nastavení vlastností pro modul dosud.

1. Vyberte **výběr sloupců v datové sadě** modulu.

1. V **vlastnosti** podokně napravo od plátna vyberte **upravit sloupce**.

    V **vyberte sloupce, které** dialogového okna, vyberte **všechny sloupce** a zahrnují **všechny funkce**. Dialogové okno by měl vypadat nějak takto:

     ![selektor sloupců](./media/ui-tutorial-automobile-price-train-score/select-all.png)

1. Vpravo dole vyberte **OK** zavřete selektor sloupců.

## <a name="run-the-experiment"></a>Spusťte experiment.

V okamžiku klikněte na výstupní port datové sady nebo modulu a prohlédnout si, co vypadá v daném okamžiku v toku dat. Pokud **vizualizovat** možnost je vypnuta, musíte nejprve spusťte experiment.

Experimentu se spouští na cílové výpočetní prostředí, což je výpočetní prostředek, který je připojený k pracovnímu prostoru. Po vytvoření cílové výpočetní prostředí, můžete znovu použít pro budoucí spouštění.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

Po cílové výpočetní prostředí je k dispozici, spustí se experiment. Po dokončení spuštění se zobrazí zelená značka zaškrtnutí v každém modulu.


## <a name="preview-the-data"></a>Zobrazte náhled dat

Teď, když spustíte počáteční experimentu můžete vizualizovat data a Pochopte více o datové sady, které je nutné pracovat s.

1. Vyberte na výstupní port v dolní části **výběr sloupců v datové sadě** vyberte **vizualizovat**.

1. Klikněte na různé sloupce v okně dat. Chcete-li zobrazit informace o tomto sloupci.

    V této datové sadě každý řádek představuje automobilu a proměnné přidružené k automobilům se zobrazují jako sloupce. Existují 205 řádků a sloupců 26 v této datové sadě.

     Pokaždé, když kliknete na sloupce dat, **statistiky** informace a **vizualizace** obrázek sloupce se zobrazí na levé straně. Například když kliknete na **num dveře** obsahuje jedinečné hodnoty 2 a 2 chybějící hodnoty. Přejděte dolů a zobrazit hodnoty: dvěma až čtyřmi dveře.

     ![Zobrazte náhled dat](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)

1. Kliknutím na jednotlivé sloupce a lépe pochopit, jaké vaše datová sada, rozmyslete si, zda bude předpovídat cenu automobilu užitečné tyto sloupce.

## <a name="prepare-data"></a>Příprava dat

Před analýzou datové sady bývá zpravidla nutné sadu nějakým způsobem předzpracovat. Jste si možná všimli některé chybějící hodnoty při vizualizaci datové sady. Tyto chybějící hodnoty se musí vyčistit, aby model mohl data správně analyzovat. Odeberete všechny řádky, které chybí některé hodnoty. Také **normalized-losses** sloupec má velkou část chybějící hodnoty, aby tento sloupec z modelu budete vyloučit úplně.

> [!TIP]
> Vyčištění chybějících hodnot ze vstupních dat je pro většinu modulů nutností.

### <a name="remove-column"></a>Odebrání sloupce

Nejdřív odeberte **normalized-losses** sloupec úplně.

1. Vyberte **výběr sloupců v datové sadě** modulu.

1. V **vlastnosti** podokně napravo od plátna vyberte **upravit sloupce**.

    * Ponechte **s pravidly** a **všechny sloupce** vybrané.

    * V rozevíracích seznamech vyberte **Vyloučit** a **názvy sloupců** a klikněte do textového pole. Typ **normalized-losses**.

    * Vpravo dole vyberte **OK** zavřete selektor sloupců.

    ![Vyloučení sloupce](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    Teď v podokně vlastností pro výběr sloupců v datové sadě označuje, že bude procházet všechny sloupce datové sady kromě **normalized-losses**.
        
    V podokně vlastností ukazuje, že **normalized-losses** sloupec je vyloučený.
        
    ![Podokno vlastností](./media/ui-tutorial-automobile-price-train-score/property-pane.png)
        
    Kliknutím dvakrát na modul a zadáním textu je možné přidat k modulu komentář. To vám může pomoci rychle poznat, jaký je účel modulu v experimentu. 

1. Dvakrát klikněte **výběr sloupců v datové sadě** modul a zadejte komentář vyloučit normalized-losses." 
    
    Po zadání komentáře, klikněte na tlačítko mimo modul. Šipka dolů, zobrazí se, že modul obsahuje komentář.

1. Klikněte na šipku dolů, chcete-li zobrazit komentář.

    Modul se teď zobrazí šipka nahoru Chcete-li skrýt komentář.
        
    ![Komentáře](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>Vyčištění chybějících dat

Při tréninku modelů, budete muset udělat něco o datech, která chybí. V tomto případě přidáte modul a všechny zbývající řádky, ve kterých chybějí data.

1. Typ **Vyčistit** do vyhledávacího pole Najít **vyčištění chybějících dat** modulu.

1. Přetáhněte **vyčištění chybějících dat** plátno modul do experimentu a propojte jej s **výběr sloupců v datové sadě** modulu. 

1. V podokně vlastností, vyberte **odstranit celý řádek** pod **režim čištění**.

    Tyto možnosti s přímým přístupem **vyčištění chybějících dat** k vyčištění dat odstraněním řádků, které mají chybí některé hodnoty.

1. Klikněte dvakrát na modul a zadejte komentář Odstranění řádků s chybějícími hodnotami.
 
    ![Odebrat řádky](./media/ui-tutorial-automobile-price-train-score/remove-rows.png)

    Experiment by teď měl vypadat přibližně takto:
    
    ![Vyberte sloupec](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="visualize-the-results"></a>Vizualizace výsledků

Vzhledem k tomu, že jste provedli změny moduly do experimentu, se změnila stav na "V návrhu".  K vizualizaci nových vyčištění dat, budete muset nejprve spustit experiment znovu.

1. Vyberte **spustit** v dolní části pro spuštění testu.

    Nyní můžete znovu použít cílové výpočetní prostředí, které jste vytvořili dříve.

1. Vyberte **spustit** v dialogovém okně.

   ![Spusťte experiment](./media/ui-tutorial-automobile-price-train-score/select-compute.png)

1. Po dokončení spuštění, klikněte pravým tlačítkem na **vyčištění chybějících dat** modulu můžete vizualizovat nové vyčištění dat.

    ![Vizualizujte vyčištění dat](./media/ui-tutorial-automobile-price-train-score/visualize-cleaned.png)

1. Klikněte na různé sloupce v okně vyčištěnou dat. Pokud chcete zobrazit, jak se data změnila.

    ![Vizualizujte vyčištění dat](media/ui-tutorial-automobile-price-train-score/visualize-result.png)

    Nejsou nyní 193 řádků a sloupců 25.

    Po kliknutí na **num dveře** uvidíte stále má 2 jedinečné hodnoty, ale teď má chybějící hodnoty 0. Proklikejte se prostřednictvím rest sloupce, které chcete zobrazit, že neexistují žádné chybějící hodnoty left v datové sadě. 

## <a name="train-the-model"></a>Trénování modelu

Teď, když jsou data připravená, můžete vytvořit prediktivní model. Vaše data použijete k natrénování modelu. Potom budete testovat model je jak přesně dokáže předpovídat ceny.

**Klasifikace** a **regrese** jsou dva typy technik strojového učení se supervizí. **Klasifikace** předpovídá odpověď na základě definované sady kategorií, například barvy (červená, modrá nebo zelená). **Regrese** se používá k předpovědi čísel.

Vzhledem k tomu, že chcete předpovědět cenu, což je číslo, můžete použít regresní algoritmus. V tomto příkladu použijete model lineární regrese.

Trénování modelu tím, že se sadu dat, která zahrnuje cenu. Model vyhledá data a hledá korelace mezi funkcí automobilu a jeho cenou. Potom otestování modelu tím, že je sada funkcí pro automobily, který je obeznámen se a v tématu jak blízko dodává model k predikci známé ceně.

Pomocí dat pro trénování modelu a jeho otestováním rozdělením dat do samostatných trénování a testování datových sad.

1. Typ **rozdělení dat** do vyhledávacího pole Najít **rozdělení dat** modulu a připojte ho k levé číslo portu **vyčištění chybějících dat** modulu.

1. Vyberte **rozdělení dat** modulu jste se právě připojili ho vyberte. V podokně vlastností nastavte podíl řádků v první výstupní sadě dat na 0,7. Takto použijeme 70 procent dat pro trénování modelu a Ponecháme na testování 30 procent.

    ![Snímek obrazovky podokna vlastností správnou konfiguraci. Hodnoty "Rozdělení dat", by měl být "Rozdělit řádky", 0,7, náhodně mění rozdělení, 0, False.](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. Dvakrát klikněte **rozdělení dat** a zadejte komentář "Rozdělit datovou sadu do školení set(0.3) set(0.7) a test"

1. Vyberte algoritmus učení, zrušte zaškrtnutí pole Hledat palety vašeho modulu.

1. Rozbalte **Machine Learning** rozbalte **inicializovat Model**. Tímto se zobrazí několik kategorií modulů, které je možné použít k inicializaci algoritmů strojového učení.

1. Pro tento experiment vyberte **regrese** > **lineární regrese** a přetáhněte ji na plátno experimentu.

    ![Snímek obrazovky podokna vlastností správnou konfiguraci. Hodnoty "Rozdělení dat", by měl být "Rozdělit řádky", 0,7, náhodně mění rozdělení, 0, False.](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. Najděte a přetáhněte **Train Model** modulů na plátno experimentu. Propojte výstup modulu lineární regrese levým vstupem modulu Train Model a připojit výstup trénovacích dat (levý port) z **rozdělení dat** modul s pravým vstupem modulu **Train Model**modulu.

    ![Snímek obrazovky zobrazující správnou konfiguraci modulu Train Model. Modul lineární regrese připojuje k modulu levý port Train Model a připojí modulu rozdělení dat na správný port z trénování modelu](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Vyberte **Train Model** modulu. V podokně vlastností, vyberte spustit selektor sloupců a pak zadejte **cena** vedle **zahrnout názvy sloupců**. Cena je hodnota, která váš model bude předpovídat

    ![Snímek obrazovky zobrazující správnou konfiguraci pro modul Výběr sloupců. S pravidly > zahrnout názvy sloupců > "price"](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    Experiment by měl vypadat nějak takto:

    ![Snímek obrazovky zobrazující správnou konfiguraci testu po přidání modulu Train Model.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>Spuštění výukového experimentu

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>Stanovení skóre a vyhodnocení modelu

Teď, když jsme natrénovali model pomocí 70 procent dat, které můžete použít ke stanovení skóre pro jiné 30 procent dat a zjistit, jak dobře vaše funkce modelu.

1. Typ **určení skóre modelu** do vyhledávacího pole Najít **Score Model** modulu a modulu přetáhněte na plátno experimentu. Propojte výstup modulu **Train Model** modul na levé straně vstupním portem modulu **Score Model**. Připojte se výstup testovacích dat (pravý port) z **rozdělení dat** modulu na pravé straně vstupním portem modulu **Score Model**.

1. Typ **vyhodnotit** do vyhledávacího pole Najít **Evaluate Model** a přetáhněte na plátno experimentu modul. Propojte výstup modulu **Score Model** levým vstupem modulu **Evaluate Model**. Konečný experiment by měl vypadat přibližně takto:

    ![Snímek obrazovky zobrazující konečné správnou konfiguraci testu.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Spusťte experiment. použití stejného cíle výpočetní použili.

1. Zobrazte výstup **Score Model** tak, že vyberete na výstupní port modulu **Score Model** a vyberte **vizualizovat**. Na výstupu se zobrazí predikované hodnoty ceny a známé hodnoty v testovacích datech.

    ![Snímek obrazovky vizualizace výstupu zvýraznění "Skóre popisek" sloupce](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Pokud chcete zobrazit výstup z modulu Evaluate Model, vyberte na výstupní port a vyberte vizualizovat.

    ![Snímek obrazovky zobrazující výsledky vyhodnocení pro konečný experiment.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

Pro váš model se zobrazí následující statistiky:

* **Střední absolutní chyba (MAE)** : Průměr absolutních chyb (chybu je rozdíl mezi předpovězenou a skutečnou hodnotu).
* **Střední kořenové spolehlivosti chyby (RMSE)** : Druhá odmocnina průměru kvadratických chyb předpovědí na základě testovací datové.
* **Relativní absolutní chyba**: Průměr absolutních chyb relativních k absolutnímu rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot.
* **Relativní spolehlivosti chyba**: Průměr kvadratických chyb relativních ke kvadratickému rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot.
* **Koeficient spolehlivosti**: Také známé jako hodnota spolehlivosti R, to tedy statistická metrika označující, jak dobře model odpovídá zpracovávaným datům.

Pro každou statistiku chyb platí, že menší hodnota je lepší. Menší hodnota označuje, že předpověď přesněji odpovídá skutečným hodnotám. Pro koeficientu spolehlivosti, čím blíž jeho hodnota hodnotě jedna (1,0), tím lepší jsou předpovědi.

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>Spravovat experimenty v pracovním prostoru služby Azure Machine Learning

Experimenty, které vytvoříte ve vizuální rozhraní je možné spravovat z pracovního prostoru služby Azure Machine Learning. Používejte pracovní prostor zobrazíte podrobnější informace, jako je spuštění experimentu jednotlivce, diagnostické protokoly, exekučních grafů a dalších.

1. Otevření pracovního prostoru v [webu Azure portal](https://portal.azure.com/).  

1. V pracovním prostoru vyberte **experimenty**. Vyberte experiment, který jste vytvořili.

    ![Snímek obrazovky ukazující, jak se orientovat na experimentů na webu Azure Portal](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    Na této stránce zobrazí se vám přehled testu a jeho poslední spuštění.

    ![Snímek obrazovky zobrazující přehled statistiky experimentu na webu Azure Portal](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. Vyberte spuštění číslo zobrazíte další podrobnosti o konkrétního spuštění.

    ![Spusťte sestavu podrobný snímek obrazovky](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    Spusťte sestavu je aktualizovat v reálném čase. Pokud jste použili **Execute Python Script** modulu v experimentu můžete určit skript protokoly pro výstup v **protokoly** kartu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další postup

V první části tohoto kurzu dokončení těchto kroků:

* Vytvoření experimentu
* Příprava dat
* Trénování modelu
* Stanovení skóre a vyhodnocení modelu

V druhé části se dozvíte, jak model nasadit jako webová služba Azure.

> [!div class="nextstepaction"]
> [Pokračovat v nasazení modelů](ui-tutorial-automobile-price-deploy.md)
