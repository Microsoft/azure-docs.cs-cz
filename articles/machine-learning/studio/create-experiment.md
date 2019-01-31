---
Název: Jednoduchý experiment titleSuffix: Azure Machine Learning Studio Popis: Tento kurz Machine Learningu vás provede jednoduchým experimentem z oblasti datové vědy. Pomocí regresního algoritmu předpovíme cenu automobilu.
Services: machine learningu ms.service: ms.subservice strojového učení: studio ms.topic: kurz

Autor: garyericson ms.author: garye ms.custom: seodec18 ms.date: 03/20/2017
---

# <a name="tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Kurz: Vytvoření prvního experimentu datové vědy v nástroji Azure Machine Learning Studio

Pokud jste **Azure Machine Learning Studio** nikdy nepoužívali, je tento kurz právě pro vás.

V tomto kurzu vás provedeme prvním použitím sady Studio a ukážeme vám, jak vytvořit experiment strojového učení. Tento experiment otestuje analytický model, který bude předpovídat cenu automobilu podle různých proměnných, třeba značky a technických specifikací.

> [!NOTE]
> V tomto kurzu si ukážeme základy toho, jak pomocí myši přetáhnout moduly do experimentu, vzájemně je propojit, spustit experiment a prohlédnout si výsledky. Nebudeme se zabývat obecnými tématy strojového učení ani tím, jak vybírat a využívat více než 100 předdefinovaných algoritmů a modulů pro manipulaci s daty, které jsou součástí sady Studio.
>
>Pokud se strojovým učením teprve začínáte, může pro vás být dobrým výchozím bodem seriál videí [Data Science for Beginners (Datová věda pro začátečníky)](data-science-for-beginners-the-5-questions-data-science-answers.md). Tento seriál videí představuje vynikající úvod do strojového učení s využitím jazyka a konceptů každodenního života.
>
>Pokud už strojové učení znáte, ale hledáte obecnější informace o sadě Machine Learning Studio a algoritmech strojového učení, které obsahuje, vhodné zdroje informací můžete najít tady:
>
- [Co je Machine Learning Studio?](what-is-ml-studio.md) - Toto téma obsahuje obecný přehled sady Studio.
- [Základy služby Machine Learning s příklady algoritmů](basics-infographic-with-algorithm-examples.md) – Tato infografika je užitečná k tomu, abyste se blíže seznámili s různými algoritmy strojového učení, které jsou součástí sady Machine Learning Studio.
- [Průvodce službou Machine Learning](https://gallery.cortanaintelligence.com/Tutorial/Machine-Learning-Guide-1) – Tato příručka zahrnuje podobné informace jako infografika uvedená výše, ale v interaktivním formátu.
- [Stručný přehled algoritmů strojového učení](algorithm-cheat-sheet.md) a [Jak zvolit algoritmy pro Microsoft Azure Machine Learning](algorithm-choice.md) – Tento plakát ke stažení a doprovodný článek podrobně rozebírají algoritmy sady Studio.
- [Ve službě Machine Learning Studio: Algoritmus a pomozte modulu](https://msdn.microsoft.com/library/azure/dn905974.aspx) – Toto je úplný odkaz pro všechny moduly ze studia, včetně algoritmů machine learningu



## <a name="how-does-machine-learning-studio-help"></a>V čem je přínos nástroje Machine Learning Studio?

Machine Learning Studio usnadňuje vytvoření pokusu přetahováním předem naprogramovaných modulů s technikami prediktivního modelování.

Pomocí interaktivního vizuálního pracovního prostoru můžete přetáhnout ***datové sady*** a ***moduly*** analýz na interaktivní plátno. Vzájemně je propojíte a vytvoříte ***experiment***, který spustíte v sadě Machine Learning Studio.
***Vytvoříte model***, ***natrénujete ho*** a potom ***stanovíte skóre a otestujete ho***.

Můžete iterovat návrh modelu, upravovat experiment a spouštět ho, dokud nedává výsledky, jaké potřebujete. Když je model hotový, můžete ho publikovat jako ***webovou službu***, aby mu i ostatní mohli zasílat nová data a získávat na oplátku predikce.

## <a name="open-machine-learning-studio"></a>Otevřít Machine Learning Studio

Pokud chcete začít se sadou Studio, přejděte na [https://studio.azureml.net](https://studio.azureml.net). Pokud už jste se do nástroje Machine Learning Studio někdy přihlašovali, klikněte na odkaz **Přihlásit se**. Jinak klikněte na **Zaregistrovat** a vyberte si mezi bezplatnou a placenou možností.

![Přihlásit se k sadě Machine Learning Studio][sign-in-to-studio]
<br/>
***Přihlásit se k sadě Machine Learning Studio***

## <a name="five-steps-to-create-an-experiment"></a>Vytvoření experimentu v pěti krocích

V tomto kurzu strojového učení vytvoříte experiment provedením pěti základních kroků v nástroji Machine Learning Studio, v rámci kterých sestavíte model, natrénujete ho a stanovíte jeho skóre:

- **Vytvoření modelu**
    - [Krok 1: Získání dat]
    - [Krok 2: Příprava dat]
    - [Krok 3: Definice funkcí]
- **Trénování modelu**
    - [Krok 4: Volba a použití algoritmu učení]
- **Stanovení skóre a otestování modelu**
    - [Krok 5: Předpověď cen nových automobilů]

[Krok 1: Získání dat]: #step-1-get-data
[Krok 2: Příprava dat]: #step-2-prepare-the-data
[Krok 3: Definice funkcí]: #step-3-define-features
[Krok 4: Volba a použití algoritmu učení]: #step-4-choose-and-apply-a-learning-algorithm
[Krok 5: Předpověď cen nových automobilů]: #step-5-predict-new-automobile-prices

> [!TIP] 
> Pracovní kopii následujícího experimentu najdete v [galerii Azure AI](https://gallery.cortanaintelligence.com). Přejděte k části **[První experiment z oblasti datové vědy – predikce ceny automobilu](https://gallery.cortanaintelligence.com/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)**, klikněte na **Open in Studio** (Otevřít v sadě Studio) a stáhněte kopii experimentu do pracovního prostoru Machine Learning Studio.


## <a name="step-1-get-data"></a>Krok 1: Získání dat

První věc, kterou budete pro strojové učení potřebovat, jsou data.
Machine Learning Studio obsahuje několik ukázkových datových sad, které můžete použít, nebo můžete data importovat z mnoha zdrojů. V tomto příkladu použijeme ukázkovou datovou sadu **Automobile price data (Raw)**, která je součástí vašeho pracovního prostoru.
Tato datová sada obsahuje záznamy řady různých automobilů, včetně informací o značce, modelu, technických specifikacích a ceně.

Tuto datovou sadu dostanete do svého experimentu takto.

1. Kliknutím na **+NOVÝ** ve spodní části okna nástroje Machine Learning Studio vytvořte nový experiment a vyberte **EXPERIMENT** a **Prázdný experiment**.

2. Experimentu se přiřadí výchozí název, který se zobrazí v horní části plátna. Vyberte tento text a přejmenujte jej na něco smysluplného, například **Predikce ceny automobilu**. Název nemusí být jedinečný.

    ![Přejmenování experimentu][rename-experiment]

2. Nalevo od plátna experimentu je paleta datových sad a modulů. Do pole Hledat v horní části palety zadejte **automobile**. Vyhledá se datová sada **Automobile price data (Raw)**. Přetáhněte tuto datovou sadu na plátno experimentu.

    ![Vyhledejte datovou sadu automobilů a přetáhněte ji na plátno experimentu.][type-automobile]
    <br/>
    ***Vyhledejte datovou sadu automobilů a přetáhněte ji na plátno experimentu.***

Pokud se chcete podívat, jak tato data vypadají, klikněte na výstupní port ve spodní části datové sady automobilů a vyberte **Vizualizovat**.

![Klikněte na výstupní port a vyberte Vizualizovat.][select-visualize]
<br/>
***Klikněte na výstupní port a vyberte Vizualizovat.***

> [!TIP]
> Vstupní a výstupní porty datových sad a modulů jsou reprezentované malými kroužky – vstupní porty v horní části, výstupní porty v dolní části.
Pokud chcete vytvořit tok dat prostřednictvím experimentu, připojte výstupní port jednoho modulu ke vstupnímu portu jiného.
V libovolném okamžiku můžete kliknout na výstupní port datové sady nebo modulu a prohlédnout si, jak v tomto bodě vypadá tok dat.

V této ukázkové datové sadě každou instanci automobilu představuje jeden řádek a proměnné přidružené k automobilům se zobrazují jako sloupce. Na základě hodnot proměnných pro konkrétní automobil se pokusíme odhadnout cenu ve sloupci nejvíce vpravo (sloupec 26 se záhlavím price).

![Zobrazte data automobilů v okně vizualizace dat.][visualize-auto-data]
<br/>
***Zobrazte data automobilů v okně vizualizace dat.***

Kliknutím na **x** v pravém horním rohu zavřete okno vizualizace.

## <a name="step-2-prepare-the-data"></a>Krok 2: Příprava dat

Před analýzou datové sady bývá zpravidla nutné sadu nějakým způsobem předzpracovat. Možná jste si ve sloupcích různých řádků všimli chybějících hodnot. Tyto chybějící hodnoty se musí vyčistit, aby model mohl data správně analyzovat. V našem případě odstraníme všechny řádky, ve kterých některé hodnoty chybí. Hodnoty ve sloupci **normalized-losses** navíc z velké části chybí, proto tento sloupec v modelu zcela vynecháme.

> [!TIP]
> Vyčištění chybějících hodnot ze vstupních dat je pro většinu modulů nutností.

Nejdříve přidáme modul, který úplně odebere sloupec **normalized-losses** a potom přidáme další modul, který odebere všechny řádky, ve kterých chybějí data.

1. Do pole Hledat v horní části palety modulů zadejte **výběr sloupců**. Vyhledá se modul [Výběr sloupců v datové sadě ][select-columns]. Ten potom přetáhněte na plátno experimentu. Tento modul umožňuje vybrat, které sloupce dat chceme zahrnout do modelu, nebo je z modelu naopak vyloučit.

2. Připojte výstupní port datové sady **Automobile price data (Raw)** ke vstupnímu portu modulu [Výběr sloupců v datové sadě][select-columns].

    ![Přidejte modul Výběr sloupců v datové sadě na plátno experimentu a připojte ho.][type-select-columns]
    <br/>
    ***Přidejte modul Výběr sloupců v datové sadě na plátno experimentu a připojte ho.***

3. Klikněte na modul [Výběr sloupců v datové sadě][select-columns] a v podokně **Vlastnosti** klikněte na **Spustit selektor sloupců**.

    - Vlevo klikněte na **S pravidly**.
    - V části **Začít s** klikněte na **Všechny sloupce**. Tím modul [Výběr sloupců v datové sadě][select-columns] dostává instrukci, aby prošel všechny sloupce (kromě sloupců, které vyloučíme).
    - V rozevíracích seznamech vyberte **Vyloučit** a **názvy sloupců** a klikněte do textového pole. Zobrazí se seznam sloupců. Vyberte sloupec **normalized-losses**, který se tak přidá do textového pole.
    - Kliknutím na tlačítko se značkou zaškrtnutí (OK) zavřete selektor sloupců (vpravo dole).

    ![Spusťte selektor sloupců a vylučte sloupec normalized-losses.][launch-column-selector]
    <br/>
    ***Spusťte selektor sloupců a vylučte sloupec normalized-losses.***

    Podokno vlastností modulu **Výběr sloupců v datové sadě** teď indikuje, že modul bude procházet všechny sloupce datové sady kromě **normalized-losses**.

    ![Podokno vlastností indikuje, že sloupec normalized-losses je vyloučený.][showing-excluded-column]
    <br/>
    ***Podokno vlastností indikuje, že sloupec normalized-losses je vyloučený.***

    > [!TIP]
    Kliknutím dvakrát na modul a zadáním textu je možné přidat k modulu komentář. To vám může pomoci rychle poznat, jaký je účel modulu v experimentu. V tomto případě klikněte dvakrát na modul [Výběr sloupců v datové sadě][select-columns] a zadejte komentář Vyloučit normalized-losses.

    ![Klikněte dvakrát na modul a přidejte komentář.][add-comment]
    <br/>
    ***Klikněte dvakrát na modul a přidejte komentář.***

3. Přetáhněte modul [Vyčištění chybějících dat][clean-missing-data] na plátno experimentu a propojte jej s modulem [Výběr sloupců v datové sadě][select-columns]. V podokně **Vlastnosti** vyberte v části **Režim čištění** možnost **Odstranit celý řádek**. Tím modul [Vyčištění chybějících dat][clean-missing-data] dostává pokyn k vyčištění dat odstraněním řádků, ve kterých chybí některé hodnoty. Klikněte dvakrát na modul a zadejte komentář Odstranění řádků s chybějícími hodnotami.

    ![Pro modul Vyčištění chybějících dat nastavte režim čištění na Odstranit celý řádek.][set-remove-entire-row]
    <br/>
    ***Pro modul Vyčištění chybějících dat nastavte režim čištění na Odstranit celý řádek.***

4. Spusťte experiment kliknutím na **SPUSTIT** v dolní části stránky.

    Až se spuštění experimentu dokončí, u všech modulů se zobrazí zelená značka zaškrtnutí, která označuje, že jejich činnost úspěšně skončila. Všimněte si také stavu **Konec běhu** v pravém horním rohu.

![Po spuštění by experiment měl vypadat asi takhle nějak.][early-experiment-run]
<br/>
***Po spuštění by experiment měl vypadat asi takhle nějak.***

> [!TIP]
> Proč jsme experiment teď spustili? Spuštěním experimentu jsme zajistili, aby definice sloupců pro naše data prošly z původní datové sady přes modul [Výběr sloupců v datové sadě][select-columns] a přes modul [Vyčištění chybějících dat][clean-missing-data]. To znamená, že všechny moduly, které připojíme k modulu [Vyčištění chybějících dat][clean-missing-data], budou také mít tytéž informace.

Jediné, co jsme do této chvíle v experimentu udělali, bylo vyčištění dat. Pokud si chcete zobrazit vyčištěnou datovou sadu, klikněte na levý výstupní port modulu [Vyčištění chybějících dat][clean-missing-data] a vyberte **Vizualizovat**. Všimněte si, že již není zobrazen sloupec **normalized-losses** a že nechybí žádné hodnoty.

Nyní když jsou data vyčištěna, můžete určit, které příznaky použijeme v prediktivním modelu.

## <a name="step-3-define-features"></a>Krok 3: Definice funkcí

Ve strojovém učení se jako *příznaky* označují jednotlivé měřitelné vlastnosti něčeho, co vás zajímá. V naší datové sadě každý řádek představuje jeden automobil a každý sloupec je příznak daného automobilu.

Nalezení správné sady příznaků pro vytvoření prediktivního modelu vyžaduje experimentování a znalost problému, který chcete vyřešit. Některé příznaky jsou pro predikci cíle vhodnější než jiné. Některé příznaky také mají silnou korelaci s jinými a je možné je odebrat. Například příznaky city-mpg a highway-mpg jsou vzájemně těsně propojené, takže stačí jeden z nich odebrat a ponechat jenom ten druhý, aniž by to vytvářenou predikci výrazně ovlivnilo.

Vytvořme model, který používá podmnožinu příznaků naší datové sady. Později můžete vybrat jiné příznaky, spustit experiment znovu a zjistit, jestli nedostanete lepší výsledky. Nejdřív ale vyzkoušíme tyto funkce:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Na plátno experimentu přetáhněte další modul [Výběr sloupců v datové sadě][select-columns]. Propojte levý výstupní port modulu [Vyčištění chybějících dat][clean-missing-data] se vstupem modulu [Výběr sloupců v datové sadě][select-columns].

    ![Připojte modul Výběr sloupců v datové sadě k modulu Vyčištění chybějících dat.][connect-clean-to-select]
    <br/>
    ***Připojte modul Výběr sloupců v datové sadě k modulu Vyčištění chybějících dat.***

2. Poklikejte na modul a zadejte Výběr příznaků pro predikci.

2. V podokně **Vlastnosti** klikněte na **Spustit selektor sloupců**.

3. Klikněte na **S pravidly**.

4. V části **Začít s** klikněte na **Žádné sloupce**. V řádku filtru vyberte **Zahrnout** a **názvy sloupců** a vyberte náš seznam názvů sloupců v textovém poli. Tím dáváte modul instrukci, aby neprocházel žádné sloupce (příznaky) s výjimkou těch, které určíme.

5. Klikněte na tlačítko zaškrtnutí (OK).

    ![Vyberte sloupce (příznaky), které se mají zahrnout do predikce.][select-columns-to-include]
    <br/>
    ***Vyberte sloupce (příznaky), které se mají zahrnout do predikce.***

Výsledkem je filtrovaná datová sada obsahující jenom ty příznaky, které chceme předat algoritmu učení, který použijeme v dalším kroku. Později se můžete vrátit a zkusit jiný výběr příznaků.

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>Krok 4: Volba a použití algoritmu učení

Nyní když jsou data připravena, tvorba prediktivního modelu sestává z trénování a testování. Naše data použijeme pro trénování modelu. Potom model otestujeme a zjistíme, jak přesně dokáže předpovídat ceny.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*Klasifikace* a *regrese* jsou dva typy technik strojového učení se supervizí. Klasifikace předpovídá odpověď na základě definované sady kategorií, třeba barvy (červená, modrá nebo zelená). Regrese se používá k předpovědi čísel.

Chceme předpovědět cenu, což je číslo, a tak použijeme regresní algoritmus. Pro tento příklad použijeme jednoduchý model *lineární regrese*.

> [!TIP]
> Pokud se chcete o různých typech algoritmů strojového učení a jejich použití dozvědět víc, můžete si pustit první video ze seriálu Data Science for Beginners (Datová věda pro začátečníky), [The five questions data science answers](data-science-for-beginners-the-5-questions-data-science-answers.md) (Prvních pět dotazů, na které datová věda poskytuje odpověď). Můžete se také podívat na infografiku [Základy služby Machine Learning s příklady algoritmů](basics-infographic-with-algorithm-examples.md) nebo si prohlédnout [Stručný přehled algoritmů strojového učení ](algorithm-cheat-sheet.md).

Model trénujeme tím, že mu poskytneme sadu dat, která zahrnují cenu. Model projde data a hledá korelaci mezi příznaky automobilu a jeho cenou. Potom model otestujeme. Poskytneme mu sadu příznaků pro automobily, které známe, a uvidíme, do jaké míry se predikce modelu blíží známé ceně.

Naše data můžeme použít jak pro trénování modelu, tak pro jeho otestování. Dají se totiž rozdělit na samostatné sady pro trénování a testování.

1. Vyberte a přetáhněte na plátno experimentu modul [Rozdělení dat][split] a propojte jej s posledním modulem [Výběr sloupců v datové sadě][select-columns].

2. Klikněte na modul [Rozdělení dat][split]. Modul se vybere. Vyhledejte **Podíl řádků v první výstupní sadě dat** (v podokně **Vlastnosti** napravo od plátna) a nastavte ho na 0,75. Takto použijeme 75 procent dat pro trénování modelu a 25 procent si ponecháme na testování (později můžete experimentovat s různými procentními podíly).

    ![Nastavte podíl pro rozdělení modulu Rozdělení dat na 0,75.][set-split-data-percentage]
    <br/>
    ***Nastavte podíl pro rozdělení modulu Rozdělení dat na 0,75.***

    > [!TIP]
    > Změnou parametru **Náhodná počáteční hodnota** je možné pro trénování a testování vytvořit různé náhodné vzorky. Tento parametr řídí nastavování počáteční hodnoty pseudonáhodného generátoru čísel.

2. Spusťte experiment. Při spuštění experimentu moduly [Výběr sloupců v datové sadě][select-columns] a [Rozdělení dat][split] předají definice sloupců do modulů, které přidáme jako další.  

3. Nyní vyberte algoritmus učení. Na paletě modulů nalevo od plátna rozbalte kategorii **Strojové učení** a pak **Inicializovat model**. Tímto se zobrazí několik kategorií modulů, které je možné použít k inicializaci algoritmů strojového učení. Pro tento experiment vyberte modul [Lineární regrese][linear-regression] v kategorii **Regrese** a přetáhněte ho na plátno experimentu.
(Tento modul můžete najít i tak, že do pole Hledat palety zadáte lineární regrese.)

4. Najděte modul [Trénování modelu][train-model] a přetáhněte ho na plátno experimentu. Propojte výstup modulu [Lineární regrese][linear-regression] s levým vstupem modulu [Trénování modelu][train-model] a potom propojte výstup trénovacích dat (levý port) modulu [Rozdělení dat][split] s pravým vstupem modulu [Trénování modelu][train-model].

    ![Připojte modul Trénování modelu k modulům Lineární regrese a Rozdělení dat.][connect-train-model]
    <br/>
    ***Připojte modul Trénování modelu k modulům Lineární regrese a Rozdělení dat.***

5. Klikněte na modul [Trénování modelu][train-model], v podokně **Vlastnosti** klikněte na **Spustit selektor sloupců** a vyberte sloupec **price**. Toto je hodnota, kterou náš model bude předpovídat.

    V selektoru sloupců vyberete sloupec **price** – přesunete ho ze seznamu **Dostupné sloupce** do seznamu **Vybrané sloupce**.

    ![Pro modul Trénování modelu vyberte sloupec price.][select-price-column]
    <br/>
    ***Pro modul Trénování modelu vyberte sloupec price.***

6. Spusťte experiment.

Výsledkem je natrénovaný model, který je možné použít ke stanovení skóre pro nová data automobilů a k následné predikci cen.

![Po spuštění by experiment měl vypadat asi takhle nějak.][second-experiment-run]
<br/>
***Po spuštění by experiment měl vypadat asi takhle nějak.***

## <a name="step-5-predict-new-automobile-prices"></a>Krok 5: Předpověď cen nových automobilů

Nyní když jsme natrénovali model pomocí 75 procent dat, můžeme model použít ke stanovení skóre u zbylých 25 procent dat a zjistit, jak dobře model funguje.

1. Najděte modul [Určení skóre modelu][score-model] a přetáhněte ho na plátno experimentu. Propojte výstup modulu [Trénování modelu][train-model] s levým vstupním portem modulu [Určení skóre modelu][score-model]. Propojte výstup testovacích dat (pravý port) modulu [Rozdělení dat][split] s pravým vstupním portem modulu [Určení skóre modelu][score-model].

    ![Propojte modul Určení skóre modelu s moduly Lineární regrese a Rozdělení dat.][connect-score-model]
    <br/>
    ***Propojte modul Určení skóre modelu s moduly Lineární regrese a Rozdělení dat.***

2. Spusťte experiment a zobrazte výstup modulu [Určení skóre modelu][score-model] (klikněte na výstupní port modulu [Určení skóre modelu][score-model] a vyberte **Vizualizovat**). Na výstupu se zobrazí predikované hodnoty ceny a známé hodnoty v testovacích datech.  

    ![Výstup modulu Určení skóre modelu][score-model-output]
    <br/>
    ***Výstup modulu Určení skóre modelu***

3. Nakonec otestujeme kvalitu výsledků. Najděte modul [Vyhodnocení modelu][evaluate-model], přetáhněte ho na plátno experimentu a propojte výstup modulu [Určení skóre modelu][score-model] s levým vstupem modulu [Vyhodnocení modelu][evaluate-model].

    > [!TIP]
    > Modul [Vyhodnocení modelu][evaluate-model] má dva vstupní porty, protože se dá využít k porovnání dvou modelů vedle sebe. Později můžete do experimentu přidat další algoritmus a pomocí modulu [Vyhodnocení modelu][evaluate-model] zjistit, který z nich dává lepší výsledky.

4. Spusťte experiment.

Zobrazte výstup modulu [Vyhodnocení modelu][evaluate-model] tak, že kliknete na výstupní port a vyberete **Vizualizovat**.

![Výsledky vyhodnocení pro experiment][evaluation-results]
<br/>
***Výsledky vyhodnocení pro experiment***

Pro náš model se zobrazí následující statistiky:

- **Střední absolutní chyba** (MAE): Průměr absolutních chyb ( *chyba* je rozdíl mezi předpovězenou a skutečnou hodnotu).
- **Střední kořenové spolehlivosti chyba** (RMSE): Druhá odmocnina průměru kvadratických chyb předpovědí na základě testovací datové.
- **Relativní absolutní chyba**: Průměr absolutních chyb relativních k absolutnímu rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot.
- **Relativní spolehlivosti chyba**: Průměr kvadratických chyb relativních ke kvadratickému rozdílu mezi skutečnými hodnotami a průměrem všech skutečných hodnot.
- **Koeficient spolehlivosti**: Také **hodnota spolehlivosti R**, to tedy statistická metrika označující, jak dobře model odpovídá zpracovávaným datům.

Pro každou statistiku chyb platí, že menší hodnota je lepší. Menší hodnota označuje, že předpověď přesněji odpovídá skutečným hodnotám. V případě **koeficientu spolehlivosti** platí, že čím bližší je jeho hodnota hodnotě jedna (1,0), tím lepší jsou předpovědi.

## <a name="final-experiment"></a>Konečný experiment

Konečný experiment by měl vypadat přibližně takto:

![Konečný experiment][complete-linear-regression-experiment]
<br/>
***Konečný experiment***

## <a name="next-steps"></a>Další postup

Právě jste dokončili první kurz strojového učení a máte vytvořený experiment. Teď můžete pokračovat, pokusit se model vylepšit a potom ho nasadit jako prediktivní webovou službu.

- **Iterace pro vylepšení modelu** – Můžete například změnit příznaky, které ve své predikci používáte. Dále můžete upravit vlastnosti algoritmu [Lineární regrese][linear-regression] nebo zkusit úplně jiný algoritmus. Dokonce je možné přidat do experimentu několik algoritmů strojového učení najednou a porovnat dva z nich pomocí modulu [Vyhodnocení modelu][evaluate-model].
Příklad porovnávání několik modelů v jednom experimentu najdete v tématu věnovaném [porovnání regresorů](https://gallery.cortanaintelligence.com/Experiment/Compare-Regressors-5) v [galerii Azure AI](https://gallery.cortanaintelligence.com).

    > [!TIP]
    > Pomocí tlačítka **ULOŽIT JAKO** v dolní části stránky je možné zkopírovat kteroukoli iteraci experimentu. Všechny iterace experimentu si můžete zobrazit kliknutím na **ZOBRAZIT HISTORII SPUŠTĚNÍ** v dolní části stránky. Další podrobnosti najdete v tématu [Správa iterací experimentů v nástroji Azure Machine Learning Studio][runhistory].

[runhistory]: manage-experiment-iterations.md

- **Nasazení modelu jako prediktivní webové služby** – Jakmile budete se svým modelem spokojeni, můžete ho nasadit jako webovou službu, která se dá použít k předvídání cen automobilů na základě nových dat. Další podrobnosti najdete v tématu [Nasazení webové služby Azure Machine Learning][publish].

[publish]: publish-a-machine-learning-web-service.md

Chcete se dozvědět víc? Rozsáhlejší a podrobnější návod k technikám pro vytváření, natrénování, stanovení skóre a nasazení modelu najdete v tématu [Vývoj prediktivního řešení pomocí služby Azure Machine Learning][walkthrough].

[walkthrough]: walkthrough-develop-predictive-solution.md

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
