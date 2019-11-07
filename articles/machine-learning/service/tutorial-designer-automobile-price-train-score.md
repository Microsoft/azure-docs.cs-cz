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
ms.openlocfilehash: 3df1a0430983b52d8a791acabbd03efe19055697
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721769"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Kurz: předpověď ceny automobilu pomocí návrháře (Preview)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

V tomto kurzu se dozvíte, jak pomocí návrháře Azure Machine Learning vyvíjet a nasazovat řešení prediktivní analýzy, které předpovídá cenu každé auta. 

V první části nastavíte prostředí, přetahujete moduly na interaktivní plátno a spojíte je dohromady, aby se vytvořil kanál Azure Machine Learning.

V první části kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit nový kanál
> * Import dat
> * Příprava dat
> * Výuka modelu strojového učení
> * Vyhodnocení modelu Machine Learning

V [druhé části](tutorial-designer-automobile-price-deploy.md) kurzu se naučíte, jak nasadit prediktivní model jako Inferencing koncový bod v reálném čase, abyste mohli předpovídat cenu každé auta na základě technických specifikací, které vám posíláte. 

> [!Note]
>Dokončená verze tohoto kurzu je k dispozici jako vzorový kanál.
>
>Pokud ho chcete najít, **v pracovním prostoru**se podívejte do návrháře. V části **Nový kanál** vyberte **Ukázka 1 – regrese: automobilová předpověď ceny (základní)** .

## <a name="create-a-new-pipeline"></a>Vytvořit nový kanál

Kanály Azure Machine Learning organizují více závislých kroků strojového učení a zpracování dat do jediného prostředku. Kanály vám pomůžou organizovat, spravovat a opakovaně používat složité pracovní postupy strojového učení napříč projekty a uživateli. Pokud chcete vytvořit kanál Azure Machine Learning, potřebujete Azure Machine Learning pracovní prostor. V této části se dozvíte, jak tyto prostředky vytvořit.

### <a name="create-a-new-workspace"></a>Vytvořit nový pracovní prostor

