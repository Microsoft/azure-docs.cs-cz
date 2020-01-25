---
title: 'Kurz: předpověď ceny automobilu pomocí návrháře'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí rozhraní přetažení využít výuku, skóre a nasazení modelu strojového učení. Tento kurz je první částí série dvou částí pro předpověď cen automobilů pomocí lineární regrese.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 639a61cddde27b0d989e5a3dd4c599c353182a73
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720154"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Kurz: předpověď ceny automobilu pomocí návrháře (Preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

V tomto kurzu se dozvíte, jak pomocí návrháře Azure Machine Learning vyvíjet a nasazovat řešení prediktivní analýzy, které předpovídá cenu každé auta.

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
>Pokud ho chcete najít, v pracovním prostoru se podívejte do návrháře. V části **Nový kanál** vyberte **Ukázka 1 – regrese: automobilová předpověď ceny (základní)** .

## <a name="create-a-new-pipeline"></a>Vytvořit nový kanál

Kanály Azure Machine Learning organizují více kroků strojového učení a zpracování dat do jednoho prostředku. Kanály umožňují organizovat, spravovat a opakovaně používat složité pracovní postupy strojového učení napříč projekty a uživateli.

Pokud chcete vytvořit kanál Azure Machine Learning, potřebujete Azure Machine Learning pracovní prostor. V této části se dozvíte, jak tyto prostředky vytvořit.

### <a name="create-a-new-workspace"></a>Vytvořit nový pracovní prostor

