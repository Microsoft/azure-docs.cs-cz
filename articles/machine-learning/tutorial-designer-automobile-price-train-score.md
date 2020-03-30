---
title: 'Kurz: Předvídejte cenu automobilu s designérem'
titleSuffix: Azure Machine Learning
description: Naučte se trénovat, skóre a nasadit model strojového učení pomocí rozhraní drag-and-drop. Tento kurz je první částí dvoudílné série o předpovídání cen automobilů pomocí lineární regrese.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 03/12/2020
ms.openlocfilehash: 0488002352d222abb0166737f9a042060b1a1bb1
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389422"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Kurz: Předvídejte cenu automobilu s designérem (náhled)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

V tomto dvoudílném kurzu se dozvíte, jak pomocí návrháře Azure Machine Learning trénovat a nasadit model strojového učení, který předpovídá cenu libovolného vozu. Návrhář je nástroj přetažení, který umožňuje vytvářet modely strojového učení bez jediného řádku kódu.

V první části kurzu se naučíte, jak:

> [!div class="checklist"]
> * Vytvořte nový kanál.
> * Import dat.
> * Připravte data.
> * Trénování modelu strojového učení.
> * Vyhodnoťte model strojového učení.

Ve [druhé části](tutorial-designer-automobile-price-deploy.md) kurzu nasadíte model jako koncový bod pro odvození v reálném čase, abyste předpověděli cenu libovolného vozu na základě technických specifikací, které odešlete. 

> [!NOTE]
>Dokončená verze tohoto kurzu je k dispozici jako ukázkový kanál.
>
>Chcete-li ji najít, přejděte k návrháři ve vašem pracovním prostoru. V části **Nový kanál** vyberte **ukázku 1 – regrese: Predikce cen automobilů (základní).**

## <a name="create-a-new-pipeline"></a>Vytvoření nového kanálu

Kanály Azure Machine Learning organizují více kroků strojového učení a zpracování dat do jednoho prostředku. Kanály umožňují organizovat, spravovat a znovu používat složité pracovní postupy strojového učení napříč projekty a uživateli.

K vytvoření kanálu Azure Machine Learning potřebujete pracovní prostor Azure Machine Learning. V této části se dozvíte, jak vytvořit oba tyto prostředky.

### <a name="create-a-new-workspace"></a>Vytvoření nového pracovního prostoru

Chcete-li použít návrháře, nejprve potřebujete pracovní prostor Azure Machine Learning. Pracovní prostor je prostředek nejvyšší úrovně pro Azure Machine Learning, poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte v Azure Machine Learning.

