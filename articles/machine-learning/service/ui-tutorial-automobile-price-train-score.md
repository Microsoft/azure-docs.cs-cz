---
title: 'Kurz: Předpověď ceny automobilu pomocí vizuálního rozhraní'
titleSuffix: Azure Machine Learning service
description: Naučte se, jak pomocí vizuálního rozhraní přetáhnout, bodování a nasadit model strojového učení. Tento kurz je první částí série dvou částí pro předpověď cen automobilů pomocí lineární regrese.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/21/2019
ms.openlocfilehash: b0d227b71677db1d6b4ce8386b02cf957ca259f7
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668414"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Kurz: Předpověď ceny automobilu pomocí vizuálního rozhraní

V tomto kurzu se dozvíte, jak pomocí vizuálního rozhraní služby Azure Machine Learning vyvíjet a nasazovat prediktivní analytické řešení, které předpovídá cenu každého auta. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

V první části si nastavíte prostředí, přetahujete datové sady a moduly pro analýzu na interaktivní plátno a spojíte je dohromady a vytvoříte experiment. 

V první části kurzu se naučíte:

> [!div class="checklist"]
> * Import a vyčištění dat
> * Výuka modelu strojového učení
> * Skóre a vyhodnocení modelu

V [druhé části](ui-tutorial-automobile-price-deploy.md) kurzu se naučíte, jak nasadit prediktivní model jako webovou službu Azure, abyste ho mohli použít k předpovídání ceny každé auta na základě technických specifikací, které odešlete. 

Hotová verze tohoto kurzu je k dispozici jako vzorový experiment.

Pokud ho chcete najít, vyberte na **stránce experimenty**možnost **Přidat nový**a **potom vyberte ukázková 1 – regrese: Experimentování s cenami za automobil (** základní)

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pokud máte pracovní prostor služby Azure Machine Learning, přejděte k další části.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-new-experiment"></a>Vytvořit nový experiment

