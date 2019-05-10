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
ms.openlocfilehash: 352f695f186b656ad0f6471d8ede905d53ad5274
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65234088"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Kurz: Předvídání cen automobilů vizuální rozhraní

V tomto kurzu provedete rozšířené podívat vývoj prediktivního řešení ve vizuální rozhraní služby Azure Machine Learning. Na konci tohoto kurzu budete mít, který může předpovídat cenu za jakékoli automobilu na základě technických specifikací, které odesíláte řešení.

V tomto kurzu [pokračuje od tohoto rychlého startu](ui-quickstart-run-experiment.md) a je **první části série dvojdílného**. Nicméně není nutné dokončit před zahájením tohoto rychlého startu.

V první části této série kurzů se dozvíte, jak:

> [!div class="checklist"]
> * Import a vyčištění dat (stejný postup jako rychlý start)
> * Trénování modelu strojového učení
> * Stanovení skóre a vyhodnocení modelu

V [druhá část](ui-tutorial-automobile-price-deploy.md) této série kurzů, dozvíte víc o prediktivní model nasadit jako webová služba Azure.

> [!NOTE]
> Úplnou verzi tohoto kurzu je k dispozici jako ukázkový experiment.
> Na stránce experimenty, přejděte na **přidat nový** > **ukázka 1 - regrese: Prediction(Basic) automobilů cena**

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Build-zero-code-machine-learning-models-with-Azure-Machine-Learning-service/player]


## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pokud máte pracovní prostor služby Azure Machine Learning service, pokračujte [další části](#open-the-visual-interface-webpage). V opačném případě vytvořte si ho teď.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>Otevřít webovou stránku vizuální rozhraní

