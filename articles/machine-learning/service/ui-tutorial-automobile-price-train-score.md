---
title: 'Kurz: předpověď ceny automobilu pomocí vizuálního rozhraní'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí vizuálního rozhraní přetáhnout, bodování a nasadit model strojového učení. Tento kurz je první částí série dvou částí pro předpověď cen automobilů pomocí lineární regrese.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/22/2019
ms.openlocfilehash: 3852531615418ffe5397295bc194de34139d6e81
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792661"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Kurz: předpověď ceny automobilu pomocí vizuálního rozhraní

V tomto výukovém kurzu se naučíte používat rozhraní Azure Machine Learningho vizuálu k vývoji a nasazení řešení prediktivní analýzy, které předpovídá cenu každé auta. 

V první části nastavíte prostředí, přetahujete moduly na interaktivní plátno a spojíte je dohromady, aby se vytvořil kanál Azure Machine Learning.

V první části kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit nový kanál
> * Import dat
> * Příprava dat
> * Výuka modelu strojového učení
> * Vyhodnocení modelu Machine Learning

V [druhé části](ui-tutorial-automobile-price-deploy.md) kurzu se naučíte, jak nasadit prediktivní model jako webovou službu Azure, abyste předpovídat cenu každé auta na základě technických specifikací, které odešlete. 

> [!Note]
>Dokončená verze tohoto kurzu je k dispozici jako vzorový kanál.
>
>Pokud ho chcete najít, **v pracovním prostoru se podívejte do vizuálního rozhraní**. V části **Nový kanál** vyberte **Ukázka 1 – regrese: automobilová předpověď ceny (základní)** .

## <a name="create-a-new-pipeline"></a>Vytvořit nový kanál

Kanály Azure Machine Learning organizují více závislých kroků strojového učení a zpracování dat do jediného prostředku. Kanály vám pomůžou organizovat, spravovat a opakovaně používat složité pracovní postupy strojového učení napříč projekty a uživateli. Pokud chcete vytvořit kanál Azure Machine Learning, potřebujete pracovní prostor služby Azure Machine Learning. V této části se dozvíte, jak tyto prostředky vytvořit.

### <a name="create-a-new-workspace"></a>Vytvořit nový pracovní prostor

Pokud máte pracovní prostor služby Azure Machine Learning, přejděte k další části.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="create-the-pipeline"></a>Vytvoření kanálu

