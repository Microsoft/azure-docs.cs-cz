---
title: 'Kurz: předpověď cen automobilů pomocí návrháře'
titleSuffix: Azure Machine Learning
description: Naučte se model strojového učení předpovídat ceny automobilu pomocí lineární regrese. Tento kurz je první částí z dvoudílné série.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 09/28/2020
ms.custom: designer
ms.openlocfilehash: ca812fc7548e3c70f1faa1e1ed6a34afda3872af
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575971"
---
# <a name="tutorial-predict-automobile-price-with-the-designer"></a>Kurz: Predikce ceny automobilů s využitím návrháře


V tomto kurzu se dozvíte, jak pomocí návrháře Azure Machine Learning naučit a nasazovat model strojového učení, který předpovídá cenu každého auta. Návrhář je nástroj pro přetahování, který umožňuje vytvářet modely strojového učení bez jediného řádku kódu.

V první části kurzu se dozvíte, jak:

> [!div class="checklist"]
> * Vytvořte nový kanál.
> * Importujte data.
> * Připravte data.
> * Naučte se model strojového učení.
> * Vyhodnoťte model strojového učení.

V [druhé části](tutorial-designer-automobile-price-deploy.md) kurzu nasadíte svůj model jako koncový bod Inferencing v reálném čase, který bude předpovídat cenu každé auta na základě technických specifikací, které odešlete. 

> [!NOTE]
>Dokončená verze tohoto kurzu je k dispozici jako vzorový kanál.
>
>Pokud ho chcete najít, v pracovním prostoru se podívejte do návrháře. V části **Nový kanál** vyberte **Ukázka 1 – regrese: automobilová předpověď ceny (základní)**.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-new-pipeline"></a>Vytvořit nový kanál

Kanály Azure Machine Learning organizují více kroků strojového učení a zpracování dat do jednoho prostředku. Kanály umožňují organizovat, spravovat a opakovaně používat složité pracovní postupy strojového učení napříč projekty a uživateli.

Pokud chcete vytvořit kanál Azure Machine Learning, potřebujete Azure Machine Learning pracovní prostor. V této části se dozvíte, jak tyto prostředky vytvořit.

### <a name="create-a-new-workspace"></a>Vytvoření nového pracovního prostoru

K použití návrháře potřebujete Azure Machine Learning pracovní prostor. Pracovní prostor je prostředek nejvyšší úrovně pro Azure Machine Learning, poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte v Azure Machine Learning. Pokyny k vytvoření pracovního prostoru najdete v tématu [Vytvoření a správa Azure Machine Learningch pracovních prostorů](how-to-manage-workspace.md).

> [!NOTE]
> Pokud váš pracovní prostor používá virtuální síť, existují další kroky konfigurace, které musíte použít pro použití návrháře. Další informace najdete v tématu [použití Azure Machine Learning studia ve službě Azure Virtual Network](how-to-enable-studio-virtual-network.md) .

### <a name="create-the-pipeline"></a>Vytvoření kanálu

1. Přihlaste se k <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>a vyberte pracovní prostor, se kterým chcete pracovat.