Pokud máte pracovní prostor Azure Machine Learning s **edicí Enterprise**, [přejděte k další části](#create-the-pipeline).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>Vytvoření kanálu

1. Přihlaste se k [ml.Azure.com](https://ml.azure.com) a vyberte pracovní prostor, se kterým chcete pracovat.

1. Vyberte **Návrhář**.

    ![Snímek obrazovky s vizuálním pracovním prostorem, který ukazuje, jak získat přístup k Návrháři](./media/ui-tutorial-automobile-price-train-score/launch-visual-interface.png)

1. Vyberte **snadno použitelné předem připravené moduly**.

1. V horní části plátna vyberte výchozí název kanálu, **"vytvoření kanálu-zapnuto..."** a přejmenujte ho na něco smysluplného. Například **"předpověď ceny automobilu"** . Název nemusí být jedinečný.

## <a name="import-data"></a>Import dat

Návrhář obsahuje několik ukázkových datových sad, které můžete experimentovat s nástrojem. Pro tento kurz použijte **data o cenách automobilu (RAW)** . 

1. Vlevo od plátna kanálu je paleta datových sad a modulů. Vyberte **datové sady** a zobrazte si část **ukázky** a zobrazte si dostupné ukázkové datové sady.

1. Vyberte datovou sadu, **údaje o cenách automobilu (RAW)** a přetáhněte ji na plátno.

   ![Přetáhněte data na plátno.](./media/ui-tutorial-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Vizualizace dat

Můžete vizualizovat data a pochopit datovou sadu, kterou budete používat.

1. Vyberte modul **cena za automobilový data (RAW)** .

1. V podokně **vlastnosti** napravo od plátna vyberte **výstupy**.

1. Vyberte ikonu grafu pro vizualizaci dat.

    ![Vizualizace dat](./media/ui-tutorial-automobile-price-train-score/visualize-data.png)

1. Výběrem různých sloupců v okně data zobrazíte informace o každém z nich.

    Každý řádek představuje automobil a proměnné přidružené k jednotlivým automobilům se zobrazí jako sloupce. V této datové sadě jsou 205 řádky a 26 sloupců.

## <a name="prepare-data"></a>Příprava dat

Datové sady obvykle vyžadují před analýzou určitý předzpracování. Při kontrole datové sady jste si pravděpodobně všimli některých chybějících hodnot. Tyto chybějící hodnoty je potřeba vyčistit, aby model mohl správně analyzovat data.

### <a name="remove-a-column"></a>Odebrání sloupce

Při výukovém modelu je nutné provést něco o chybějících datech. Ve sloupci **normalizované ztráty** v této datové sadě chybí mnoho hodnot, takže tento sloupec z modelu zcela vyloučíte.

1. Do pole Hledat v horní části palety zadejte **Select** a vyhledejte modul **Výběr sloupců v datové sadě** .

1. Klikněte na plátno a přetáhněte modul **Výběr sloupců v datové sadě** na plátno. Přetáhněte modul pod modul DataSet.

1. Připojte datovou sadu **dat o ceně automobilu (RAW)** k poli **Vybrat sloupce v datové sadě**. Přetáhněte z výstupního portu datové sady, což je malý kroužek v dolní části datové sady na plátně, na vstupní port pro **Výběr sloupců v datové sadě**, což je malý kruh v horní části modulu.

    > [!TIP]
    > Pokud připojíte výstupní port jednoho modulu ke vstupnímu portu jiného, vytvoříte tok dat prostřednictvím kanálu.
    >

    ![Připojit moduly](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Vyberte modul **Výběr sloupců v datové sadě** .

1. V podokně **vlastnosti** napravo od plátna vyberte **parametry** > **Upravit sloupec**.

1. Vyberte **+** pro přidání nového pravidla.

1. V rozevírací nabídce vyberte **vyloučit** a **názvy sloupců**.
    
1. Do textového pole zadejte **normalizované ztráty** .

1. V pravém dolním rohu výběrem **Uložit** zavřete selektor sloupců.

    ![Vyloučení sloupce](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    V podokně vlastnosti se zobrazí vyloučený sloupec **normalizované ztráty** .

1. Vyberte modul **Výběr sloupců v datové sadě** . 

1. V podokně **vlastnosti** vyberte **parametry** > **Komentář** a zadejte "vyloučit normalizované ztráty".

### <a name="clean-missing-data"></a>Vyčistit chybějící data

Ve vaší datové sadě ještě chybí hodnoty po odebrání sloupce **normalizované ztráty** . Zbývající chybějící data můžete odebrat pomocí modulu **Vyčištění chybějících dat** .

> [!TIP]
> Vyčištění chybějících hodnot ze vstupních dat je předpokladem pro použití většiny modulů v návrháři.

1. Do vyhledávacího pole zadejte **vyčistit** a vyhledejte modul **Vyčištění chybějících dat** .

1. Přetáhněte modul **Vyčištění chybějících dat** na plátno kanálu a připojte ho k modulu **Výběr sloupců v datové sadě** . 

1. V podokně Vlastnosti vyberte v **režimu čištění**možnost **odstranit celý řádek** .

1. V poli **Komentář** k podoknu vlastností zadejte "odebrat chybějící řádky hodnot".  

    Váš kanál by teď měl vypadat nějak takto:
    
    ![vybrat – sloupec](./media/ui-tutorial-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Výuka modelu strojového učení

Teď, když jsou data zpracovaná, můžete vytvořit prediktivní model.

### <a name="select-an-algorithm"></a>Výběr algoritmu

**Klasifikace** a **regrese** jsou dva typy technik strojového učení se supervizí. **Klasifikace** předpovídá odpověď ze definované sady kategorií, jako je například barva (červená, modrá nebo zelená). **Regrese** se používá k předpovídání čísla.

Vzhledem k tomu, že chcete předpovědět cenu, což je číslo, můžete použít regresní algoritmus. V tomto příkladu použijete model lineární regrese.

### <a name="split-the-data"></a>Rozdělení dat

Rozdělte data do dvou samostatných datových sad pro účely školení modelu a testování.

1. Do vyhledávacího pole zadejte **rozdělená data** , abyste našli modul **rozdělení dat** a připojili ho k levému portu modulu **Vyčištění chybějících dat** .

1. Vyberte modul **rozdělit data** .

1. V podokně Vlastnosti nastavte **zlomek řádků v první výstupní sadě dat** na 0,7.

    Toto rozdělení 70 procent dat za účelem analýzy modelu a 30 procent pro jeho testování.

1. V poli **Komentář** k vlastnostem zadejte "rozdělit datovou sadu do sady školení (0,7) a testovací sadu (0,3)."

### <a name="train-the-model"></a>Trénování modelu

Zajistěte si model tím, že mu udělíte sadu dat, která obsahuje cenu. Model prochází data a vyhledává korelace mezi funkcemi automobilu a cenou za účelem vytvoření modelu.

1. Chcete-li vybrat sledovací algoritmus, zrušte zaškrtnutí políčka pro hledání palety modulu.

1. Rozbalte **Machine Learning algoritmy**.
    
    Zobrazí se několik kategorií modulů, které můžete použít k inicializaci výukových algoritmů.

1. Vyberte **regresi** > **lineární regresi** a přetáhněte ji na plátno kanálu.

1. Najděte modul **vlakového modelu** a přetáhněte ho na plátno kanálu. 

1. Připojte výstup modulu **lineární regrese** k levému vstupu modulu **vlak model** .

1. Připojte výstup školicích dat (levý port) modulu **rozdělení dat** ke správnému vstupu modulu **vlak model** .

    ![Snímek obrazovky znázorňující správnou konfiguraci modulu vlakového modelu. Modul lineární regrese se připojí k levému portu modulu vlakového modelu a modul rozdělit data se připojí k pravému portu modelu vlaku.](./media/ui-tutorial-automobile-price-train-score/pipeline-train-model.png)

1. Vyberte modul **vlakového modelu** .

1. V podokně Vlastnosti vyberte možnost **Upravit selektor sloupců** .

1. V dialogovém okně **popisek sloupce** rozbalte rozevírací nabídku a vyberte **názvy sloupců**. 

1. Do textového pole zadejte **Price**. Cena je hodnota, kterou model hodlá předpovědět.

    Váš kanál by měl vypadat takto:

    ![Snímek obrazovky, který zobrazuje správnou konfiguraci kanálu po přidání modulu vlakového modelu.](./media/ui-tutorial-automobile-price-train-score/pipeline-train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Vyhodnocení modelu Machine Learning

Po školení modelu pomocí 70 procent dat ho můžete použít k vyhodnocení dalších 30 procent, abyste viděli, jak dobře model funguje.

1. Do vyhledávacího pole zadejte **model skóre** a najděte modul **skóre modelu** a přetáhněte ho na plátno kanálu. 

1. Připojte výstup modulu **vlak model** k levému vstupnímu portu **modelu skóre**. Připojte výstup testovacích dat (pravý port) modulu **rozdělení dat** ke správnému vstupnímu portu **modelu skóre**.

1. Do vyhledávacího pole zadejte **vyhodnotit** a najděte **model vyhodnocení** a přetáhněte ho na plátno kanálu. 

1. Propojte výstup modulu určení **skóre modelu** s levým vstupem **modelu vyhodnocení**. 

    Konečný kanál by měl vypadat přibližně takto:

    ![Snímek obrazovky znázorňující správnou konfiguraci kanálu](./media/ui-tutorial-automobile-price-train-score/pipeline-final-graph.png)

### <a name="run-the-pipeline"></a>Spuštění kanálu

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

### <a name="view-results"></a>Zobrazení výsledků

Po dokončení běhu můžete zobrazit výsledky spuštění kanálu. 

1. Vyberte modul určení **skóre modelu** , ve kterém chcete zobrazit jeho výstup.

1. V podokně **vlastnosti** vyberte možnost **výstupy** > **vizualizace**.

    Tady vidíte předpovězené ceny a skutečné ceny z testovacích dat.

    ![Snímek obrazovky výstupní vizualizace, která zvýrazňuje sloupec označení skóre](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Vyberte modul **vyhodnocení modelu** a zobrazte jeho výstup.

1. V podokně **vlastnosti** vyberte **výstup** > **vizualizace**.

Pro váš model se zobrazí následující statistiky:

* **Střední absolutní chyba (Mae)** : průměr absolutních chyb (chyba je rozdíl mezi předpovězenou a skutečnou hodnotou).
* **Původní střední hodnota chyby (RMSE)** : druhá odmocnina průměru kvadratických chyb předpovědi provedených v testovací datové sadě.
* **Relativní absolutní chyba**: Průměr absolutních chyb relativních k absolutnímu rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot
* **Relativní kvadratická chyba**: Průměr kvadratických chyb relativních ke kvadratickému rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot
* **Koeficient stanovitelnosti**: označuje se také jako hodnota v metrice R. Jedná se o statistickou metriku, která označuje, jak dobře model odpovídá datům.

Pro každou statistiku chyb platí, že menší hodnota je lepší. Menší hodnota označuje, že předpovědi jsou bližší k skutečným hodnotám. Pro koeficient stanovení je bližší jeho hodnota jednomu (1,0), což je lepší předpovědi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V první části tohoto kurzu jste dokončili následující úlohy:

* Vytvoření kanálu
* Příprava dat
* Trénování modelu
* Skóre a vyhodnocení modelu

V části druhá část se dozvíte, jak model nasadit jako koncový bod v reálném čase.

> [!div class="nextstepaction"]
> [Pokračovat v nasazování modelů](tutorial-designer-automobile-price-deploy.md)