1. Přihlaste se k [ml.Azure.com](https://ml.azure.com) a vyberte pracovní prostor, se kterým chcete pracovat.

1. Vyberte **vizuální rozhraní**.

    ![Snímek obrazovky s vizuálním pracovním prostorem, který ukazuje, jak přistupovat k vizuálnímu rozhraní](./media/ui-tutorial-automobile-price-train-score/launch-visual-interface.png)

1. Vyberte **snadno použitelné předem připravené moduly**.

1. V horní části plátna vyberte výchozí název kanálu, který jste **vytvořili** , a přejmenujte ho na něco smysluplného. Například **"předpověď ceny automobilu"** . Název nemusí být jedinečný.

## <a name="import-data"></a>Import dat

K dispozici je několik ukázkových datových sad, které jsou součástí vizuálního rozhraní, abyste se mohli experimentovat. Pro tento kurz použijte ukázková data o **cenách automobilu (RAW)** . 

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

Datové sady obvykle vyžadují před analýzou určitý předzpracování. Při vizualizaci datové sady jste si pravděpodobně všimli některých chybějících hodnot. Tyto chybějící hodnoty se musí vyčistit, aby model mohl data správně analyzovat. Odeberete sloupce s velkým počtem chybějících hodnot a odeberete všechny řádky, které obsahují chybějící hodnoty.

### <a name="remove-a-column"></a>Odebrání sloupce

Při výukovém modelu je nutné provést něco o chybějících datech. Ve sloupci **normalizované ztráty** v této datové sadě chybí mnoho hodnot, takže tento sloupec z modelu zcela vyloučíte.

1. Vyberte sloupce dat, se kterými chcete pracovat. Do pole Hledat v horní části palety zadejte **Select** a vyhledejte modul **Výběr sloupců v datové sadě** .

1. Klikněte na plátno a přetáhněte modul **Výběr sloupců v datové sadě** na plátno. Přetáhněte modul pod modul DataSet.

1. Připojte datovou sadu, kterou jste přidali dříve, do modulu **Výběr sloupců v datové sadě** kliknutím a přetažením. Přetáhněte z výstupního portu datové sady, což je malý kroužek v dolní části datové sady na plátně, na vstupní port pro **Výběr sloupců v datové sadě**, což je malý kruh v horní části modulu.

    > [!TIP]
    > Pokud připojíte výstupní port jednoho modulu ke vstupnímu portu jiného, vytvoříte tok dat prostřednictvím kanálu.
    >

    ![Připojit moduly](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Vyberte modul **Výběr sloupců v datové sadě** .

1. V podokně **vlastnosti** napravo od plátna vyberte **parametry**  > **Upravit sloupec**.

1. Vyberte **+** pro přidání nového pravidla.

1. V rozevírací nabídce vyberte **vyloučit** a **názvy sloupců**.
    
1. Do textového pole zadejte **normalizované ztráty** .

1. V pravém dolním rohu výběrem **Uložit** zavřete selektor sloupců.

    ![Vyloučení sloupce](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    V podokně vlastnosti se zobrazí vyloučený sloupec **normalizované ztráty** .

1. Vyberte modul **Výběr sloupců v datové sadě** . 

1. V okně **vlastnosti**vyberte **parametry**  > **Komentář** a zadejte "vyloučit normalizované ztráty".

### <a name="clean-missing-data"></a>Vyčistit chybějící data

Ve vaší datové sadě ještě chybí hodnoty po odebrání sloupce **normalizované ztráty** . Zbývající chybějící data můžete odebrat pomocí modulu **Vyčištění chybějících dat** .

> [!TIP]
> Vyčištění chybějících hodnot ze vstupních dat je předpokladem pro použití většiny modulů v rámci vizuálního rozhraní.

1. Do vyhledávacího pole zadejte **vyčistit** a vyhledejte modul **Vyčištění chybějících dat** .

1. Přetáhněte modul **Vyčištění chybějících dat** na plátno kanálu a připojte ho k modulu **Výběr sloupců v datové sadě** . 

1. V podokně Vlastnosti vyberte v **režimu čištění**možnost **odstranit celý řádek** .

1. V podokně Vlastnosti zadejte "odebrat řádky chybějících hodnot". v poli **Komentář** .  

    Váš kanál by teď měl vypadat nějak takto:
    
    ![vybrat – sloupec](./media/ui-tutorial-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Výuka modelu strojového učení

Teď, když jsou data předzpracovaná, můžete vytvořit prediktivní model. Vaše data budete používat ke výukě modelu. Pak otestujete model, abyste viděli, jak úzce je možné předpovědět ceny.

### <a name="select-an-algorithm"></a>Výběr algoritmu

**Klasifikace** a **regrese** jsou dva typy technik strojového učení se supervizí. **Klasifikace** předpovídá odpověď ze definované sady kategorií, jako je například barva (červená, modrá nebo zelená). **Regrese** se používá k předpovídání čísla.

Vzhledem k tomu, že chcete odhadnout cenu, což je číslo, můžete použít regresní algoritmus. V tomto příkladu použijete model lineární regrese.

### <a name="split-the-data"></a>Rozdělení dat

Použijte svá data pro školení modelu a testujte je rozdělením dat do dvou samostatných datových sad.

1. Zadejte **rozdělená data** do vyhledávacího pole, abyste našli modul **rozdělení dat** a připojili ho k levému portu modulu **Vyčištění chybějících dat** .

1. Vyberte modul **rozdělit data** .

1. V podokně Vlastnosti nastavte **zlomek řádků v první výstupní sadě dat** na 0,7.

    Tím je rozděleno 70 procent dat za účelem výuky modelu a při testování obsahuje zpět 30 procent.

1. V podokně Vlastnosti zadejte "rozdělit datovou sadu do sady školení (0,7) a sadu testů (0,3)." v poli **Komentář** .

### <a name="train-the-model"></a>Trénování modelu

Zajistěte si model tím, že mu udělíte sadu dat, která obsahuje cenu. Model vyhledá data a vyhledá korelaci mezi funkcemi automobilu a jeho cenou.

1. Chcete-li vybrat sledovací algoritmus, zrušte zaškrtnutí políčka pro hledání palety modulu.

1. Rozbalte **Machine Learning algoritmy**.
    
    Tímto se zobrazí několik kategorií modulů, které je možné použít k inicializaci algoritmů strojového učení.

1. Pro tento kanál vyberte **regresi**  > **lineární regresi** a přetáhněte ji na plátno kanálu.

1. Najděte modul **vlakového modelu** a přetáhněte ho na plátno kanálu. 

1. Připojte výstup modulu lineární regrese k levému vstupu modulu vlak model.

1. Připojte výstup školicích dat (levý port) modulu **rozdělení dat** ke správnému vstupu modulu **vlak model** .

    ![Snímek obrazovky znázorňující správnou konfiguraci modulu vlakového modelu. Modul lineární regrese se připojí k levému portu modulu vlakového modelu a modul rozdělit data se připojí k pravému portu modelu vlaku.](./media/ui-tutorial-automobile-price-train-score/pipeline-train-model.png)

1. Vyberte modul **vlakového modelu** .

1. V podokně Vlastnosti vyberte možnost **Upravit selektor sloupců** .

1. V dialogovém okně **popisek sloupce** rozbalte rozevírací nabídku a vyberte **názvy sloupců**. Do textového pole zadejte **Price**. Cena je hodnota, kterou model hodlá předpovědět.

    Váš kanál by měl vypadat takto:

    ![Snímek obrazovky, který zobrazuje správnou konfiguraci kanálu po přidání modulu vlakového modelu.](./media/ui-tutorial-automobile-price-train-score/pipeline-train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Vyhodnocení modelu Machine Learning

Teď, když jste pronaučili model pomocí 70 procent vašich dat, můžete ho použít k vyhodnocení dalších 30 procent dat, abyste viděli, jak dobře model funguje.

1. Do vyhledávacího pole zadejte **model skóre** a najděte modul **skóre modelu** a přetáhněte ho na plátno kanálu. 

1. Připojte výstup modulu **vlak model** k levému vstupnímu portu **modelu skóre**. Připojte výstup testovacích dat (pravý port) modulu **rozdělení dat** ke správnému vstupnímu portu **modelu skóre**.

1. Do vyhledávacího pole zadejte **vyhodnocení** pro vyhledání **modelu vyhodnocení** a přetáhněte modul na plátno kanálu. 

1. Propojte výstup modulu určení **skóre modelu** s levým vstupem **modelu vyhodnocení**. 

    Konečný kanál by měl vypadat přibližně takto:

    ![Snímek obrazovky znázorňující správnou konfiguraci kanálu](./media/ui-tutorial-automobile-price-train-score/pipeline-final-graph.png)

### <a name="run-the-pipeline"></a>Spuštění kanálu

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

### <a name="view-results"></a>Zobrazení výsledků

Po dokončení běhu můžete zobrazit výsledky spuštění kanálu. 

1. Výběrem modulu **skóre** modelu Zobrazte výstup z modulu **skóre modelu** .

1. V podokně **vlastnosti** vyberte možnost **výstupy**  > **vizualizace**. Na výstupu se zobrazí predikované hodnoty ceny a známé hodnoty v testovacích datech.

    ![Snímek obrazovky výstupní vizualizace, která zvýrazňuje sloupec označení skóre](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Výběrem modulu **skóre** modelu Zobrazte výstup z modulu **vyhodnocení modelu** .

1. V podokně **vlastnosti** vyberte **výstup**  > **vizualizace**a pak vyberte **vizualizovat**.

Pro váš model se zobrazí následující statistiky:

* **Střední absolutní chyba (Mae)** : průměr absolutních chyb (chyba je rozdíl mezi předpovězenou a skutečnou hodnotou).
* **Původní střední hodnota chyby (RMSE)** : druhá odmocnina průměru kvadratických chyb předpovědi provedených v testovací datové sadě.
* **Relativní absolutní chyba**: Průměr absolutních chyb relativních k absolutnímu rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot
* **Relativní kvadratická chyba**: Průměr kvadratických chyb relativních ke kvadratickému rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot
* **Koeficient stanovitelnosti**: označuje se také jako hodnota v metrice R. Jedná se o statistickou metriku, která označuje, jak dobře model odpovídá datům.

Pro každou statistiku chyb platí, že menší hodnota je lepší. Menší hodnota označuje, že předpověď přesněji odpovídá skutečným hodnotám. U koeficientu stanovitelnosti je bližší jeho hodnota jednomu (1,0), což je lepší předpovědi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V první části tohoto kurzu jste dokončili tyto kroky:

* Vytvoření kanálu
* Příprava dat
* Vyškolený model
* Skóre a vyhodnocení modelu

V části druhá část se dozvíte, jak model nasadit jako koncový bod v reálném čase.

> [!div class="nextstepaction"]
> [Pokračovat v nasazování modelů](ui-tutorial-automobile-price-deploy.md)