Pokud máte pracovní prostor Azure Machine Learning s edicí Enterprise, [přejděte k další části](#create-the-pipeline).

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>Vytvoření kanálu

1. Přihlaste se k [ml.Azure.com](https://ml.azure.com)a vyberte pracovní prostor, se kterým chcete pracovat.

1. Vyberte **Návrhář**.

    ![Snímek obrazovky s vizuálním pracovním prostorem, který ukazuje, jak získat přístup k Návrháři](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Vyberte **snadno použitelné předem připravené moduly**.

1. V horní části plátna vyberte výchozí kanál názvu kanálu **– Vytvořeno**. Přejmenujte ho na *automobilovou předpověď cen*. Název nemusí být jedinečný.

## <a name="import-data"></a>Import dat

Návrhář obsahuje několik ukázkových datových sad, které můžete experimentovat s nástrojem. Pro tento kurz použijte **data o cenách automobilu (RAW)** . 

1. Vlevo od plátna kanálu je paleta datových sad a modulů. Vyberte **datové sady**a pak zobrazte část **ukázky** a zobrazte si dostupné ukázkové datové sady.

1. Vyberte **data automobil auto data (Hrubá cena)** a přetáhněte je na plátno.

   ![Přetáhněte data na plátno.](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Vizualizace dat

Můžete vizualizovat data a pochopit datovou sadu, kterou budete používat.

1. Vyberte modul **cena za automobilový data (RAW)** .

1. V podokně vlastnosti napravo od plátna vyberte **výstupy**.

1. Vyberte ikonu grafu pro vizualizaci dat.

    ![Vizualizace dat](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. Výběrem různých sloupců v okně data zobrazíte informace o každém z nich.

    Každý řádek představuje automobil a proměnné přidružené k jednotlivým automobilům se zobrazí jako sloupce. V této datové sadě jsou 205 řádky a 26 sloupců.

## <a name="prepare-data"></a>Příprava dat

Datové sady obvykle vyžadují před analýzou určitý předzpracování. Při kontrole datové sady jste si pravděpodobně všimli některých chybějících hodnot. Tyto chybějící hodnoty musí být vyčištěny, aby model mohl správně analyzovat data.

### <a name="remove-a-column"></a>Odebrání sloupce

Při výukovém modelu je nutné provést něco o chybějících datech. Ve sloupci **normalizované ztráty** v této datové sadě chybí mnoho hodnot, takže tento sloupec z modelu zcela vyloučíte.

1. Do pole Hledat v horní části palety zadejte **Select** a vyhledejte modul **Výběr sloupců v datové sadě** .

1. Přetáhněte modul **Výběr sloupců v datové sadě** na plátno. Přetáhněte modul pod modul DataSet.

1. Připojte datovou sadu **dat o ceně automobilu (RAW)** do modulu **Výběr sloupců v datové sadě** . Přetáhněte z výstupního portu datové sady, což je malý kroužek v dolní části datové sady na plátně, na vstupní port pro **Výběr sloupců v datové sadě**, což je malý kruh v horní části modulu.

    > [!TIP]
    > Pokud připojíte výstupní port jednoho modulu ke vstupnímu portu jiného, vytvoříte tok dat prostřednictvím kanálu.
    >

    ![Připojit moduly](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Vyberte modul **Výběr sloupců v datové sadě** .

1. V podokně vlastnosti napravo od plátna vyberte **všechny sloupce**.

1. Vyberte **+** pro přidání nového pravidla.

1. V rozevírací nabídce vyberte **vyloučit** a **názvy sloupců**.
    
1. Do textového pole zadejte *normalizované ztráty* .

1. V pravém dolním rohu výběrem **Uložit** zavřete selektor sloupců.

    ![Vyloučení sloupce](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Vyberte modul **Výběr sloupců v datové sadě** . 

1. V podokně Vlastnosti vyberte textové pole **Komentář** a zadejte *vyloučit normalizované ztráty*.

    V grafu se zobrazí komentáře, které vám pomůžou organizovat svůj kanál.

### <a name="clean-missing-data"></a>Vyčištění chybějících dat

V datové sadě ještě chybí hodnoty po odebrání sloupce **normalizované ztráty** . Zbývající chybějící data můžete odebrat pomocí modulu **Vyčištění chybějících dat** .

> [!TIP]
> Vyčištění chybějících hodnot ze vstupních dat je předpokladem pro použití většiny modulů v návrháři.

1. Do vyhledávacího pole zadejte **vyčistit** a vyhledejte modul **Vyčištění chybějících dat** .

1. Přetáhněte modul **Vyčištění chybějících dat** na plátno kanálu. Připojte ho k modulu **Výběr sloupců v datové sadě** . 

1. V podokně Vlastnosti vyberte v **režimu čištění**možnost **odstranit celý řádek** .

1. V poli **Komentář** k podoknu vlastností zadejte příkaz *odebrat chybějící řádky hodnoty*. 

    Váš kanál by teď měl vypadat nějak takto:
    
    ![Vybrat – sloupec](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Výuka modelu strojového učení

Teď, když máte moduly, které jsou pro zpracování dat k dispozici, můžete nastavit školicí moduly.

Vzhledem k tomu, že chcete odhadnout cenu, což je číslo, můžete použít regresní algoritmus. V tomto příkladu použijete model lineární regrese.

### <a name="split-the-data"></a>Rozdělení dat

Rozdělení dat je běžnou úlohou ve strojovém učení. Data budete rozdělit do dvou samostatných datových sad. Jedna datová sada povede model a druhá bude testovat, jak dobře byl model proveden.

1. Do vyhledávacího pole zadejte **rozdělená data** , abyste mohli najít modul **rozdělených dat** . Připojte levý port modulu **Vyčištění chybějících dat** k modulu **rozdělit data** .

    > [!IMPORTANT]
    > Ujistěte se, že levé výstupní porty **vyčistit chybějící data** se připojují k **rozděleným datům**. Levý port obsahuje vyčištěná data. Pravý port obsahuje data, která jsou k diskošíku.

1. Vyberte modul **rozdělit data** .

1. V podokně Vlastnosti nastavte **zlomek řádků v první výstupní sadě dat** na 0,7.

    Tato možnost rozdělí 70 procent dat za účelem výuky modelu a 30 procent pro jeho testování. Datový objekt 70% bude přístupný prostřednictvím levého výstupního portu. Zbývající data budou k dispozici prostřednictvím správného výstupního portu.

1. V poli **Komentář** podokna Vlastnosti zadejte *rozdělit datovou sadu do sady školení (0,7) a sady testů (0,3)* .

### <a name="train-the-model"></a>Trénování modelu

Vytvořte si model tak, že mu udělíte datovou sadu, která obsahuje cenu. Algoritmus vytvoří model, který vysvětluje vztah mezi funkcemi a cenou, jak je znázorněno v školicích datech.

1. Chcete-li vybrat sledovací algoritmus, zrušte zaškrtnutí políčka pro hledání palety modulu.

1. Rozbalte **Machine Learning algoritmy**.
    
    Tato možnost zobrazí několik kategorií modulů, které lze použít k inicializaci výukových algoritmů.

1. Vyberte **regresi** > **lineární regresi**a přetáhněte ji na plátno kanálu.

1. Najděte modul **vlakového modelu** a přetáhněte ho na plátno kanálu. 

1. Připojte výstup modulu **lineární regrese** k levému vstupu modulu **vlak model** .

1. Připojte výstup školicích dat (levý port) modulu **rozdělení dat** ke správnému vstupu modulu **vlak model** .
    
    > [!IMPORTANT]
    > Ujistěte se, že levé výstupní porty **rozdělených dat** se připojují ke **výukového modelu**. Levý port obsahuje sadu školení. Pravý port obsahuje sadu testů.

    ![Snímek obrazovky znázorňující správnou konfiguraci modulu vlakového modelu. Modul lineární regrese se připojí k levému portu modulu vlakového modelu a modul rozdělit data se připojí k pravému portu modelu vlaku.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. Vyberte modul **vlakového modelu** .

1. V podokně Vlastnosti vyberte možnost **Upravit selektor sloupců** .

1. V dialogovém okně **popisek sloupce** rozbalte rozevírací nabídku a vyberte možnost **názvy sloupců**. 

1. Do textového pole zadejte *Price (cena* ) a zadejte hodnotu, kterou model bude předpovídat.

    Váš kanál by měl vypadat takto:

    ![Snímek obrazovky, který zobrazuje správnou konfiguraci kanálu po přidání modulu vlakového modelu.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

## <a name="score-a-machine-learning-model"></a>Určení skóre modelu Machine Learning

Jakmile svůj model provedete pomocí 70 procent dat, můžete ho použít k vyhodnocení dalších 30 procent, abyste viděli, jak dobře model funguje.

1. Do vyhledávacího pole zadejte *model skóre* , abyste našli modul **skóre modelu** . Přetáhněte modul na plátno kanálu. 

1. Připojte výstup modulu **vlak model** k levému vstupnímu portu **modelu skóre**. Připojte výstup testovacích dat (pravý port) modulu **rozdělení dat** ke správnému vstupnímu portu **modelu skóre**.

## <a name="evaluate-a-machine-learning-model"></a>Vyhodnocení modelu Machine Learning

Pomocí modulu **vyhodnocení modelu** můžete vyhodnotit, jak dobře model vyhodnotil testovací datovou sadu.

1. Do vyhledávacího pole zadejte *vyhodnotit* a najděte modul **vyhodnocení modelu** . Přetáhněte modul na plátno kanálu. 

1. Propojte výstup modulu určení **skóre modelu** s levým vstupem **modelu vyhodnocení**. 

    Konečný kanál by měl vypadat přibližně takto:

    ![Snímek obrazovky znázorňující správnou konfiguraci kanálu](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

## <a name="run-the-pipeline"></a>Spuštění kanálu

[!INCLUDE [aml-ui-create-training-compute](../../includes/aml-ui-create-training-compute.md)]

### <a name="view-scored-labels"></a>Zobrazit popisky s skóre

Po dokončení běhu můžete zobrazit výsledky spuštění kanálu. Nejprve se podívejte na předpovědi vygenerovaný regresním modelem.

1. Vyberte modul určení **skóre modelu** , ve kterém chcete zobrazit jeho výstup.

1. V podokně Vlastnosti vyberte možnost **výstupy** > ikona grafu ![vizualizace](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) ikona zobrazit výsledky.

    Tady vidíte předpovězené ceny a skutečné ceny z testovacích dat.

    ![Snímek obrazovky výstupní vizualizace, která zvýrazňuje sloupec popisku s skóre](./media/tutorial-designer-automobile-price-train-score/score-result.png)

### <a name="evaluate-models"></a>Vyhodnotit modely

Pomocí **modelu vyhodnocení** můžete zjistit, jak dobře byl vyškolený model proveden na testovací datové sadě.

1. Vyberte modul **vyhodnocení modelu** a zobrazte jeho výstup.

1. V podokně Vlastnosti vyberte **výstupní** > ikonu grafu ![vizualizaci ikona](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) pro zobrazení výsledků.

Pro váš model se zobrazí následující statistiky:

* **Střední absolutní chyba (Mae)** : průměr absolutních chyb. Chyba je rozdíl mezi předpokládanou hodnotou a skutečnou hodnotou.
* **Původní střední hodnota chyby (RMSE)** : druhá odmocnina průměru kvadratických chyb předpovědi provedených v testovací datové sadě.
* **Relativní absolutní chyba**: Průměr absolutních chyb relativních k absolutnímu rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot
* **Relativní kvadratická chyba**: Průměr kvadratických chyb relativních ke kvadratickému rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot
* **Koeficient stanovitelnosti**: označuje se také jako hodnota v hodnotě R. Tato statistická metrika indikuje, jak dobře model odpovídá datům.

Pro každou statistiku chyb platí, že menší hodnota je lepší. Menší hodnota označuje, že předpovědi jsou bližší k skutečným hodnotám. Pro koeficient stanovení je bližší jeho hodnota jednomu (1,0), což je lepší předpovědi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V první části tohoto kurzu jste dokončili následující úlohy:

* Vytvoření kanálu
* Příprava dat
* Trénování modelu
* Skóre a vyhodnocení modelu

V části druhá část se dozvíte, jak model nasadit jako koncový bod v reálném čase.

> [!div class="nextstepaction"]
> [Pokračovat v nasazování modelů](tutorial-designer-automobile-price-deploy.md)
