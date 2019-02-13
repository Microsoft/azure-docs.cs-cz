---
title: 'Rychlý start: Vytvoření experimentu datové vědy'
titleSuffix: Azure Machine Learning Studio
description: V tomto rychlém startu machine learningu vás provede experimentu jednoduché datové vědy. Pomocí regresního algoritmu předpovíme cenu automobilu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.custom: seodec18
ms.date: 02/06/2019
ms.openlocfilehash: 5daedcc931732e916a47ab857f489ebff0184101
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204453"
---
# <a name="quickstart-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Rychlý start: Vytvoření prvního experimentu datové vědy v nástroji Azure Machine Learning Studio

V tomto rychlém startu vytvoříte experiment strojového učení v [Azure Machine Learning Studio](what-is-ml-studio.md) , který bude předpovídat cenu automobilu podle různých proměnných, třeba značky a technických specifikací.

Pokud jste úplně novým uživatelem ve službě machine learning, série videí [datová věda pro začátečníky](data-science-for-beginners-the-5-questions-data-science-answers.md) představuje vynikající Úvod do strojového učení s využitím každodenního jazyka a konceptů.

V tomto rychlém startu následující výchozí pracovní postup pro experiment:

1. **Vytvoření modelu**
    - [Získání dat]
    - [Příprava dat]
    - [Definice funkcí]
1. **Trénování modelu**
    - [Volba a použití algoritmu]
1. **Stanovení skóre a otestování modelu**
    - [Předpověď cen nových automobilů]

[Získání dat]: #get-the-data
[Příprava dat]: #prepare-the-data
[Definice funkcí]: #define-features
[Volba a použití algoritmu]: #choose-and-apply-an-algorithm
[Předpověď cen nových automobilů]: #predict-new-automobile-prices