1. Otevřete pracovní prostor v [Azure Portal](https://portal.azure.com/).

1. V pracovním prostoru vyberte možnost **vizuální rozhraní**. Pak vyberte **Spustit vizuální rozhraní**. 

    ![Snímek obrazovky Azure Portal znázorňující, jak přistupovat k vizuálnímu rozhraní z pracovního prostoru služby Machine Learning](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

1. Vytvořte nový experiment tak, že v dolní části okna vizuální rozhraní vyberete **+ Nový** .

1. Vyberte **prázdný experiment**.

1. Vyberte výchozí název experimentu **"experiment byl vytvořen na...** " v horní části plátna a přejmenujte ho na něco smysluplného. Například **"předpověď ceny automobilu"** . Název nemusí být jedinečný.

## <a name="specify-data"></a>Zadat data

Machine Learning závisí na datech. Donovanovo je v tomto rozhraní k dispozici několik ukázkových datových sad, které můžete experimentovat s nástrojem. Pro tento kurz použijte ukázková data o **cenách automobilu (RAW)** . 

1. Nalevo od plátna experimentu je paleta datových sad a modulů. Vyberte **uložené datové sady** a potom výběrem **ukázek** zobrazte dostupné ukázkové datové sady.

1. Vyberte datovou sadu, **údaje o cenách automobilu (RAW)** a přetáhněte ji na plátno.

   ![Přetáhněte data na plátno.](./media/ui-tutorial-automobile-price-train-score/drag-data.png)

1. Vyberte sloupce dat, se kterými chcete pracovat. Do pole Hledat v horní části palety zadejte **Select** a vyhledejte modul **Výběr sloupců v datové sadě** .

1. Klikněte na plátno a přetáhněte modul **Výběr sloupců v datové sadě** na plátno. Přetáhněte modul pod modul DataSet.

1. Připojte datovou sadu, kterou jste přidali dříve, do modulu **Výběr sloupců v datové sadě** kliknutím a přetažením. Přetáhněte z výstupního portu datové sady, což je malý kroužek v dolní části datové sady na plátně, a to tak, aby byl v horní části modulu k dispozici vstupní port pro **Výběr sloupců v datové sadě**, což je malý kroužek.

    > [!TIP]
    > Tok dat můžete vytvořit prostřednictvím experimentu, když připojíte výstupní port jednoho modulu ke vstupnímu portu jiného.
    >

    ![Připojit moduly](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    Červený vykřičník označuje, že jste ještě nastavili vlastnosti modulu.

1. Vyberte modul **Výběr sloupců v datové sadě** .

1. V podokně **vlastnosti** napravo od plátna vyberte **Upravit sloupce**.

    V dialogovém okně **Vybrat sloupce** vyberte **všechny sloupce** a zahrňte **všechny funkce**. Dialogové okno by mělo vypadat takto:

     ![výběr sloupců](./media/ui-tutorial-automobile-price-train-score/select-all.png)

1. V pravém dolním rohu výběrem **OK** zavřete selektor sloupců.

## <a name="run-the-experiment"></a>Spusťte experiment.

V každém okamžiku klikněte na výstupní port datové sady nebo modulu, abyste viděli, jak data v tomto okamžiku v toku dat vypadají. Pokud je možnost vizualizace zakázaná, musíte nejdřív spustit experiment.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

Až bude cílový výpočetní výkon dostupný, experiment se spustí. Po dokončení běhu se u každého modulu zobrazí zelený znak zaškrtnutí.


## <a name="visualize-the-data"></a>Vizualizace dat

Teď, když jste spustili svůj úvodní experiment, můžete vizualizovat data a pochopit Další informace o datové sadě, kterou máte.

1. V dolní části pole **Vybrat sloupce v datové sadě** vyberte výstupní port a pak vyberte **vizualizovat**.

1. Kliknutím na jiné sloupce v okně data zobrazíte informace o tomto sloupci.

    V této datové sadě každý řádek představuje automobil a proměnné přidružené k jednotlivým automobilům se zobrazí jako sloupce. V této datové sadě jsou 205 řádky a 26 sloupců.

     Pokaždé, když kliknete na sloupec dat, zobrazí se vlevo obrázek informace o statistice a **vizualizaci** daného sloupce. Když například kliknete na **Počet dvířek** , které vidíte, má dvě jedinečné hodnoty a dvě chybějící hodnoty. Posuňte se dolů a zobrazte hodnoty: dvě a čtyři dveře.

     ![Náhled dat](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)

1. Kliknutím na jednotlivé sloupce pochopíte více o vaší datové sadě a zamyslete se nad tím, zda budou tyto sloupce užitečné pro předpověď ceny automobilu.

## <a name="prepare-data"></a>Příprava dat

Datová sada obvykle vyžaduje před analýzou nějaký předzpracování. Při vizualizaci datové sady jste si pravděpodobně všimli některých chybějících hodnot. Tyto chybějící hodnoty se musí vyčistit, aby model mohl data správně analyzovat. Odeberete všechny řádky, které obsahují chybějící hodnoty. Sloupec normalizovaných **ztrát** navíc má velký podíl chybějících hodnot, takže tento sloupec z modelu zcela vyloučíte.

> [!TIP]
> Vyčištění chybějících hodnot ze vstupních dat je pro většinu modulů nutností.

### <a name="remove-column"></a>Odebrání sloupce

Nejprve odeberte sloupec **normalizovaných ztrát** .

1. Vyberte modul **Výběr sloupců v datové sadě** .

1. V podokně **vlastnosti** napravo od plátna vyberte **Upravit sloupce**.

    * Ponechte vybranou možnost **pravidla** a **všechny sloupce** .

    * V rozevíracích seznamech vyberte **Vyloučit** a **názvy sloupců** a klikněte do textového pole. Zadejte **normalizované ztráty**.

    * V pravém dolním rohu výběrem **OK** zavřete selektor sloupců.

    ![Vyloučení sloupce](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    Nyní podokno vlastnosti pro možnost vybrat sloupce v datové sadě označuje, že bude procházet všechny sloupce datové sady kromě **normalizovaných ztrát**.
        
    V podokně vlastnosti se zobrazí vyloučený sloupec **normalizované ztráty** .
        
    ![Podokno vlastností](./media/ui-tutorial-automobile-price-train-score/property-pane.png)
        
    Kliknutím dvakrát na modul a zadáním textu je možné přidat k modulu komentář. To vám může pomoci rychle poznat, jaký je účel modulu v experimentu. 

1. Dvakrát klikněte na modul **Výběr sloupců v datové sadě** a zadejte komentář "vyloučit normalizované ztráty". 
    
    Po zadání komentáře klikněte mimo modul. Zobrazí se šipka dolů, která ukazuje, že modul obsahuje komentář.

1. Kliknutím na šipku dolů zobrazíte komentář.

    Modul nyní zobrazuje šipku nahoru pro skrytí komentáře.
        
    ![Komentáře](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>Vyčištění chybějících dat

Při výukovém modelu je nutné provést něco o chybějících datech. V takovém případě přidáte modul pro odebrání všech zbývajících řádků, ve kterých chybí data.

1. Do vyhledávacího pole zadejte **vyčistit** a vyhledejte modul **Vyčištění chybějících dat** .

1. Přetáhněte modul **Vyčištění chybějících dat** na plátno experimentu a propojte jej s modulem **Výběr sloupců v datové sadě** . 

1. V podokně Vlastnosti vyberte v **režimu čištění**možnost **odstranit celý řádek** .

1. Klikněte dvakrát na modul a zadejte komentář Odstranění řádků s chybějícími hodnotami.
 
    ![Odebrat řádky](./media/ui-tutorial-automobile-price-train-score/remove-rows.png)

    Experiment by teď měl vypadat nějak takto:
    
    ![vybrat – sloupec](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="train-the-model"></a>Trénování modelu

Teď, když jsou data připravena, můžete vytvořit prediktivní model. Vaše data budete používat ke výukě modelu. Pak otestujete model, abyste viděli, jak úzce je možné předpovědět ceny.

**Klasifikace** a **regrese** jsou dva typy technik strojového učení se supervizí. **Klasifikace** předpovídá odpověď ze definované sady kategorií, jako je například barva (červená, modrá nebo zelená). **Regrese** se používá k předpovídání čísla.

Vzhledem k tomu, že chcete odhadnout cenu, což je číslo, můžete použít regresní algoritmus. V tomto příkladu použijete model lineární regrese.

Zajistěte si model tím, že mu udělíte sadu dat, která obsahuje cenu. Model vyhledá data a vyhledá korelaci mezi funkcemi automobilu a jeho cenou.

Využijte svá data pro školení modelu a testujte je rozdělením dat na samostatné školení a testování datových sad.

1. Zadejte **rozdělená data** do vyhledávacího pole, abyste našli modul **rozdělení dat** a připojili ho k levému portu modulu **Vyčištění chybějících dat** .

1. Vyberte modul **rozdělit data** . V podokně Vlastnosti nastavte zlomek řádků v první výstupní sadě dat na 0,7. Tímto způsobem budeme k tomu, že použijeme 70 procent dat pro výuku modelu, a pro testování se vrátíme o 30 procent.

    ![Snímek obrazovky znázorňující správnou konfiguraci podokna vlastností Hodnoty "rozdělená data" by měly být rozdělené na řádky, 0,7, náhodné rozdělení, 0, false.](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. Poklikejte na rozdělená **data** a zadejte komentář "rozdělit datovou sadu do sady školení (0,7) a sady testů (0,3)".

1. Chcete-li vybrat sledovací algoritmus, zrušte zaškrtnutí políčka pro hledání palety modulu.

1. Rozbalte **Machine Learning** potom rozbalte položku **inicializovat model**. Tímto se zobrazí několik kategorií modulů, které je možné použít k inicializaci algoritmů strojového učení.

1. Pro tento experiment vyberte **regresní** > **lineární regresi** a přetáhněte ji na plátno experimentu.

    ![Snímek obrazovky znázorňující správnou konfiguraci podokna vlastností Hodnoty "rozdělená data" by měly být rozdělené na řádky, 0,7, náhodné rozdělení, 0, false.](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. Najděte modul vlakového **modelu** a přetáhněte ho na plátno experimentu. Připojte výstup modulu lineární regrese k levému vstupu modulu vlak model a připojte výstup školicích dat (levý port) modulu **rozdělení dat** ke správnému vstupu modulu **vlak model** .

    ![Snímek obrazovky znázorňující správnou konfiguraci modulu vlakového modelu. Modul lineární regrese se připojí k levému portu modulu vlakového modelu a modul rozdělit data se připojí k pravému portu modelu vlaku.](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Vyberte modul **vlakového modelu** . V podokně Vlastnosti vyberte spustit selektor sloupců a do pole **zahrnout názvy sloupců**zadejte **Price** (další). Cena je hodnota, kterou model hodlá předpovědět.

    ![Snímek obrazovky znázorňující správnou konfiguraci modulu selektor sloupců S pravidly > zahrnout názvy sloupců > "Price"](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    Experiment by měl vypadat takto:

    ![Snímek obrazovky, který ukazuje správnou konfiguraci experimentu po přidání modulu vlak model.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

## <a name="score-and-evaluate-the-model"></a>Skóre a vyhodnocení modelu

Teď, když jste pronaučili model pomocí 70 procent vašich dat, můžete ho použít k vyhodnocení dalších 30 procent dat, abyste viděli, jak dobře model funguje.

1. Do vyhledávacího pole zadejte **model skóre** a najděte modul **skóre modelu** a přetáhněte ho na plátno experimentu. Připojte výstup modulu **vlak model** k levému vstupnímu portu **modelu skóre**. Připojte výstup testovacích dat (pravý port) modulu **rozdělení dat** ke správnému vstupnímu portu **modelu skóre**.

1. Do vyhledávacího pole zadejte **vyhodnocení** pro vyhledání **modelu vyhodnocení** a přetáhněte modul na plátno experimentu. Propojte výstup modulu určení **skóre modelu** s levým vstupem **modelu vyhodnocení**. Konečný experiment by měl vypadat přibližně takto:

    ![Snímek obrazovky, který ukazuje poslední správnou konfiguraci experimentu.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Spusťte experiment pomocí výpočetního prostředku, který jste vytvořili dříve.

1. Zobrazte výstup modulu určení **skóre** modelu tak, že vyberete výstupní port **modelu skóre** a vyberete **vizualizovat**. Na výstupu se zobrazí predikované hodnoty ceny a známé hodnoty v testovacích datech.

    ![Snímek obrazovky výstupní vizualizace, která zvýrazňuje sloupec označení skóre](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Pokud chcete zobrazit výstup z modulu **vyhodnocení modelu** , vyberte výstupní port a vyberte **vizualizovat**.

    ![Snímek obrazovky zobrazující výsledky vyhodnocení pro finální experiment](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

Pro váš model se zobrazí následující statistiky:

* **Střední absolutní chyba (Mae)** : Průměr absolutních chyb (chyba je rozdíl mezi předpovězenou a skutečnou hodnotou).
* **Střední Chyba na čtverci kořene (RMSE)** : Druhá odmocnina průměru kvadratických chyb předpovědi provedených v testovací datové sadě.
* **Relativní absolutní chyba**: Průměr absolutních chyb relativních k absolutnímu rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot.
* **Relativní čtvercová chyba**: Průměr kvadratických chyb vzhledem k kvadratickému rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot.
* **Koeficient určení**: Tato statistická Metrika označuje, jak dobře model odpovídá datům, označovanou také jako hodnota R.

Pro každou statistiku chyb platí, že menší hodnota je lepší. Menší hodnota označuje, že předpověď přesněji odpovídá skutečným hodnotám. U koeficientu stanovitelnosti je bližší jeho hodnota jednomu (1,0), což je lepší předpovědi.

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>Správa experimentů v pracovním prostoru služby Azure Machine Learning

Experimenty, které vytvoříte ve vizuálním rozhraní, lze spravovat z pracovního prostoru služby Azure Machine Learning. Použijte pracovní prostor k zobrazení podrobnějších informací, jako jsou například jednotlivci experimentů, diagnostické protokoly, prováděcí grafy a další.

1. Otevřete pracovní prostor v [Azure Portal](https://portal.azure.com/).  

1. V pracovním prostoru vyberte **experimenty**. Pak vyberte experiment, který jste vytvořili.

    ![Snímek obrazovky, který ukazuje, jak přejít k experimentům v Azure Portal](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    Na této stránce se zobrazí přehled experimentu a jeho nejnovějších spuštění.

    ![Snímek obrazovky s přehledem statistik experimentů v Azure Portal](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. Pokud chcete zobrazit další podrobnosti o konkrétním spuštění, vyberte číslo spuštění.

    ![Podrobná sestava spuštění snímku obrazovky](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    Sestava spuštění je aktualizována v reálném čase. Pokud jste v experimentu použili **skript spustit** v Pythonu nebo **spustíte modul skriptu jazyka R** , můžete na kartě **protokoly** zadat protokol skriptu pro výstup.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další postup

V první části tohoto kurzu jste dokončili tyto kroky:

* Vytvoření experimentu
* Příprava dat
* Trénování modelu
* Skóre a vyhodnocení modelu

V části druhá část se dozvíte, jak model nasadit jako webovou službu Azure.

> [!div class="nextstepaction"]
> [Pokračovat v nasazování modelů](ui-tutorial-automobile-price-deploy.md)
