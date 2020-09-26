---
title: 'ML Studio (Classic): rychlý Start: vytvoření experimentu pro datové vědy – Azure'
description: Tento rychlý Start Machine Learning vás provede jednoduchým experimentem pro datové vědy. Pomocí regresního algoritmu předpovíme cenu automobilu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: quickstart
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/06/2019
ms.openlocfilehash: d23702a71bababec2e172181c8e75c26241d0460
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91348913"
---
# <a name="quickstart-create-your-first-data-science-experiment-in-machine-learning-studio-classic"></a>Rychlý Start: vytvoření prvního experimentu pro datové vědy v Machine Learning Studio (Classic)

**platí pro:** ![ Ano ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ bez](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

V tomto rychlém startu vytvoříte experiment strojového učení v [Azure Machine Learning Studio (Classic)](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio) , který předpovídá cenu auta na základě různých proměnných, jako jsou třeba technické specifikace.

Pokud se naučíte novinkou ve strojovém učení, je [pro začátečníky užitečná řada datových věd](data-science-for-beginners-the-5-questions-data-science-answers.md) , která využívá každodenní jazyk a koncepty.

Tento rychlý Start následuje za výchozím pracovním postupem pro experiment:

1. **Vytvoření modelu**
    - [Získání dat]
    - [Příprava dat]
    - [Definovat funkce]
1. **Trénování modelu**
    - [Volba a použití algoritmu]
1. **Stanovení skóre a otestování modelu**
    - [Předpověď cen nových automobilů]

[Získání dat]: #get-the-data
[Příprava dat]: #prepare-the-data
[Definovat funkce]: #define-features
[Volba a použití algoritmu]: #choose-and-apply-an-algorithm
[Předpověď cen nových automobilů]: #predict-new-automobile-prices

## <a name="get-the-data"></a>Získání dat

První věc, kterou potřebujete ve službě Machine Learning, je data.
Sada Studio (Classic) obsahuje několik ukázkových datových sad, které můžete použít, nebo můžete importovat data z mnoha zdrojů. V tomto příkladu použijeme ukázkovou datovou sadu **Automobile price data (Raw)**, která je součástí vašeho pracovního prostoru.
Tato datová sada obsahuje záznamy řady různých automobilů, včetně informací o značce, modelu, technických specifikacích a ceně.

> [!TIP]
> Pracovní kopii následujícího experimentu najdete v [galerii Azure AI](https://gallery.azure.ai). Přejděte na **[první experiment z oblasti datové vědy, předpověď ceny automobilu](https://gallery.azure.ai/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** a kliknutím na **otevřít v aplikaci Studio** Stáhněte kopii experimentu do pracovního prostoru Machine Learning Studio (Classic).

Tuto datovou sadu dostanete do svého experimentu takto.

1. Vytvořte nový experiment kliknutím na **+ Nový** ve spodní části okna Machine Learning Studio (Classic). Vyberte **experimentovat**  >   **prázdný experiment**.

1. Experimentu se přiřadí výchozí název, který se zobrazí v horní části plátna. Vyberte tento text a přejmenujte jej na něco smysluplného, například **Predikce ceny automobilu**. Název nemusí být jedinečný.

    ![Přejmenování experimentu](./media/create-experiment/rename-experiment.png)

1. Nalevo od plátna experimentu je paleta datových sad a modulů. Do pole Hledat v horní části palety zadejte **automobile**. Vyhledá se datová sada **Automobile price data (Raw)**. Přetáhněte tuto datovou sadu na plátno experimentu.

    ![Vyhledejte datovou sadu automobilů a přetáhněte ji na plátno experimentu.](./media/create-experiment/type-automobile.png)

Pokud se chcete podívat, jak tato data vypadají, klikněte na výstupní port v dolní části datové sady automobilu a potom vyberte **vizualizovat**.

![Klikněte na výstupní port a vyberte Vizualizovat.](./media/create-experiment/select-visualize.png)

> [!TIP]
> Vstupní a výstupní porty datových sad a modulů jsou reprezentované malými kroužky – vstupní porty v horní části, výstupní porty v dolní části.
Pokud chcete vytvořit tok dat prostřednictvím experimentu, připojte výstupní port jednoho modulu ke vstupnímu portu jiného.
V libovolném okamžiku můžete kliknout na výstupní port datové sady nebo modulu a prohlédnout si, jak v tomto bodě vypadá tok dat.

V této datové sadě každý řádek představuje automobil a proměnné přidružené k jednotlivým automobilům se zobrazí jako sloupce. Za použití proměnných pro konkrétní automobil předpovídáme cenu v pravém sloupci (sloupec 26 s názvem Price).

![Zobrazte data automobilů v okně vizualizace dat.](./media/create-experiment/visualize-auto-data.png)

Kliknutím na **x** v pravém horním rohu zavřete okno vizualizace.

## <a name="prepare-the-data"></a>Příprava dat

Před analýzou datové sady bývá zpravidla nutné sadu nějakým způsobem předzpracovat. Možná jste si ve sloupcích různých řádků všimli chybějících hodnot. Tyto chybějící hodnoty se musí vyčistit, aby model mohl data správně analyzovat. Odebereme všechny řádky, které obsahují chybějící hodnoty. Hodnoty ve sloupci **normalized-losses** navíc z velké části chybí, proto tento sloupec v modelu zcela vynecháme.

> [!TIP]
> Vyčištění chybějících hodnot ze vstupních dat je pro většinu modulů nutností.

Nejprve přidáme modul, který zcela odebere sloupec **normalizovaných ztrát** . Pak přidáme další modul, který odebere všechny řádky, které obsahují chybějící data.

1. Do pole Hledat v horní části palety modulů zadejte **Výběr sloupců** a vyhledejte modul [Výběr sloupců v datové sadě][select-columns] . Přetáhněte ho na plátno experimentu. Tento modul umožňuje vybrat, které sloupce dat chceme zahrnout do modelu, nebo je z modelu naopak vyloučit.

1. Připojte výstupní port datové sady **dat automobilu (RAW)** k vstupnímu portu pro výběr sloupců v datové sadě.

    ![Přidejte modul Výběr sloupců v datové sadě na plátno experimentu a připojte ho.](./media/create-experiment/type-select-columns.png)

1. Klikněte na modul [Výběr sloupců v datové sadě][select-columns] a v podokně **Vlastnosti** klikněte na **Spustit selektor sloupců**.

   - Vlevo klikněte na **S pravidly**.
   - V části **Začít s** klikněte na **Všechny sloupce**. Tato pravidla přímo [vyberou sloupce v datové sadě][select-columns] , aby prošla všemi sloupci (s výjimkou těch sloupců, které se chystáme vyloučit).
   - V rozevíracích seznamech vyberte **Vyloučit** a **názvy sloupců** a klikněte do textového pole. Zobrazí se seznam sloupců. Vyberte sloupec **normalized-losses**, který se tak přidá do textového pole.
   - Kliknutím na tlačítko se značkou zaškrtnutí (OK) zavřete selektor sloupců (vpravo dole).

     ![Spusťte selektor sloupců a vylučte sloupec normalized-losses.](./media/create-experiment/launch-column-selector.png)

     Podokno vlastností modulu **Výběr sloupců v datové sadě** teď indikuje, že modul bude procházet všechny sloupce datové sady kromě **normalized-losses**.

     ![Podokno vlastností indikuje, že sloupec normalized-losses je vyloučený.](./media/create-experiment/showing-excluded-column.png)

     > [!TIP] 
     > Kliknutím dvakrát na modul a zadáním textu je možné přidat k modulu komentář. To vám může pomoci rychle poznat, jaký je účel modulu v experimentu. V tomto případě klikněte dvakrát na modul [Výběr sloupců v datové sadě][select-columns] a zadejte komentář Vyloučit normalized-losses.

     ![Klikněte dvakrát na modul a přidejte komentář.](./media/create-experiment/add-comment.png)

1. Přetáhněte modul [Vyčištění chybějících dat][clean-missing-data] na plátno experimentu a propojte jej s modulem [Výběr sloupců v datové sadě][select-columns]. V podokně **Vlastnosti** vyberte v části **Režim čištění** možnost **Odstranit celý řádek**. Tyto možnosti přímo [čistí chybějící data][clean-missing-data] k vyčištění dat odstraněním řádků, které obsahují chybějící hodnoty. Klikněte dvakrát na modul a zadejte komentář Odstranění řádků s chybějícími hodnotami.

    ![Pro modul Vyčištění chybějících dat nastavte režim čištění na Odstranit celý řádek.](./media/create-experiment/set-remove-entire-row.png)

1. Spusťte experiment kliknutím na **SPUSTIT** v dolní části stránky.

    Až se spuštění experimentu dokončí, u všech modulů se zobrazí zelená značka zaškrtnutí, která označuje, že jejich činnost úspěšně skončila. Všimněte si také stavu **Konec běhu** v pravém horním rohu.

    ![Po spuštění by experiment měl vypadat asi takhle nějak.](./media/create-experiment/early-experiment-run.png)

> [!TIP]
> Proč jsme experiment teď spustili? Spuštěním experimentu jsme zajistili, aby definice sloupců pro naše data prošly z původní datové sady přes modul [Výběr sloupců v datové sadě][select-columns] a přes modul [Vyčištění chybějících dat][clean-missing-data]. To znamená, že všechny moduly, které připojíme k modulu [Vyčištění chybějících dat][clean-missing-data], budou také mít tytéž informace.

Teď máme čistá data. Pokud si chcete zobrazit vyčištěnou datovou sadu, klikněte na levý výstupní port modulu [Vyčištění chybějících dat][clean-missing-data] a vyberte **Vizualizovat**. Všimněte si, že již není zobrazen sloupec **normalized-losses** a že nechybí žádné hodnoty.

Nyní když jsou data vyčištěna, můžete určit, které příznaky použijeme v prediktivním modelu.

## <a name="define-features"></a>Definovat funkce

Ve strojovém učení jsou *funkce* jednotlivé měřitelné vlastnosti něčeho, co vás zajímá. V naší datové sadě každý řádek představuje jeden automobil a každý sloupec je příznak daného automobilu.

Nalezení správné sady příznaků pro vytvoření prediktivního modelu vyžaduje experimentování a znalost problému, který chcete vyřešit. Některé příznaky jsou pro predikci cíle vhodnější než jiné. Některé funkce mají silnou korelaci s jinými funkcemi a dají se odebrat. Například příznaky city-mpg a highway-mpg jsou vzájemně těsně propojené, takže stačí jeden z nich odebrat a ponechat jenom ten druhý, aniž by to vytvářenou predikci výrazně ovlivnilo.

Vytvořme model, který používá podmnožinu příznaků naší datové sady. Později můžete vybrat jiné příznaky, spustit experiment znovu a zjistit, jestli nedostanete lepší výsledky. Nejdřív ale vyzkoušíme tyto funkce:

> Značka, styl textu, kolo – základ, velikost motoru, výkon, špička, max./min., silniční-MPG, cena

1. Na plátno experimentu přetáhněte další modul [Výběr sloupců v datové sadě][select-columns]. Propojte levý výstupní port modulu [Vyčištění chybějících dat][clean-missing-data] se vstupem modulu [Výběr sloupců v datové sadě][select-columns].

    ![Připojte modul Výběr sloupců v datové sadě k modulu Vyčištění chybějících dat.](./media/create-experiment/connect-clean-to-select.png)

1. Poklikejte na modul a zadejte Výběr příznaků pro predikci.

1. V podokně **Vlastnosti** klikněte na **Spustit selektor sloupců**.

1. Klikněte na **S pravidly**.

1. V části **Začít s** klikněte na **Žádné sloupce**. V řádku filtru vyberte **Zahrnout** a **názvy sloupců** a vyberte náš seznam názvů sloupců v textovém poli. Tento filtr přesměruje modul, aby nepředával žádné sloupce (funkce) s výjimkou těch, které určíme.

1. Klikněte na tlačítko zaškrtnutí (OK).

    ![Vyberte sloupce (příznaky), které se mají zahrnout do predikce.](./media/create-experiment/select-columns-to-include.png)

Tento modul vytváří filtrovanou datovou sadu obsahující jenom ty funkce, které chceme předat do výukového algoritmu, který použijeme v dalším kroku. Později se můžete vrátit a zkusit jiný výběr příznaků.

## <a name="choose-and-apply-an-algorithm"></a>Volba a použití algoritmu

Nyní když jsou data připravena, tvorba prediktivního modelu sestává z trénování a testování. Naše data použijeme pro trénování modelu. Potom model otestujeme a zjistíme, jak přesně dokáže předpovídat ceny.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*Klasifikace* a *regrese* jsou dva typy technik strojového učení se supervizí. Klasifikace předpovídá odpověď na základě definované sady kategorií, třeba barvy (červená, modrá nebo zelená). Regrese se používá k předpovědi čísel.

Chceme předpovědět cenu, což je číslo, a tak použijeme regresní algoritmus. V tomto příkladu použijeme model *lineární regrese* .


Model trénujeme tím, že mu poskytneme sadu dat, která zahrnují cenu. Model projde data a hledá korelaci mezi příznaky automobilu a jeho cenou. Potom model otestujeme. Poskytneme mu sadu příznaků pro automobily, které známe, a uvidíme, do jaké míry se predikce modelu blíží známé ceně.

Naše data můžeme použít jak pro trénování modelu, tak pro jeho otestování. Dají se totiž rozdělit na samostatné sady pro trénování a testování.

1. Vyberte a přetáhněte na plátno experimentu modul [Rozdělení dat][split] a propojte jej s posledním modulem [Výběr sloupců v datové sadě][select-columns].

1. Klikněte na modul [Rozdělení dat][split]. Modul se vybere. Vyhledejte **Podíl řádků v první výstupní sadě dat** (v podokně **Vlastnosti** napravo od plátna) a nastavte ho na 0,75. Takto použijeme 75 procent dat pro trénování modelu a 25 procent si ponecháme na testování.

    ![Nastavte podíl pro rozdělení modulu Rozdělení dat na 0,75.](./media/create-experiment/set-split-data-percentage.png)

    > [!TIP]
    > Změnou parametru **Náhodná počáteční hodnota** je možné pro trénování a testování vytvořit různé náhodné vzorky. Tento parametr řídí nastavování počáteční hodnoty pseudonáhodného generátoru čísel.

1. Spusťte experiment. Při spuštění experimentu moduly [Výběr sloupců v datové sadě][select-columns] a [Rozdělení dat][split] předají definice sloupců do modulů, které přidáme jako další.  

1. Nyní vyberte algoritmus učení. Na paletě modulů nalevo od plátna rozbalte kategorii **Strojové učení** a pak **Inicializovat model**. Tímto se zobrazí několik kategorií modulů, které je možné použít k inicializaci algoritmů strojového učení. Pro tento experiment vyberte modul [Lineární regrese][linear-regression] v kategorii **Regrese** a přetáhněte ho na plátno experimentu. (Tento modul můžete najít i tak, že do pole Hledat palety zadáte lineární regrese.)

1. Najděte modul [Trénování modelu][train-model] a přetáhněte ho na plátno experimentu. Propojte výstup modulu [Lineární regrese][linear-regression] s levým vstupem modulu [Trénování modelu][train-model] a potom propojte výstup trénovacích dat (levý port) modulu [Rozdělení dat][split] s pravým vstupem modulu [Trénování modelu][train-model].

    ![Připojte modul Trénování modelu k modulům Lineární regrese a Rozdělení dat.](./media/create-experiment/connect-train-model.png)

1. Klikněte na modul [Trénování modelu][train-model], v podokně **Vlastnosti** klikněte na **Spustit selektor sloupců** a vyberte sloupec **price**. **Cena** je hodnota, kterou náš model hodlá předpovědět.

    V selektoru sloupců vyberete sloupec **price** – přesunete ho ze seznamu **Dostupné sloupce** do seznamu **Vybrané sloupce**.

    ![Pro modul Trénování modelu vyberte sloupec price.](./media/create-experiment/select-price-column.png)

1. Spusťte experiment.

Výsledkem je natrénovaný model, který je možné použít ke stanovení skóre pro nová data automobilů a k následné predikci cen.

![Po spuštění by experiment měl vypadat asi takhle nějak.](./media/create-experiment/second-experiment-run.png)

## <a name="predict-new-automobile-prices"></a>Předpověď cen nových automobilů

Nyní když jsme natrénovali model pomocí 75 procent dat, můžeme model použít ke stanovení skóre u zbylých 25 procent dat a zjistit, jak dobře model funguje.

1. Najděte modul [Určení skóre modelu][score-model] a přetáhněte ho na plátno experimentu. Propojte výstup modulu [Trénování modelu][train-model] s levým vstupním portem modulu [Určení skóre modelu][score-model]. Propojte výstup testovacích dat (pravý port) modulu [Rozdělení dat][split] s pravým vstupním portem modulu [Určení skóre modelu][score-model].

    ![Propojte modul Určení skóre modelu s moduly Lineární regrese a Rozdělení dat.](./media/create-experiment/connect-score-model.png)

1. Spusťte experiment a zobrazte výstup z modulu [skóre modelu][score-model] kliknutím na výstupní port [modelu skóre][score-model] a vyberte **vizualizovat**. Na výstupu se zobrazí predikované hodnoty ceny a známé hodnoty v testovacích datech.  

    ![Výstup modulu Určení skóre modelu](./media/create-experiment/score-model-output.png)

1. Nakonec otestujeme kvalitu výsledků. Najděte modul [Vyhodnocení modelu][evaluate-model], přetáhněte ho na plátno experimentu a propojte výstup modulu [Určení skóre modelu][score-model] s levým vstupem modulu [Vyhodnocení modelu][evaluate-model]. Konečný experiment by měl vypadat přibližně takto:

    ![Konečný experiment](./media/create-experiment/complete-linear-regression-experiment.png)

1. Spusťte experiment.

Zobrazte výstup modulu [Vyhodnocení modelu][evaluate-model] tak, že kliknete na výstupní port a vyberete **Vizualizovat**.

![Výsledky vyhodnocení pro experiment](./media/create-experiment/evaluation-results.png)

Pro náš model se zobrazí následující statistiky:

- **Střední absolutní chyba** (MAE): Průměr absolutních chyb (*chyba* je rozdíl mezi předpovězenou a skutečnou hodnotu)
- **Odmocnina střední kvadratické chyby** (RMSE): Druhá odmocnina průměru kvadratických chyb předpovědí na základě testovací datové sady
- **Relativní absolutní chyba**: Průměr absolutních chyb relativních k absolutnímu rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot
- **Relativní kvadratická chyba**: Průměr kvadratických chyb relativních ke kvadratickému rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot
- **Koeficient spolehlivosti**: Znám také jako **hodnota spolehlivosti R**, tedy statistická metrika označující kvalitu přizpůsobení modelu datům

Pro každou statistiku chyb platí, že menší hodnota je lepší. Menší hodnota označuje, že předpověď přesněji odpovídá skutečným hodnotám. V případě **koeficientu spolehlivosti** platí, že čím bližší je jeho hodnota hodnotě jedna (1,0), tím lepší jsou předpovědi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili jednoduchý experiment s použitím ukázkové datové sady. Chcete-li prozkoumat proces tvorby a nasazení modelu podrobněji, pokračujte do kurzu prediktivního řešení.

> [!div class="nextstepaction"]
> [Kurz: vývoj prediktivního řešení v studiu (Classic)](tutorial-part1-credit-risk.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