Pokud nemáte účet Studio, přejděte na [domovské stránky Studio](https://studio.azureml.net) a vyberte **zaregistrovat** vytvořte si bezplatný účet. Bezplatný pracovní prostor bude mít všechny funkce, které potřebujete pro tento rychlý start.

## <a name="get-the-data"></a>Získání dat

První věc, kterou je nutné ve službě machine learning se data.
Existuje několik ukázkových datových sad se sadou Studio, které můžete použít, nebo můžete importovat data z mnoha zdrojů. V tomto příkladu použijeme ukázkovou datovou sadu **Automobile price data (Raw)**, která je součástí vašeho pracovního prostoru.
Tato datová sada obsahuje záznamy řady různých automobilů, včetně informací o značce, modelu, technických specifikacích a ceně.

> [!TIP]
> Pracovní kopii následujícího experimentu najdete v [galerii Azure AI](https://gallery.cortanaintelligence.com). Přejděte k části **[První experiment z oblasti datové vědy – predikce ceny automobilu](https://gallery.cortanaintelligence.com/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)**, klikněte na **Open in Studio** (Otevřít v sadě Studio) a stáhněte kopii experimentu do pracovního prostoru Machine Learning Studio.

Tuto datovou sadu dostanete do svého experimentu takto.

1. Vytvořte nový experiment kliknutím **+ nová** v dolní části okna Machine Learning Studio. Vyberte **EXPERIMENT** >  **prázdný Experiment**.

1. Experimentu se přiřadí výchozí název, který se zobrazí v horní části plátna. Vyberte tento text a přejmenujte jej na něco smysluplného, například **Predikce ceny automobilu**. Název nemusí být jedinečný.

    ![Přejmenování experimentu][rename-experiment]

1. Nalevo od plátna experimentu je paleta datových sad a modulů. Do pole Hledat v horní části palety zadejte **automobile**. Vyhledá se datová sada **Automobile price data (Raw)**. Přetáhněte tuto datovou sadu na plátno experimentu.

    ![Vyhledejte datovou sadu automobilů a přetáhněte ji na plátno experimentu.][type-automobile]

Chcete-li zobrazit, co tato data vypadají, klikněte na výstupní port v dolní části datovou sadu automobilů a vyberte položku **vizualizovat**.

![Klikněte na výstupní port a vyberte vizualizovat.][select-visualize]

> [!TIP]
> Vstupní a výstupní porty datových sad a modulů jsou reprezentované malými kroužky – vstupní porty v horní části, výstupní porty v dolní části.
Pokud chcete vytvořit tok dat prostřednictvím experimentu, připojte výstupní port jednoho modulu ke vstupnímu portu jiného.
V libovolném okamžiku můžete kliknout na výstupní port datové sady nebo modulu a prohlédnout si, jak v tomto bodě vypadá tok dat.

V této datové sadě každý řádek představuje automobilu a proměnné přidružené k automobilům se zobrazují jako sloupce. Předpovíme cenu ve sloupci úplně vpravo (sloupec 26 s názvem "price") pomocí proměnných pro konkrétní automobil.

![Zobrazte data automobilů v okně vizualizace dat.][visualize-auto-data]

Kliknutím na **x** v pravém horním rohu zavřete okno vizualizace.

## <a name="prepare-the-data"></a>Příprava dat

Před analýzou datové sady bývá zpravidla nutné sadu nějakým způsobem předzpracovat. Možná jste si ve sloupcích různých řádků všimli chybějících hodnot. Tyto chybějící hodnoty se musí vyčistit, aby model mohl data správně analyzovat. Odstraníme všechny řádky, které chybí některé hodnoty. Hodnoty ve sloupci **normalized-losses** navíc z velké části chybí, proto tento sloupec v modelu zcela vynecháme.

> [!TIP]
> Vyčištění chybějících hodnot ze vstupních dat je pro většinu modulů nutností.

Nejdříve přidáme modul, který odebere **normalized-losses** sloupec úplně. Potom přidáme další modul, který odebere všechny řádky, ve kterých chybějí data.

1. Typ **vyberte sloupce, které** do vyhledávacího pole v horní části palety modulů najít [výběr sloupců v datové sadě] [ select-columns] modulu. Potom přetáhněte na plátno experimentu. Tento modul umožňuje vybrat, které sloupce dat chceme zahrnout do modelu, nebo je z modelu naopak vyloučit.

1. Připojte výstupní port datové sady **Automobile price data (Raw)** ke vstupnímu portu modulu [Výběr sloupců v datové sadě][select-columns].

    ![Přidejte modul "Výběr sloupců v datové sadě" na plátno experimentu a propojte jej][type-select-columns]

1. Klikněte na modul [Výběr sloupců v datové sadě][select-columns] a v podokně **Vlastnosti** klikněte na **Spustit selektor sloupců**.

    - Vlevo klikněte na **S pravidly**.
    - V části **Začít s** klikněte na **Všechny sloupce**. Tato pravidla s přímým přístupem [výběr sloupců v datové sadě] [ select-columns] aby prošel všechny sloupce (kromě sloupců, které vyloučíme).
    - V rozevíracích seznamech vyberte **Vyloučit** a **názvy sloupců** a klikněte do textového pole. Zobrazí se seznam sloupců. Vyberte sloupec **normalized-losses**, který se tak přidá do textového pole.
    - Kliknutím na tlačítko zaškrtnutí (OK) zavřete selektor sloupců (vpravo dole).

    ![Spusťte selektor sloupců a vylučte sloupec normalized-losses."][launch-column-selector]

    Podokno vlastností modulu **Výběr sloupců v datové sadě** teď indikuje, že modul bude procházet všechny sloupce datové sady kromě **normalized-losses**.

    ![V podokně vlastností ukazuje, že sloupec normalized-losses."je vyloučený][showing-excluded-column]

    > [!TIP] 
    > Kliknutím dvakrát na modul a zadáním textu je možné přidat k modulu komentář. To vám může pomoci rychle poznat, jaký je účel modulu v experimentu. V tomto případě klikněte dvakrát na modul [Výběr sloupců v datové sadě][select-columns] a zadejte komentář Vyloučit normalized-losses.

    ![Klikněte dvakrát na modul a přidejte komentář.][add-comment]

1. Přetáhněte modul [Vyčištění chybějících dat][clean-missing-data] na plátno experimentu a propojte jej s modulem [Výběr sloupců v datové sadě][select-columns]. V podokně **Vlastnosti** vyberte v části **Režim čištění** možnost **Odstranit celý řádek**. Tyto možnosti s přímým přístupem [vyčištění chybějících dat] [ clean-missing-data] k vyčištění dat odstraněním řádků, které mají chybí některé hodnoty. Klikněte dvakrát na modul a zadejte komentář Odstranění řádků s chybějícími hodnotami.

    ![Nastavení režimu čisticí "Odebrat celý řádek" pro modul "Vyčištění chybějících dat"][set-remove-entire-row]

1. Spusťte experiment kliknutím na **SPUSTIT** v dolní části stránky.

    Až se spuštění experimentu dokončí, u všech modulů se zobrazí zelená značka zaškrtnutí, která označuje, že jejich činnost úspěšně skončila. Všimněte si také stavu **Konec běhu** v pravém horním rohu.

    ![Po spuštění by experiment měl vypadat asi takhle nějak.][early-experiment-run]

> [!TIP]
> Proč jsme experiment teď spustili? Spuštěním experimentu jsme zajistili, aby definice sloupců pro naše data prošly z původní datové sady přes modul [Výběr sloupců v datové sadě][select-columns] a přes modul [Vyčištění chybějících dat][clean-missing-data]. To znamená, že všechny moduly, které připojíme k modulu [Vyčištění chybějících dat][clean-missing-data], budou také mít tytéž informace.

Teď máme vyčistit data. Pokud si chcete zobrazit vyčištěnou datovou sadu, klikněte na levý výstupní port modulu [Vyčištění chybějících dat][clean-missing-data] a vyberte **Vizualizovat**. Všimněte si, že již není zobrazen sloupec **normalized-losses** a že nechybí žádné hodnoty.

Nyní když jsou data vyčištěna, můžete určit, které příznaky použijeme v prediktivním modelu.

## <a name="define-features"></a>Definice funkcí

Ve strojovém učení se jako *příznaky* označují jednotlivé měřitelné vlastnosti něčeho, co vás zajímá. V naší datové sadě každý řádek představuje jeden automobil a každý sloupec je příznak daného automobilu.

Nalezení správné sady příznaků pro vytvoření prediktivního modelu vyžaduje experimentování a znalost problému, který chcete vyřešit. Některé příznaky jsou pro predikci cíle vhodnější než jiné. Některé funkce mají silnou korelaci s jinými funkcemi a je možné odebrat. Například příznaky city-mpg a highway-mpg jsou vzájemně těsně propojené, takže stačí jeden z nich odebrat a ponechat jenom ten druhý, aniž by to vytvářenou predikci výrazně ovlivnilo.

Vytvořme model, který používá podmnožinu příznaků naší datové sady. Později můžete vybrat jiné příznaky, spustit experiment znovu a zjistit, jestli nedostanete lepší výsledky. Nejdřív ale vyzkoušíme tyto funkce:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Na plátno experimentu přetáhněte další modul [Výběr sloupců v datové sadě][select-columns]. Propojte levý výstupní port modulu [Vyčištění chybějících dat][clean-missing-data] se vstupem modulu [Výběr sloupců v datové sadě][select-columns].

    ![Připojte modul "Výběr sloupců v datové sadě" k "Vyčištění chybějících dat"][connect-clean-to-select]

1. Poklikejte na modul a zadejte Výběr příznaků pro predikci.

1. V podokně **Vlastnosti** klikněte na **Spustit selektor sloupců**.

1. Klikněte na **S pravidly**.

1. V části **Začít s** klikněte na **Žádné sloupce**. V řádku filtru vyberte **Zahrnout** a **názvy sloupců** a vyberte náš seznam názvů sloupců v textovém poli. Tento filtr modul dostává instrukci, aby neprocházel žádné sloupce (příznaky) s výjimkou těch, které určíme.

1. Klikněte na tlačítko zaškrtnutí (OK).

    ![Vyberte sloupce (příznaky), které chcete zahrnout do predikce.][select-columns-to-include]

Tento modul vytváří je filtrovaná datová sada obsahující jenom ty příznaky, které chceme předat algoritmu učení, který použijeme v dalším kroku. Později se můžete vrátit a zkusit jiný výběr příznaků.

## <a name="choose-and-apply-an-algorithm"></a>Volba a použití algoritmu

Nyní když jsou data připravena, tvorba prediktivního modelu sestává z trénování a testování. Naše data použijeme pro trénování modelu. Potom model otestujeme a zjistíme, jak přesně dokáže předpovídat ceny.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*Klasifikace* a *regrese* jsou dva typy technik strojového učení se supervizí. Klasifikace předpovídá odpověď na základě definované sady kategorií, třeba barvy (červená, modrá nebo zelená). Regrese se používá k předpovědi čísel.

Chceme předpovědět cenu, což je číslo, a tak použijeme regresní algoritmus. V tomto příkladu použijeme *lineární regrese* modelu.


Model trénujeme tím, že mu poskytneme sadu dat, která zahrnují cenu. Model projde data a hledá korelaci mezi příznaky automobilu a jeho cenou. Potom model otestujeme. Poskytneme mu sadu příznaků pro automobily, které známe, a uvidíme, do jaké míry se predikce modelu blíží známé ceně.

Naše data můžeme použít jak pro trénování modelu, tak pro jeho otestování. Dají se totiž rozdělit na samostatné sady pro trénování a testování.

1. Vyberte a přetáhněte na plátno experimentu modul [Rozdělení dat][split] a propojte jej s posledním modulem [Výběr sloupců v datové sadě][select-columns].

1. Klikněte na modul [Rozdělení dat][split]. Modul se vybere. Vyhledejte **Podíl řádků v první výstupní sadě dat** (v podokně **Vlastnosti** napravo od plátna) a nastavte ho na 0,75. Takto použijeme 75 procent dat pro trénování modelu a 25 procent si ponecháme na testování.

    ![Nastavte podíl modulu "Rozdělení dat" na 0,75.][set-split-data-percentage]

    > [!TIP]
    > Změnou parametru **Náhodná počáteční hodnota** je možné pro trénování a testování vytvořit různé náhodné vzorky. Tento parametr řídí nastavování počáteční hodnoty pseudonáhodného generátoru čísel.

1. Spusťte experiment. Při spuštění experimentu moduly [Výběr sloupců v datové sadě][select-columns] a [Rozdělení dat][split] předají definice sloupců do modulů, které přidáme jako další.  

1. Nyní vyberte algoritmus učení. Na paletě modulů nalevo od plátna rozbalte kategorii **Strojové učení** a pak **Inicializovat model**. Tímto se zobrazí několik kategorií modulů, které je možné použít k inicializaci algoritmů strojového učení. Pro tento experiment vyberte modul [Lineární regrese][linear-regression] v kategorii **Regrese** a přetáhněte ho na plátno experimentu. (Tento modul můžete najít i tak, že do pole Hledat palety zadáte lineární regrese.)

1. Najděte modul [Trénování modelu][train-model] a přetáhněte ho na plátno experimentu. Propojte výstup modulu [Lineární regrese][linear-regression] s levým vstupem modulu [Trénování modelu][train-model] a potom propojte výstup trénovacích dat (levý port) modulu [Rozdělení dat][split] s pravým vstupem modulu [Trénování modelu][train-model].

    ![Propojte modul "Train Model" k modulům "Lineární regrese" a "Rozdělení dat."][connect-train-model]

1. Klikněte na modul [Trénování modelu][train-model], v podokně **Vlastnosti** klikněte na **Spustit selektor sloupců** a vyberte sloupec **price**. **Cena** je hodnota, kterou náš model bude předpovídat.

    V selektoru sloupců vyberete sloupec **price** – přesunete ho ze seznamu **Dostupné sloupce** do seznamu **Vybrané sloupce**.

    ![Vyberte sloupec price. pro modul "Train Model"][select-price-column]

1. Spusťte experiment.

Výsledkem je natrénovaný model, který je možné použít ke stanovení skóre pro nová data automobilů a k následné predikci cen.

![Po spuštění experimentu by teď měl vypadat asi takhle nějak.][second-experiment-run]

## <a name="predict-new-automobile-prices"></a>Předpověď cen nových automobilů

Nyní když jsme natrénovali model pomocí 75 procent dat, můžeme model použít ke stanovení skóre u zbylých 25 procent dat a zjistit, jak dobře model funguje.

1. Najděte modul [Určení skóre modelu][score-model] a přetáhněte ho na plátno experimentu. Propojte výstup modulu [Trénování modelu][train-model] s levým vstupním portem modulu [Určení skóre modelu][score-model]. Propojte výstup testovacích dat (pravý port) modulu [Rozdělení dat][split] s pravým vstupním portem modulu [Určení skóre modelu][score-model].

    ![Propojte modul "Určení skóre modelu" k modulům "Train Model" a "Rozdělení dat."][connect-score-model]

1. Spusťte experiment a zobrazte výstup [Score Model] [ score-model] kliknutím na výstupní port modulu [určení skóre modelu] [ score-model] a vyberte  **Vizualizujte**. Na výstupu se zobrazí predikované hodnoty ceny a známé hodnoty v testovacích datech.  

    ![Výstup modulu "Určení skóre modelu"][score-model-output]

1. Nakonec otestujeme kvalitu výsledků. Najděte modul [Vyhodnocení modelu][evaluate-model], přetáhněte ho na plátno experimentu a propojte výstup modulu [Určení skóre modelu][score-model] s levým vstupem modulu [Vyhodnocení modelu][evaluate-model]. Konečný experiment by měl vypadat přibližně takto:

    ![Konečný experiment][complete-linear-regression-experiment]

1. Spusťte experiment.

Zobrazte výstup modulu [Vyhodnocení modelu][evaluate-model] tak, že kliknete na výstupní port a vyberete **Vizualizovat**.

![Výsledky vyhodnocení pro experiment][evaluation-results]

Pro náš model se zobrazí následující statistiky:

- **Střední absolutní chyba** (MAE): Průměr absolutních chyb ( *chyba* je rozdíl mezi předpovězenou a skutečnou hodnotu).
- **Střední kořenové spolehlivosti chyba** (RMSE): Druhá odmocnina průměru kvadratických chyb předpovědí na základě testovací datové.
- **Relativní absolutní chyba**: Průměr absolutních chyb relativních k absolutnímu rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot.
- **Relativní spolehlivosti chyba**: Průměr kvadratických chyb relativních ke kvadratickému rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot.
- **Koeficient spolehlivosti**: Také **hodnota spolehlivosti R**, to tedy statistická metrika označující, jak dobře model odpovídá zpracovávaným datům.

Pro každou statistiku chyb platí, že menší hodnota je lepší. Menší hodnota označuje, že předpověď přesněji odpovídá skutečným hodnotám. V případě **koeficientu spolehlivosti** platí, že čím bližší je jeho hodnota hodnotě jedna (1,0), tím lepší jsou předpovědi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili jednoduchý experiment pomocí ukázkové datové sadě. Prozkoumat proces vytvoření a nasazení modelu do větší hloubky, pokračujte ke kurzu prediktivní řešení.

> [!div class="nextstepaction"]
> [Kurz: Vývoj prediktivního řešení v sadě Studio](tutorial-part1-credit-risk.md)

<!-- Images -->
[sign-in-to-studio]: ./media/create-experiment/sign-in-to-studio.png
[rename-experiment]: ./media/create-experiment/rename-experiment.png
[visualize-auto-data]:./media/create-experiment/visualize-auto-data.png
[select-visualize]: ./media/create-experiment/select-visualize.png
[showing-excluded-column]:./media/create-experiment/showing-excluded-column.png
[set-remove-entire-row]:./media/create-experiment/set-remove-entire-row.png
[early-experiment-run]:./media/create-experiment/early-experiment-run.png
[select-columns-to-include]:./media/create-experiment/select-columns-to-include.png
[second-experiment-run]:./media/create-experiment/second-experiment-run.png
[connect-score-model]:./media/create-experiment/connect-score-model.png
[evaluation-results]:./media/create-experiment/evaluation-results.png
[complete-linear-regression-experiment]:./media/create-experiment/complete-linear-regression-experiment.png

<!-- temporarily switching GIFs to PNGs to remove animation -->
[type-automobile]:./media/create-experiment/type-automobile.png
[type-select-columns]:./media/create-experiment/type-select-columns.png
[launch-column-selector]:./media/create-experiment/launch-column-selector.png
[add-comment]:./media/create-experiment/add-comment.png
[connect-clean-to-select]:./media/create-experiment/connect-clean-to-select.png

[set-split-data-percentage]:./media/create-experiment/set-split-data-percentage.png

<!-- temporarily switching GIFs to PNGs to remove animation -->
[connect-train-model]:./media/create-experiment/connect-train-model.png
[select-price-column]:./media/create-experiment/select-price-column.png

[score-model-output]:./media/create-experiment/score-model-output.png

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