Pokud máte pracovní prostor Azure Machine Learning s edicí Enterprise, [přejděte k další části](#create-the-pipeline).

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>Vytvoření kanálu

1. Přihlaste se k <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>a vyberte pracovní prostor, se kterým chcete pracovat.

1. Vyberte **návrháře**.

    ![Snímek obrazovky s vizuálním pracovním prostorem, který ukazuje, jak získat přístup k návrháři](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Vyberte **snadno použitelné předem sestavené moduly**.

1. V horní části plátna vyberte výchozí název kanálu **Pipeline-Created-on**. Přejmenujte jej na *Predikce cen automobilů*. Název nemusí být jedinečný.

## <a name="set-the-default-compute-target"></a>Nastavení výchozího výpočetního cíle

Kanál běží na výpočetní cíl, což je výpočetní prostředek, který je připojený k vašemu pracovnímu prostoru. Po vytvoření výpočetní cíl, můžete znovu použít pro budoucí spuštění.

Můžete nastavit **výchozí výpočetní cíl** pro celý kanál, který bude říkat každý modul použít stejný výpočetní cíl ve výchozím nastavení. Můžete však zadat výpočetní cíle pro modul.

1. Vedle názvu kanálu vyberte ikonu Ozubená kola](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) **S ikonou** ![ozubeného kola v horní části plátna a otevřete podokno **Nastavení.**

1. V podokně **Nastavení** napravo od plátna vyberte **Vybrat cíl výpočetního výkonu**.

    Pokud už máte dostupný výpočetní cíl, můžete ho vybrat a spustit tento kanál.

    > [!NOTE]
    > Návrhář může spouštět experimenty jenom na cílech Azure Machine Learning Compute. Ostatní výpočetní cíle se nezobrazí.

1. Zadejte název výpočetního prostředku.

1. Vyberte **Uložit**.

    > [!NOTE]
    > Vytvoření výpočetního prostředku trvá přibližně pět minut. Po vytvoření prostředku jej můžete znovu použít a přeskočit tuto čekací dobu pro budoucí spuštění.
    >
    > Výpočetní prostředek se automaticky škáluje na nulové uzly, když je nečinný, aby se ušetřily náklady. Při použití znovu po zpoždění, může dojít přibližně pět minut čekací doby, zatímco se škáluje zpět nahoru.

## <a name="import-data"></a>Import dat

Existuje několik ukázkových datových sad zahrnutých v návrháři, se kterými můžete experimentovat. Pro účely tohoto kurzu použijte **data o cenách automobilu (Raw)**. 

1. Vlevo od plátna potrubí je paleta datových sad a modulů. Vyberte **Datové sady**a pak zobrazte oddíl **Vzorky,** abyste zobrazili dostupné ukázkové datové sady.

1. Vyberte datovou sadu **Dat youcen (Nezpracovaný)** a přetáhněte je na plátno.

   ![Přetažení dat na plátno](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Vizualizace dat

Můžete vizualizovat data, abyste porozuměli datové sadě, kterou budete používat.

1. Vyberte modul **Data cen automobilů (Raw).**

1. V podokně podrobností modulu vpravo od plátna vyberte **Výstupy + protokol**.

1. Vyberte ikonu grafu pro vizualizaci dat.

    ![Vizualizace dat](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. Vyberte různé sloupce v datovém okně a zobrazte informace o každém z nich.

    Každý řádek představuje automobil a proměnné přidružené ke každému automobilu se zobrazí jako sloupce. V této datové sadě je 205 řádků a 26 sloupců.

## <a name="prepare-data"></a>Příprava dat

Datové sady obvykle vyžadují určité předběžné zpracování před analýzou. Možná jste si všimli některých chybějících hodnot při kontrole datové sady. Tyto chybějící hodnoty musí být vyčištěny, aby model mohl správně analyzovat data.

### <a name="remove-a-column"></a>Odebrání sloupce

Když trénujete model, musíte něco udělat s daty, která chybí. V této datové sadě chybí sloupec **normalizované ztráty** mnoho hodnot, takže tento sloupec zcela vyloučíte z modelu.

1. V paletě modulů nalevo od plátna rozbalte oddíl **Transformace dat** a najděte modul Vybrat sloupce v **datové sadě.**

1. Přetáhněte modul **Vybrat sloupce v datové sadě** na plátno. Zanechá modul pod modulem datové sady.

1. Připojte **datovou sadu Dat ceny automobilu (Raw)** k modulu **Vybrat sloupce v datové sadě.** Přetažení mj. **Select Columns in Dataset**

    > [!TIP]
    > Tok dat prostřednictvím kanálu vytvoříte, když připojíte výstupní port jednoho modulu ke vstupnímu portu jiného.
    >

    ![Připojit moduly](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Vyberte modul **Vybrat sloupce v datové sadě.**

1. V podokně podrobností modulu vpravo od plátna vyberte **Upravit sloupec**.

1. Rozbalte rozevírací seznam **Názvy sloupců** vedle **položky Zahrnout**a vyberte **Všechny sloupce**.

1. Vyberte **+** možnost Přidání nového pravidla.

1. V rozevíracích nabídkách vyberte **Vyloučit** a **Názvy sloupců**.
    
1. Do textového pole zadejte *normalizované ztráty.*

1. Vpravo dole vyberte **Uložit,** abyste zavřeli volič sloupců.

    ![Vyloučení sloupce](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Vyberte modul **Vybrat sloupce v datové sadě.** 

1. V podokně podrobností modulu napravo od plátna vyberte textové pole **Komentář** a zadejte *Vyloučit normalizované ztráty*.

    Komentáře se zobrazí v grafu, které vám pomohou uspořádat kanál.

### <a name="clean-missing-data"></a>Vyčistit chybějící data

Po odebrání sloupce **normalizované ztráty** stále chybí data. Zbývající chybějící data můžete odebrat pomocí modulu **Vyčistit chybějící data.**

> [!TIP]
> Čištění chybějící hodnoty ze vstupních dat je předpokladem pro použití většiny modulů v návrháři.

1. V paletě modulů nalevo od plátna rozbalte část **Transformace dat**a najděte modul Vyčistit **chybějící data.**

1. Přetáhněte modul **Vyčistit chybějící data** na plátno kanálu. Připojte ji k modulu **Vybrat sloupce v datové sadě.** 

1. Vyberte modul **Vyčistit chybějící data.**

1. V podokně podrobností modulu vpravo od plátna vyberte **Upravit sloupec**.

1. V okně Sloupce, které **chcete vyčistit,** které se zobrazí, rozbalte rozevírací nabídku vedle **položky Zahrnout**. Vybrat, **všechny sloupce**

1. Vyberte **Uložit**.

1. V podokně podrobností modulu vpravo od plátna vyberte **odebrat celý řádek** v části **Režim čištění**.

1. V podokně podrobností modulu vpravo od plátna vyberte pole **Komentář** a zadejte *Odebrat chybějící řádky hodnot*. 

    Váš kanál by teď měl vypadat nějak takto:
    
    ![Vybrat sloupec](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Trénování modelu strojového učení

Nyní, když máte moduly na místě pro zpracování dat, můžete nastavit trénovací moduly.

Protože chcete předpovědět cenu, což je číslo, můžete použít regresní algoritmus. V tomto příkladu použijete lineární regresní model.

### <a name="split-the-data"></a>Rozdělení dat

Rozdělení dat je běžný úkol ve strojovém učení. Data rozdělíte do dvou samostatných datových sad. Jedna datová sada bude trénovat model a druhá bude testovat, jak dobře model provádí.

1. V paletě modulů rozbalte sekci **Transformace dat** a najděte modul **Rozdělit data.**

1. Přetáhněte modul **Rozdělit data** na plátno kanálu.

1. Připojte levý port modulu **Vyčistit chybějící data** k modulu Rozdělit **data.**

    > [!IMPORTANT]
    > Ujistěte se, že levé výstupní porty **clean chybějící data** se připojí k split **data**. Levý port obsahuje vyčištěná data. Pravý port obsahuje diskovaná data.

1. Vyberte modul **Rozdělit data.**

1. V podokně podrobností modulu vpravo od plátna nastavte **zlomek řádků v první výstupní datové sadě** na 0,7.

    Tato možnost rozdělí 70 procent dat pro trénování modelu a 30 procent pro jeho testování. Datová sada 70 procent bude přístupná prostřednictvím levého výstupního portu. Zbývající data budou k dispozici prostřednictvím správného výstupního portu.

1. V podokně podrobností modulu vpravo od plátna vyberte pole **Komentář** a zadejte *Rozdělit datovou sadu do trénovací sady (0,7) a testovací sady (0,3).*

### <a name="train-the-model"></a>Trénování modelu

Trénování modelu tím, že mu datovou sadu, která zahrnuje cenu. Algoritmus vytvoří model, který vysvětluje vztah mezi funkcemi a cenou, jak jsou uvedena v trénovacích datech.

1. V paletě modulů rozbalte **položku Algoritmy strojového učení**.
    
    Tato možnost zobrazí několik kategorií modulů, které můžete použít k inicializaci algoritmů učení.

1. Vyberte **Regresní** > **lineární regrese**a přetáhněte ji na plátno potrubí.

1. Připojte výstup **modulu Lineární regrese** k levému vstupu modulu **Model vlaku.**

1. V paletě modulů rozbalte oddíl **Modul školení**a přetáhněte modul **Model vlaku** na plátno.

1. Vyberte modul **Model vlaku** a přetáhněte ho na plátno potrubí.

1. Připojte výstup trénovacích dat (levý port) modulu **Split Data** k pravému vstupu modulu **Model vlaku.**
    
    > [!IMPORTANT]
    > Ujistěte se, že levé výstupní porty **Split Data** se připojí k **train model**. Levý port obsahuje trénovací sadu. Pravý port obsahuje testovací sadu.

    ![Snímek obrazovky se správnou konfigurací modulu Model vlaku. Modul Lineární regrese se připojuje k levému portu modulu Model vlaku a modul Split Data se připojuje k pravému portu modelu vlaku](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. Vyberte modul **Model vlaku.**

1. V podokně podrobností modulu vpravo od plátna vyberte Upravit volič **sloupců.**

1. V dialogovém okně **Popis ovat sloupec** rozbalte rozevírací nabídku a vyberte **Názvy sloupců**. 

1. Do textového pole zadejte *cenu* a určete hodnotu, kterou bude váš model předpovídat.

    >[!IMPORTANT]
    > Ujistěte se, že jste přesně zadali název sloupce. Nepoužívejte vydělávat **cenu**. 

    Váš kanál by měl vypadat takto:

    ![Snímek obrazovky zobrazující správnou konfiguraci kanálu po přidání modulu Model vlaku.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

### <a name="add-the-score-model-module"></a>Přidání modulu Model skóre

Po trénování modelu pomocí 70 procent dat, můžete jej použít k skóre dalších 30 procent zobrazíte, jak dobře váš model funguje.

1. Zadejte *model skóre* do vyhledávacího pole a vyhledejte modul **Model skóre.** Přetáhněte modul na plátno potrubí. 

1. Propojte výstup modulu **Trénování modelu** s levým vstupním portem modulu **Určení skóre modelu**. Propojte výstup testovacích dat (pravý port) modulu **Rozdělení dat** s pravým vstupním portem modulu **Určení skóre modelu**.

### <a name="add-the-evaluate-model-module"></a>Přidání modulu Vyhodnotit model

Pomocí modulu **Vyhodnotit model** můžete vyhodnotit, jak dobře váš model zaznamenal testovací datovou sadu.

1. Zadejte *hodnotit* do vyhledávacího pole a vyhledejte modul **Vyhodnotit model.** Přetáhněte modul na plátno potrubí. 

1. Připojte výstup modulu **Model skóre** k levému vstupu **vyhodnocení modelu**. 

    Konečný potrubí by mělo vypadat nějak takto:

    ![Snímek obrazovky se správnou konfigurací kanálu](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

## <a name="submit-the-pipeline"></a>Odeslat potrubí

Teď, když je váš kanál nastavený, můžete odeslat spuštění kanálu pro trénování modelu strojového učení. Můžete odeslat platný kanál spustit kdykoliv, který lze použít ke kontrole změny kanálu během vývoje.

1. V horní části plátna vyberte **Odeslat**.

1. V dialogovém okně **Nastavit spuštění kanálu** vyberte Vytvořit **nový**.

    > [!NOTE]
    > Experimenty seskupí podobné potrubí běží společně. Pokud spustíte kanál vícekrát, můžete vybrat stejný experiment pro následné spuštění.

    1. Zadejte popisný název pro **Nový název experimentu**.

    1. Vyberte **Odeslat**.
    
    Stav spuštění a podrobnosti můžete zobrazit v pravém horním rohu plátna.
    
    Pokud je první spuštění, může trvat až 20 minut pro spuštění kanálu. Výchozí nastavení výpočetních prostředků mají minimální velikost uzlu 0, což znamená, že návrhář musí přidělit prostředky po nečinnosti. Opakované spuštění kanálu bude trvat kratší dobu, protože výpočetní prostředky jsou již přiděleny. Kromě toho návrhář používá výsledky uložené v mezipaměti pro každý modul k dalšímu zlepšení efektivity.

### <a name="view-scored-labels"></a>Zobrazit popisky s vyhotovenými body

Po dokončení spuštění můžete zobrazit výsledky spuštění kanálu. Nejprve se podívejte na předpovědi generované regresní model.

1. Chcete-li zobrazit jeho výstup, vyberte modul **Model skóre.**

1. V podokně podrobností modulu vpravo od plátna vyberte ![ **Výstupy + protokoly** > ikonou grafu vizualizovat,](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) abyste zobrazili výsledky.

    Zde si můžete prohlédnout předpokládané ceny a skutečné ceny z testovacích dat.

    ![Snímek obrazovky s vizualizací výstupu zvýrazňující sloupec Popisek s hodnocenou skóre](./media/tutorial-designer-automobile-price-train-score/score-result.png)

### <a name="evaluate-models"></a>Vyhodnocení modelů

Pomocí **vyhodnotit model** zobrazíte, jak dobře trénovaný model provádí na testovací datové sady.

1. Chcete-li zobrazit jeho výstup, vyberte modul **Vyhodnotit model.**

1. V podokně podrobností modulu vpravo od plátna vyberte ![ **Výstupy + protokoly** > ikonou grafu vizualizovat,](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) abyste zobrazili výsledky.

Pro váš model jsou zobrazeny následující statistiky:

* **Průměrná absolutní chyba (MAE)**: Průměr absolutních chyb. Chyba je rozdíl mezi předpokládanou hodnotou a skutečnou hodnotou.
* **Kořenová kvadratická chyba (RMSE):** Druhá odmocnina průměru kvadratických chyb předpovědí provedených v testovací datové sadě.
* **Relativní absolutní chyba**: Průměr absolutních chyb relativních k absolutnímu rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot
* **Relativní kvadratická chyba**: Průměr kvadratických chyb relativních ke kvadratickému rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot
* **Koeficient stanovení**: Tato statistická metrika, známá také jako kvadratová hodnota R, označuje, jak dobře model odpovídá datům.

Pro každou statistiku chyb platí, že menší hodnota je lepší. Menší hodnota označuje, že předpovědi jsou blíže ke skutečné hodnoty. Pro koeficient stanovení, čím blíže je jeho hodnota na jeden (1,0), tím lepší předpovědi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Přeskočit tuto část, pokud chcete pokračovat s částí 2 kurzu, [nasazení modelů](tutorial-designer-automobile-price-deploy.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Ve druhé části se dozvíte, jak nasadit model jako koncový bod v reálném čase.

> [!div class="nextstepaction"]
> [Pokračovat v nasazování modelů](tutorial-designer-automobile-price-deploy.md)