1. Otevření pracovního prostoru v [webu Azure portal](https://portal.azure.com/).  

1. V pracovním prostoru vyberte **vizuální rozhraní**.  Potom vyberte **vizuální rozhraní spuštění**.  

    ![Snímek obrazovky webu Azure portal ukazující, jak přistupovat k vizuální rozhraní z pracovního prostoru služby Machine Learning](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

    Rozhraní webová stránka se otevře v nové stránky prohlížeče.  

## <a name="import-and-clean-your-data"></a>Import a vyčistit data

První věc, kterou je třeba je vyčistit data. Po dokončení tohoto rychlého startu můžete znovu použít přípravu experimentu data zde. Pokud jste nedokončili rychlý start, přeskočte další části a [začněte nový experiment](#start-from-a-new-experiment).

### <a name="reuse-the-quickstart-experiment"></a>Znovu použít rychlý start experimentu

1. Otevřete experiment rychlý start.

1. Vyberte **uložit jako** v dolní části okna.

1. Přiřaďte jí nový název v místním dialogovém okně, které se zobrazí.

    ![Snímek obrazovky ukazující, jak přejmenování experimentu "Kurzu – předpovědět Automobile Price"](./media/ui-tutorial-automobile-price-train-score/save-a-copy.png)

1. Experiment by teď měl vypadat přibližně takto:

    ![Snímek obrazovky zobrazující očekávaný stav testu. Automobilů datová sada připojí k modulu výběr sloupců, která se připojuje k vyčištění chybějících dat](./media/ui-tutorial-automobile-price-train-score/save-copy-result.png)

Pokud úspěšně znovu použít experimentu rychlý start, přeskočte další části, kde začít [trénování modelu](#train-the-model).

### <a name="start-from-a-new-experiment"></a>Začněte nový experiment

Pokud nebyl dokončení tohoto rychlého startu, následujícím postupem můžete rychle vytvořit nový experiment, který importuje a vyčistí automobilů datové sady.

1. Vytvoření nového experimentu tak, že vyberete **+ nová** v dolní části okna vizuální rozhraní.

1. Vyberte **EXPERIMENT** >  **prázdný Experiment**.

1. Vyberte výchozí název experimentu **"experimentovali vytvořené na...** "v horní části plátna a přejmenujte jej na něco smysluplného. Například **predikce ceny**. Název nemusí být jedinečný.

1. Nalevo od plátna experimentu je paleta datových sad a modulů. K hledání modulů, použijte vyhledávací pole v horní části palety modulů. Typ **automobile** do vyhledávacího pole najít datovou sadu s názvem **Automobile price data (Raw)**. Přetáhněte tuto datovou sadu na plátno experimentu.

    ![Snímek obrazovky jak zjistit datové sady automobilů cena](./media/ui-tutorial-automobile-price-train-score/automobile-dataset.png)

    Teď, když máte data, můžete přidat modul, který odebere **normalized-losses** sloupec úplně. Pak přidejte další modul, který odebere všechny řádky, ve kterých chybějí data.

1. Typ **vyberte sloupce, které** do vyhledávacího pole Najít **výběr sloupců v datové sadě** modulu. Potom přetáhněte na plátno experimentu. Tento modul umožňuje vybrat sloupce dat, které chcete zahrnout nebo vyloučit v modelu.

1. Připojte výstupní port modulu **Automobile price data (Raw)** datové sady ke vstupnímu portu výběr sloupců v datové sadě.

    ![Animovaný gif znázorňující způsob připojení modulu Automobile Price Data do modulu výběr sloupců](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Vyberte Výběr sloupců v datové sadě modul a vyberte **spustit selektor sloupců** v **vlastnosti** podokně.

   1. Na levé straně vyberte **s pravidly**

   1. Vedle položky **začít s**vyberte **všechny sloupce**. Tato pravidla s přímým přístupem **výběr sloupců v datové sadě** aby prošel všechny sloupce (kromě sloupců, které vyloučíme).

   1. Z rozevírací nabídky vyberte **vyloučit** a **názvy sloupců**a pak zadejte **normalized-losses** do textového pole.

   1. Vyberte tlačítko OK zavřete selektor sloupců (vpravo dole).

     Podokno vlastností modulu **Výběr sloupců v datové sadě** teď indikuje, že modul bude procházet všechny sloupce datové sady kromě **normalized-losses**.

1. Přidat komentář **výběr sloupců v datové sadě** modulu dvakrát na modul a zadáním "Vyloučit normalized ztráty.". To vám může pomoci zjistit, na první pohled, jaký je účel modulu v experimentu.

    ![Snímek obrazovky znázorňující správnou konfiguraci modulu výběr sloupců](./media/ui-tutorial-automobile-price-train-score/select-columns.png)

1. Typ **Vyčistit** do vyhledávacího pole Najít **vyčištění chybějících dat** modulu. Přetáhněte **vyčištění chybějících dat** plátno modul do experimentu a propojte jej s **výběr sloupců v datové sadě** modulu.

1. V podokně **Vlastnosti** vyberte v části **Režim čištění** možnost **Odstranit celý řádek**. Tyto možnosti s přímým přístupem **vyčištění chybějících dat** k vyčištění dat odstraněním řádků, které mají chybí některé hodnoty. Klikněte dvakrát na modul a zadejte komentář Odstranění řádků s chybějícími hodnotami.

![Snímek obrazovky znázorňující správnou konfiguraci modulu vyčištění chybějících dat](./media/ui-tutorial-automobile-price-train-score/clean-missing-data.png)

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

    Experiment by měl vypadat.
    ![Snímek obrazovky zobrazující správnou konfiguraci testu po přidání modulu Train Model.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>Spuštění výukového experimentu

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>Stanovení skóre a vyhodnocení modelu

Teď, když jsme natrénovali model pomocí 70 procent dat, které můžete použít ke stanovení skóre pro jiné 30 procent dat a zjistit, jak dobře vaše funkce modelu.

1. Typ **určení skóre modelu** do vyhledávacího pole Najít **Score Model** modulu a modulu přetáhněte na plátno experimentu. Propojte výstup modulu **Train Model** modul na levé straně vstupním portem modulu **Score Model**. Připojte se výstup testovacích dat (pravý port) z **rozdělení dat** modulu na pravé straně vstupním portem modulu **Score Model**.

1. Typ **vyhodnotit** do vyhledávacího pole Najít **Evaluate Model** a přetáhněte ji it modulů na plátno experimentu. Propojte výstup modulu **Score Model** levým vstupem modulu **Evaluate Model**. Konečný experiment by měl vypadat přibližně takto:

    ![Snímek obrazovky zobrazující konečné správnou konfiguraci testu.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Spusťte experiment. použití stejného cíle výpočetní použili.

1. Zobrazte výstup **Score Model** tak, že vyberete na výstupní port modulu **Score Model** a vyberte **vizualizovat**. Na výstupu se zobrazí predikované hodnoty ceny a známé hodnoty v testovacích datech.

    ![Snímek obrazovky vizualizace výstupu zvýraznění "Skóre popisek" sloupce](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Pokud chcete zobrazit výstup z modulu Evaluate Model, vyberte na výstupní port a vyberte vizualizovat.

    ![Snímek obrazovky zobrazující výsledky vyhodnocení pro konečný experiment.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

Pro váš model se zobrazí následující statistiky:

* **Střední absolutní chyba (MAE)**: Průměr absolutních chyb (chybu je rozdíl mezi předpovězenou a skutečnou hodnotu).
* **Střední kořenové spolehlivosti chyby (RMSE)**: Druhá odmocnina průměru kvadratických chyb předpovědí na základě testovací datové.
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

* Znovu použít experimentu vytvořili v tomto rychlém startu
* Příprava dat
* Trénování modelu
* Stanovení skóre a vyhodnocení modelu

V druhé části se dozvíte, jak model nasadit jako webová služba Azure.

> [!div class="nextstepaction"]
> [Pokračovat v nasazení modelů](ui-tutorial-automobile-price-deploy.md)