1. Vyberte **Návrhář**.

    ![Snímek obrazovky s vizuálním pracovním prostorem, který ukazuje, jak získat přístup k Návrháři](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Vyberte **snadno použitelné předem připravené moduly**.

1. V horní části plátna vyberte výchozí kanál názvu kanálu **– Vytvořeno**. Přejmenujte ho na *automobilovou předpověď cen*. Název nemusí být jedinečný.

## <a name="set-the-default-compute-target"></a>Nastavit výchozí cíl výpočtů

Kanál běží na výpočetním cíli, což je výpočetní prostředek, který je připojený k vašemu pracovnímu prostoru. Po vytvoření cíle výpočetní služby ho můžete znovu použít pro budoucí spuštění.

Pro celý kanál můžete nastavit **výchozí výpočetní cíl** , který všem modulům oznámí, že ve výchozím nastavení bude používat stejný cíl výpočtů. Pro jednotlivé moduly ale můžete určit výpočetní cíle.

1. Vedle názvu kanálu vyberte **ikonu ozubeného kolečka** ![ ikony ozubeného kolečka ](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) v horní části plátna a otevřete tak podokno **Nastavení** .

1. V podokně **Nastavení** napravo od plátna vyberte **vybrat cíl výpočtů**.

    Pokud už máte dostupný cíl výpočtů, můžete ho vybrat ke spuštění tohoto kanálu.

    > [!NOTE]
    > Návrhář může spustit školicí experimenty jenom na Azure Machine Learning výpočetní výkon, ale neobjeví se jiné výpočetní cíle.

1. Zadejte název výpočetního prostředku.

1. Vyberte **Uložit**.

    > [!NOTE]
    > Vytvoření výpočetního prostředku trvá přibližně pět minut. Po vytvoření prostředku ho můžete znovu použít a tuto dobu čekání přeskočit pro budoucí spuštění.
    >
    > Výpočetní prostředek se automaticky škáluje na nulové uzly, pokud je nečinný úspora nákladů. Když ho znovu použijete po uplynutí určité prodlevy, může se při škálování na pozadí vyskytnout přibližně pět minut čekací doby.

## <a name="import-data"></a>Import dat

Návrhář obsahuje několik ukázkových datových sad, které můžete experimentovat s nástrojem. Pro tento kurz použijte **data o cenách automobilu (RAW)**. 

1. Vlevo od plátna kanálu je paleta datových sad a modulů. Vyberte **ukázková** datová sada pro zobrazení dostupných ukázkových datových sad.

1. Vyberte **data automobil auto data (Hrubá cena)** a přetáhněte je na plátno.

   ![Přetáhněte data na plátno.](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Vizualizace dat

Můžete vizualizovat data a pochopit datovou sadu, kterou budete používat.

1. Klikněte pravým tlačítkem myši na **data o cenách automobilu (hrubá)** a vyberte **vizualizovat**.

1. Výběrem různých sloupců v okně data zobrazíte informace o každém z nich.

    Každý řádek představuje automobil a proměnné přidružené k jednotlivým automobilům se zobrazí jako sloupce. V této datové sadě jsou 205 řádky a 26 sloupců.

## <a name="prepare-data"></a>Příprava dat

Datové sady obvykle vyžadují před analýzou určitý předzpracování. Při kontrole datové sady jste si pravděpodobně všimli některých chybějících hodnot. Tyto chybějící hodnoty musí být vyčištěny, aby model mohl správně analyzovat data.

### <a name="remove-a-column"></a>Odebrání sloupce

Při výukovém modelu je nutné provést něco o chybějících datech. Ve sloupci **normalizované ztráty** v této datové sadě chybí mnoho hodnot, takže tento sloupec z modelu zcela vyloučíte.

1. V paletě modulu nalevo od plátna rozbalte část **transformace dat** a vyhledejte modul **Výběr sloupců v datové sadě** .

1. Přetáhněte modul **Výběr sloupců v datové sadě** na plátno. Přetáhněte modul pod modul DataSet.

1. Připojte datovou sadu **dat o ceně automobilu (RAW)** do modulu **Výběr sloupců v datové sadě** . Přetáhněte z výstupního portu datové sady, což je malý kroužek v dolní části datové sady na plátně, na vstupní port pro **Výběr sloupců v datové sadě**, což je malý kruh v horní části modulu.

    > [!TIP]
    > Pokud připojíte výstupní port jednoho modulu ke vstupnímu portu jiného, vytvoříte tok dat prostřednictvím kanálu.
    >

    ![Připojit moduly](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Vyberte modul **Výběr sloupců v datové sadě** .

1. V podokně podrobností modulu napravo od plátna vyberte **Upravit sloupec**.

1. Rozbalte rozevírací seznam **názvů sloupců** vedle položky **Zahrnout** a vyberte možnost  **všechny sloupce**.

1. Pokud **+** chcete přidat nové pravidlo, vyberte.

1. V rozevíracích nabídkách vyberte **vyloučit** a **názvy sloupců**.
    
1. Do textového pole zadejte *normalizované ztráty* .

1. V pravém dolním rohu výběrem **Uložit** zavřete selektor sloupců.

    ![Vyloučení sloupce](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Vyberte modul **Výběr sloupců v datové sadě** . 

1. V podokně podrobností modulu napravo od plátna vyberte textové pole **Komentář** a zadejte *vyloučit normalizované ztráty*.

    V grafu se zobrazí komentáře, které vám pomůžou organizovat svůj kanál.

### <a name="clean-missing-data"></a>Vyčistit chybějící data

V datové sadě ještě chybí hodnoty po odebrání sloupce **normalizované ztráty** . Zbývající chybějící data můžete odebrat pomocí modulu **Vyčištění chybějících dat** .

> [!TIP]
> Vyčištění chybějících hodnot ze vstupních dat je předpokladem pro použití většiny modulů v návrháři.

1. V paletě modulu nalevo od plátna rozbalte **transformaci dat** oddílu a vyhledejte modul **Vyčištění chybějících dat** .

1. Přetáhněte modul **Vyčištění chybějících dat** na plátno kanálu. Připojte ho k modulu **Výběr sloupců v datové sadě** . 

1. Vyberte modul **Vyčištění chybějících dat** .

1. V podokně podrobností modulu napravo od plátna vyberte **Upravit sloupec**.

1. V okně sloupce, které se **mají vyčistit** , rozbalte rozevírací nabídku vedle položky **Zahrnout**. Vybrat, **všechny sloupce**

1. Vyberte **Uložit**.

1. V podokně podrobností modulu napravo od plátna vyberte **Odebrat celý řádek** v **režimu čištění**.

1. V podokně podrobností modulu napravo od plátna vyberte pole **Komentář** a zadejte *odebrat řádky chybějících hodnot*. 

    Váš kanál by teď měl vypadat nějak takto:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png"alt-text="Vybrat – sloupec":::

## <a name="train-a-machine-learning-model"></a>Trénování modelu strojového učení

Teď, když máte moduly, které jsou pro zpracování dat k dispozici, můžete nastavit školicí moduly.

Vzhledem k tomu, že chcete odhadnout cenu, což je číslo, můžete použít regresní algoritmus. V tomto příkladu použijete model lineární regrese.

### <a name="split-the-data"></a>Rozdělení dat

Rozdělení dat je běžnou úlohou ve strojovém učení. Data budete rozdělit do dvou samostatných datových sad. Jedna datová sada povede model a druhá bude testovat, jak dobře byl model proveden.

1. V paletě modulu rozbalte **transformaci dat** oddílu a najděte modul **rozdělit data** .

1. Přetáhněte modul **rozdělit data** na plátno kanálu.

1. Připojte levý port modulu **Vyčištění chybějících dat** k modulu **rozdělit data** .

    > [!IMPORTANT]
    > Ujistěte se, že levé výstupní porty **vyčistit chybějící data** se připojují k **rozděleným datům**. Levý port obsahuje vyčištěná data. Pravý port obsahuje data, která jsou k diskošíku.

1. Vyberte modul **rozdělit data** .

1. V podokně podrobností modulu napravo od plátna nastavte **zlomek řádků v první výstupní datové sadě** na 0,7.

    Tato možnost rozdělí 70 procent dat za účelem výuky modelu a 30 procent pro jeho testování. Datový objekt 70% bude přístupný prostřednictvím levého výstupního portu. Zbývající data budou k dispozici prostřednictvím správného výstupního portu.

1. V podokně podrobností modulu napravo od plátna vyberte pole **Komentář** a potom zadejte *rozdělit datovou sadu do sady školení (0,7) a sady testů (0,3)*.

### <a name="train-the-model"></a>Trénování modelu

Vytvořte si model tak, že mu udělíte datovou sadu, která obsahuje cenu. Algoritmus vytvoří model, který vysvětluje vztah mezi funkcemi a cenou, jak je znázorněno v školicích datech.

1. V paletě modulu rozbalte **Machine Learning algoritmy**.
    
    Tato možnost zobrazí několik kategorií modulů, které lze použít k inicializaci výukových algoritmů.

1. Vyberte **regresní**  >  **lineární regresi** a přetáhněte ji na plátno kanálu.

1. V paletě modulu rozbalte možnost **školení modulu** oddíl a přetáhněte modul **vlak model** na plátno.

1. Připojte výstup modulu **lineární regrese** k levému vstupu modulu **vlak model** .

1. Připojte výstup školicích dat (levý port) modulu **rozdělení dat** ke správnému vstupu modulu **vlak model** .
    
    > [!IMPORTANT]
    > Ujistěte se, že levé výstupní porty **rozdělených dat** se připojují ke **výukového modelu**. Levý port obsahuje sadu školení. Pravý port obsahuje sadu testů.

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png"alt-text="Snímek obrazovky znázorňující správnou konfiguraci modulu vlakového modelu. Modul lineární regrese se připojí k levému portu modulu typu vlak a modul rozdělení dat se připojí k pravému portu modelu vlaku.":::

1. Vyberte modul **vlakového modelu** .

1. V podokně podrobností modulu napravo od plátna vyberte Upravit selektor **sloupců** .

1. V dialogovém okně **popisek sloupce** rozbalte rozevírací nabídku a vyberte možnost **názvy sloupců**. 

1. Do textového pole zadejte *Price (cena* ) a zadejte hodnotu, kterou model bude předpovídat.

    >[!IMPORTANT]
    > Ujistěte se, že jste zadali název sloupce přesně. Nezadávejte **cenu** na velká písmena. 

    Váš kanál by měl vypadat takto:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png"alt-text="Snímek obrazovky, který zobrazuje správnou konfiguraci kanálu po přidání modulu vlakového modelu.":::

### <a name="add-the-score-model-module"></a>Přidat modul určení skóre modelu

Jakmile svůj model provedete pomocí 70 procent dat, můžete ho použít k vyhodnocení dalších 30 procent, abyste viděli, jak dobře model funguje.

1. Do vyhledávacího pole zadejte *model skóre* , abyste našli modul **skóre modelu** . Přetáhněte modul na plátno kanálu. 

1. Propojte výstup modulu **Trénování modelu** s levým vstupním portem modulu **Určení skóre modelu**. Propojte výstup testovacích dat (pravý port) modulu **Rozdělení dat** s pravým vstupním portem modulu **Určení skóre modelu**.

### <a name="add-the-evaluate-model-module"></a>Přidání modulu vyhodnocení modelu

Pomocí modulu **vyhodnocení modelu** můžete vyhodnotit, jak dobře model vyhodnotil testovací datovou sadu.

1. Do vyhledávacího pole zadejte *vyhodnotit* a najděte modul **vyhodnocení modelu** . Přetáhněte modul na plátno kanálu. 

1. Propojte výstup modulu určení **skóre modelu** s levým vstupem **modelu vyhodnocení**. 

    Konečný kanál by měl vypadat přibližně takto:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png"alt-text="Snímek obrazovky znázorňující správnou konfiguraci kanálu":::

## <a name="submit-the-pipeline"></a>Odeslat kanál

Teď, když je váš kanál všechna nastavení, můžete odeslat spuštění kanálu, abyste mohli naučit svůj model strojového učení. V jakémkoli okamžiku můžete odeslat platné spuštění kanálu, které můžete použít ke kontrole změn kanálu během vývoje.

1. V horní části plátna vyberte **Odeslat**.

1. V dialogovém okně **nastavit spuštění kanálu** vyberte **vytvořit novou**.

    > [!NOTE]
    > Experimenty seskupují podobný kanál společně. Pokud kanál spouštíte několikrát, můžete vybrat stejný experiment pro po sobě jdoucí běhy.

    1. Zadejte popisný název **nového názvu experimentu**.

    1. Vyberte **Odeslat**.
    
    Stav spuštění a podrobnosti můžete zobrazit v pravém horním rohu plátna.
    
    Pokud se jedná o první spuštění, může trvat až 20 minut, než se váš kanál dokončí. Výchozí nastavení COMPUTE mají minimální velikost uzlu 0, což znamená, že Návrhář musí přidělit prostředky po nečinnosti. Opakované spuštění kanálu bude trvat kratší dobu, protože výpočetní prostředky už jsou přidělené. Kromě toho Návrhář používá výsledky v mezipaměti pro každý modul k dalšímu zvýšení efektivity.

### <a name="view-scored-labels"></a>Zobrazit popisky s skóre

Po dokončení běhu můžete zobrazit výsledky spuštění kanálu. Nejprve se podívejte na předpovědi vygenerovaný regresním modelem.

1. Klikněte pravým tlačítkem na modul **bodového modelu** a vyberte **vizualizovat** a zobrazte jeho výstup.

    Tady vidíte předpovězené ceny a skutečné ceny z testovacích dat.

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/score-result.png"alt-text="Snímek obrazovky výstupní vizualizace, která zvýrazňuje sloupec popisku s skóre":::

### <a name="evaluate-models"></a>Vyhodnotit modely

Pomocí **modelu vyhodnocení** můžete zjistit, jak dobře byl vyškolený model proveden na testovací datové sadě.

1. Klikněte pravým tlačítkem na modul **vyhodnotit model** a výběrem **vizualizovat** zobrazte jeho výstup.

Pro váš model se zobrazí následující statistiky:

* **Střední absolutní chyba (Mae)**: průměr absolutních chyb. Chyba je rozdíl mezi předpokládanou hodnotou a skutečnou hodnotou.
* **Původní střední hodnota chyby (RMSE)**: druhá odmocnina průměru kvadratických chyb předpovědi provedených v testovací datové sadě.
* **Relativní absolutní chyba**: Průměr absolutních chyb relativních k absolutnímu rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot
* **Relativní kvadratická chyba**: Průměr kvadratických chyb relativních ke kvadratickému rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot
* **Koeficient stanovitelnosti**: označuje se také jako hodnota v hodnotě R. Tato statistická metrika indikuje, jak dobře model odpovídá datům.

Pro každou statistiku chyb platí, že menší hodnota je lepší. Menší hodnota označuje, že předpovědi jsou bližší k skutečným hodnotám. Pro koeficient stanovení je bližší jeho hodnota jednomu (1,0), což je lepší předpovědi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tuto část přeskočte, pokud chcete pokračovat v části 2 tohoto kurzu, [nasazení modelů](tutorial-designer-automobile-price-deploy.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V části druhá část se dozvíte, jak model nasadit jako koncový bod v reálném čase.

> [!div class="nextstepaction"]
> [Pokračovat v nasazování modelů](tutorial-designer-automobile-price-deploy.md)
