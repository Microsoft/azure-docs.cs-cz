---
title: Jak zvolit algoritmus
titleSuffix: Azure Machine Learning Studio
description: Jak zvolit algoritmy Azure Machine Learning Studio u dozorovaných, tak i u nedozorovaných učení v clustering, klasifikační nebo regresní experimentů.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 12/18/2017
ms.openlocfilehash: 5edf301ffc87a17963c3c32c7ea1b559e0834cf7
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244466"
---
# <a name="how-to-choose-algorithms-for-azure-machine-learning-studio"></a>Jak zvolit algoritmy pro Azure Machine Learning Studio

Odpověď na otázku "Co algoritmu strojového učení, mám použít?" je vždy "Záleží." To závisí na velikosti, kvality a povaze data. To závisí na co chcete udělat s odpovědí. To závisí na jak matematické algoritmus byl přeložen do pokyny, jak na počítači, který používáte. A závisí na tom, kolik času je k dispozici. Dokonce i nejzkušenějších odborníci přes data nelze zjistit, který algoritmus provede nejlepší před pokusem o nich.

## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Tahák algoritmus Machine Learning

**Microsoft Azure Machine Learning algoritmus Ošidit list** pomáhá výběru správné strojové učení algoritmu pro vaše řešení prediktivní analýzy Azure Machine Learning Studio knihovnou algoritmů.
Tento článek vás provede jeho použití.

> [!NOTE]
> Ke stažení tahák a postupovat podle tohoto článku, přejděte na [služby Machine learning tahák pro Microsoft Azure Machine Learning Studio](algorithm-cheat-sheet.md).
> 
> 

Tento tahák obsahuje velmi určitou cílovou skupinu na paměti: začátek specializují na data vysokoškoláka úroveň machine Learning, chcete-li vybrat algoritmus začít s v nástroji Azure Machine Learning Studio. To znamená, že provede některé Generalizace a oversimplifications, ale můžete odkazuje ve směru bezpečné. Také znamená, že existuje mnoho algoritmů, které tu nejsou uvedené. S růstem Azure Machine Learning zahrnuje úplnější sadu metod, přidáme je.

Tato doporučení jsou kompilované zpětnou vazbu a tipy od mnoha odborníci přes data a machine learning odborníky. Všechno, co jsme Nesouhlasili, ale jste se pokusili k harmonizaci naše názory do hrubý shody. Většina příkazů neshody začínají řetězcem "Závisí..."

### <a name="how-to-use-the-cheat-sheet"></a>Jak používat tahák

Čtení cesty a algoritmus popisků v grafu jako "pro  *&lt;popisek cesty&gt;*, použijte  *&lt;algoritmus&gt;*." Například "pro *rychlost*, použijte *dvě třídy logistické regrese*." Platí se někdy více než jedna větev.
Žádná z nich jsou někdy dokonale vyhovovat. Jejich smyslem je bude pravidlo thumb doporučení, tak Nestarejte se o to je přesné.
Několik odborníci přes data, které jsem už mluvili s uvedené, které pouze že způsob, jak najít ten nejlepší algoritmus je vyzkoušet všechno, co je.

Tady je příklad z [galerii Azure AI](http://gallery.cortanaintelligence.com/) experimentu, který se pokusí několik algoritmů na stejná data a porovnává výsledky: [Porovnejte roc třídění: Písmeno rozpoznávání](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

> [!TIP]
> Pokud si chcete stáhnout a vytisknout diagram s přehledem funkcí nástroje Machine Learning Studio, nahlédněte do tématu [Diagram s přehledem možností nástroje Machine Learning Studio](studio-overview-diagram.md).
> 
> 

## <a name="flavors-of-machine-learning"></a>Typy služby machine learning

### <a name="supervised"></a>Pod dohledem

Učení algoritmy predikci založené na sadě příklady. Například historické ceny akcií slouží k nebezpečí pokusů na budoucí ceny. Každý příklad používá pro vzdělávání je označené hodnotou zájmu – v tomto případě minimální cenu akcie. Algoritmus učení hledá vzory v tyto hodnoty popisků. Může využívat veškeré informace, které můžou být relevantní – den v týdnu, období, finanční data vaší společnosti, typ oboru, přítomnost geopolitických rušivé události, a vypadá každý algoritmus pro různé typy schémat. Po algoritmu našla nejlepší model může, využívá tento vzor k následné predikci pro bez popisku testovacích dat – zítřejší ceny.

Učení je Oblíbené a vhodné typ služby machine learning. S jednou výjimkou všech modulech ve službě Azure Machine Learning jsou pod dohledem učení se supervizí. Existuje několik konkrétních typů učení, které jsou reprezentovány v rámci Azure Machine Learning: klasifikace, regrese a detekce anomálií.

* **Klasifikace**. Když data jsou používány k předpovědi kategorii, učení se také nazývá klasifikace. Při přiřazování bitovou kopii jako obrázek "cat" nebo "pes tomu tak. Pokud existují pouze dvě možnosti, se nazývá **dvěma třídami** nebo **binomický klasifikace**. Pokud je více kategorií, jako při predikci vítěze turnaje NCAA March Madness, tento problém se označuje jako **roc klasifikace**.
* **Regrese**. Je-li hodnotu se očekává se, jak s cenami akcií, nazývá učení regrese.
* **Detekce anomálií**. Cílem je někdy k identifikaci datové body, které se jednoduše neobvyklé. Při zjišťování podvodů například všechny velmi neobvyklému platební karty útraty vzorky se stavem suspect. Je proto celá řada možných odchylky a tedy několik, není možné získat co podvodných aktivit školení příklady vypadá. Přístup, který přijímá detekce anomálií je jednoduše zjistěte, jaké běžné úrovně aktivity vypadá jako (s použitím jiných podvodné transakce historie) a identifikovat cokoli, co se značně liší.

### <a name="unsupervised"></a>Nastavenou možnost bez dohledu

Datové body mají ve službě learning bez dohledu, se k nim má přiřazené žádné popisky. Místo toho je cílem algoritmus učení bez dohledu uspořádání dat nějakým způsobem nebo k popisu struktury. To může znamenat seskupení do clusterů nebo vyhledání různé způsoby prohlížení komplexní data, aby se zobrazovala jednodušší nebo organizovanější.

### <a name="reinforcement-learning"></a>Zpětnovazebnému učení

V zpětnovazební učení získá algoritmu a vybrat akci v reakci na každý datový bod. Algoritmus učení také obdrží signál reward krátkou dobu později, určující, jak kvalitní byl rozhodnutí.
Na základě toho algoritmus změní svoji strategii, abyste dosáhli nejvyšší potřebu. Aktuálně nejsou žádné zpětnovazební učení algoritmu moduly ve službě Azure Machine Learning. Zpětnovazebnému učení je běžné v robotika, kde je sada údajů snímačů přes v jednom bodě v čase na datový bod a algoritmus musíte zvolit robot další akci. Také je, že přírodní přizpůsobit pro Internet věcí, které aplikace.

## <a name="considerations-when-choosing-an-algorithm"></a>Aspekty při výběru algoritmus

### <a name="accuracy"></a>Přesnost

Získání co nejvíce zpřesnili odpovědí je to možné není vždy nutné.
Někdy je odpovídající, v závislosti na tom, co chcete použít pro přibližný. Pokud je to tento případ, je možné výrazně snížili čas zpracování nastolit s další přibližné metody. Další výhodou další přibližné metody je, že bývají přirozeně vyhnout [overfitting](https://youtu.be/DQWI1kvmwRg).

### <a name="training-time"></a>Čas školení

Počet minut nebo hodin, které jsou nezbytné k natrénování modelu zajistí liší algoritmy. Školení čas je často úzce vázané na přesnost – jeden druhý obvykle doprovází. Kromě toho některé algoritmy jsou citlivější na počet datových bodů než jiné.
Po omezenou dobu můžete přimět vybrat algoritmus, zejména v případě, že datová sada je velká.

### <a name="linearity"></a>Linearity

Ujistěte se, velké množství algoritmů strojového učení pomocí linearity. Lineární klasifikace algoritmy předpokládají, že třídy je možné oddělit rovné čáry (nebo jeho vyšší trojrozměrné analogovým). Patří logistické regrese a podporují vektoru počítače (jak je implementován ve službě Azure Machine Learning).
Lineární regrese, algoritmy předpokládají, že trendy v datech podle rovné čáry. Tyto předpoklady nejsou vhodná pro některé problémy, ale na ostatní přinášejí přesnost.

![Hranice lineární mimo třídu][1]

***Lineární bez třídy hranice*** *-spoléhat na lineární klasifikační algoritmus by mělo za následek nízké přesnost*

![Data s nelineárních trendů][2]

***Data s nelineárních trendů*** *-metodou lineární regrese vygeneruje mnohem větší chyb, než je nutné*

Bez ohledu na jejich nebezpečí lineární algoritmy jsou velmi populární jako první řádek útoku. Bývají algorithmically snadné a rychlé školení.

### <a name="number-of-parameters"></a>Počet parametrů

Parametry jsou knoflíky, chcete-li při nastavování algoritmus získá mezi odborníky přes data. Jsou čísla, která ovlivňují chování algoritmus, například toleranci chyb nebo počet iterací nebo možnosti mezi varianty chování algoritmu. Čas na školení a nemusí být algoritmus může být někdy docela citlivé na získání správné nastavení. Obvykle vyžadují algoritmů pomocí parametrů velké počty na většině omyl a najít dobrá kombinace.

Další možností je [parametr sweeping](algorithm-parameters-optimize.md) bloku modulu ve službě Azure Machine Learning, automaticky se pokusí všechny kombinace parametrů v libovolné členitosti zvolíte. Když je skvělý způsob, jak Ujistěte se, že jste předané parametru místa, čas potřebný k natrénování modelu se exponenciálně zvyšuje s počtem parametrů.

Vzhůru je, že máte velký počet parametrů obvykle znamená, že algoritmus má větší flexibilitu. Často dosáhnout velmi dobré přesnost. Za předpokladu, že můžete najít správné kombinace nastavení parametrů.

### <a name="number-of-features"></a>Mnoho funkcí

U určitých typů dat, mnoho funkcí může být velmi velké ve srovnání s počet datových bodů. To je často případ se genetika nebo textová data. Velké množství funkcí může zpomalit některé studijní algoritmy, provádění školení unfeasibly dlouhý čas. Podpora vektoru počítače jsou zvlášť vhodná pro tento případ (viz níže).

### <a name="special-cases"></a>Zvláštní případy

Některé studijní algoritmy určité domněnky o struktuře dat nebo požadovaných výsledků. Pokud nemůžete najít takový, který nejlépe vyhovuje vašim potřebám, poskytuje další užitečné výsledky, přesnějších predikcí nebo kratší časy školení.

| **Algoritmus** | **Přesnost** | **Čas školení** | **Linearity** | **Parametry** | **Poznámky** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Klasifikace dvěma třídami** | | | | | |
| [Logistické regrese](https://msdn.microsoft.com/library/azure/dn905994.aspx) | |● |● |5 | |
| [rozhodovací les](https://msdn.microsoft.com/library/azure/dn906008.aspx) |● |○ | |6 | |
| [rozhodovací Džungle](https://msdn.microsoft.com/library/azure/dn905976.aspx) |● |○ | |6 |Nedostatek paměti nároky na místo |
| [Posílený rozhodovací strom](https://msdn.microsoft.com/library/azure/dn906025.aspx) |● |○ | |6 |Velké paměťové nároky |
| [neurálních sítí](https://msdn.microsoft.com/library/azure/dn905947.aspx) |● | | |9 |[Další přizpůsobení je možné](https://go.microsoft.com/fwlink/?LinkId=402867) |
| [průměrné perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) |○ |○ |● |4 | |
| [počítač vektorové podpory](https://msdn.microsoft.com/library/azure/dn905835.aspx) | |○ |● |5 |Vhodné pro velké funkce sad |
| [místně důkladné podpoře vector machine](https://msdn.microsoft.com/library/azure/dn913070.aspx) |○ | | |8 |Vhodné pro velké funkce sad |
| [Počítač bodu Bayes.](https://msdn.microsoft.com/library/azure/dn905930.aspx) | |○ |● |3 | |
| **Klasifikace víc tříd** | | | | | |
| [Logistické regrese](https://msdn.microsoft.com/library/azure/dn905853.aspx) | |● |● |5 | |
| [rozhodovací les](https://msdn.microsoft.com/library/azure/dn906015.aspx) |● |○ | |6 | |
| [rozhodovací Džungle ](https://msdn.microsoft.com/library/azure/dn905963.aspx) |● |○ | |6 |Nedostatek paměti nároky na místo |
| [neurálních sítí](https://msdn.microsoft.com/library/azure/dn906030.aspx) |● | | |9 |[Další přizpůsobení je možné](https://go.microsoft.com/fwlink/?LinkId=402867) |
| [one-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx) |- |- |- |- |Zobrazit vlastnosti vybrané metody dvěma třídami |
| **Regrese** | | | | | |
| [linear](https://msdn.microsoft.com/library/azure/dn905978.aspx) | |● |● |4 | |
| [Bayesova lineární](https://msdn.microsoft.com/library/azure/dn906022.aspx) | |○ |● |2 | |
| [rozhodovací les](https://msdn.microsoft.com/library/azure/dn905862.aspx) |● |○ | |6 | |
| [Posílený rozhodovací strom](https://msdn.microsoft.com/library/azure/dn905801.aspx) |● |○ | |5 |Velké paměťové nároky |
| [quantile rychlé doménové struktury](https://msdn.microsoft.com/library/azure/dn913093.aspx) |● |○ | |9 |Distribuce spíše než bodu predikcí |
| [neurálních sítí](https://msdn.microsoft.com/library/azure/dn905924.aspx) |● | | |9 |[Další přizpůsobení je možné](https://go.microsoft.com/fwlink/?LinkId=402867) |
| [Poisson](https://msdn.microsoft.com/library/azure/dn905988.aspx) | | |● |5 |Technicky protokolu lineární. Pro odhad počtu |
| [Pořadí](https://msdn.microsoft.com/library/azure/dn906029.aspx) | | | |0 |Pro predikci, pořadí řazení |
| **Detekce anomálií** | | | | | |
| [počítač vektorové podpory](https://msdn.microsoft.com/library/azure/dn913103.aspx) |○ |○ | |2 |Zvlášť vhodná pro velké funkce sad |
| [Detekce anomálií založená na PCA](https://msdn.microsoft.com/library/azure/dn913102.aspx) | |○ |● |3 | |
| [K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/) | |○ |● |4 |Algoritmu clusteringu |

**Algoritmus vlastnosti:**

**●** – zobrazuje vynikající přesnost, časy rychlé školení a použití linearity

**○** -ukazuje dobré přesnost a střední vzdělání časy

## <a name="algorithm-notes"></a>Algoritmus poznámky

### <a name="linear-regression"></a>Lineární regrese

Jak už bylo zmíněno dříve, [lineární regrese](https://msdn.microsoft.com/library/azure/dn905978.aspx) přizpůsobí datové sadě řádku (nebo rovině a hyperplane). Je centrem, snadné a rychlé, ale může být příliš zjednodušenou pro některé problémy.
Tady můžete [lineární regrese kurzu](linear-regression-in-azure.md).

![Data s lineární trend][3]

***Data s lineární trend***

### <a name="logistic-regression"></a>Logistické regrese

Přestože confusingly zahrnuje: regrese"v názvu, logistické regrese je skutečně výkonný nástroj pro [dvěma třídami](https://msdn.microsoft.com/library/azure/dn905994.aspx) a [multiclass](https://msdn.microsoft.com/library/azure/dn905853.aspx) klasifikace. To je rychlé a jednoduché. Skutečnost, že se používá s "-tvaru křivky ne jako přímá čára je přirozeně vhodná pro dělení dat do skupin. Hranice lineární třída poskytuje logistické regrese, takže při použití, ujistěte se, že lineární aproximace je něco, co může existovat s.

![Logistické regrese s daty dvěma třídami se pouze pro jednu funkci][4]

***Logistické regrese s daty dvěma třídami se pouze pro jednu funkci*** *-hranice třídy je bod, ve kterém logistické křivky jen co nejblíže obě třídy*

### <a name="trees-forests-and-jungles"></a>Džungle, stromy a doménové struktury

Rozhodnutí doménovými strukturami ([regrese](https://msdn.microsoft.com/library/azure/dn905862.aspx), [dvěma třídami](https://msdn.microsoft.com/library/azure/dn906008.aspx), a [multiclass](https://msdn.microsoft.com/library/azure/dn906015.aspx)), decision Džungle ([dvěma třídami](https://msdn.microsoft.com/library/azure/dn905976.aspx) a [ multiclass](https://msdn.microsoft.com/library/azure/dn905963.aspx)) a vylepšené rozhodovací stromy ([regrese](https://msdn.microsoft.com/library/azure/dn905801.aspx) a [dvěma třídami](https://msdn.microsoft.com/library/azure/dn906025.aspx)) jsou všechny na základě rozhodovacích stromů, základní služby machine learning koncept. Existuje mnoho variant rozhodovacích stromů, ale všechny to samé udělá – místo funkce rozdělte oblasti s většinou stejný popisek. Mohou to být oblasti konzistentní kategorie nebo konstantní hodnoty, v závislosti na tom, jestli provádíte klasifikační nebo regresní.

![Rozhodovací strom rozděluje prostor funkce][5]

***Rozhodovací strom rozděluje do oblasti hodnot zhruba jednotné místo funkce***

Protože funkce místo dají rozdělit na libovolně malou oblasti, je snadné imagine dělení dostatečně jemně mít jeden datový bod v jedné oblasti. Toto je příkladem overfitting extreme. U velkého souboru stromové struktury jsou k tomu můžete předejít, zkonstruován pomocí zvláštní pozornost matematické přijata, že nejsou korelační stromy. Průměr tento "rozhodovací les" je strom, který zabraňuje overfitting. Rozhodnutí doménové struktury mohou pomocí velké množství paměti. Rozhodovací Džungle jsou hodnotu typu variant, která využívá méně paměti za cenu mírně delší dobu školení.

Posílený rozhodovací stromy vyhnout overfitting tím, že omezíte počet opakování můžete rozdělit a jak datové body jsou povolené v jednotlivých oblastech. Algoritmus vytvoří posloupnost stromové struktury, z nichž každý se učí kompenzovat chyby zanechaný stromové struktuře před. Výsledkem je velmi přesné student, který obvykle použít velké množství paměti. Pro plnou technickou popis, projděte si [původní dokument společnosti Friedman](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Rychlé doménové struktury quantile regrese](https://msdn.microsoft.com/library/azure/dn913093.aspx) varianta rozhodovacích stromů pro zvláštní případ, ve které chcete vědět, nejen typické (střední) hodnotu data v rámci oblasti, ale také ve formě quantiles jeho distribuci.

### <a name="neural-networks-and-perceptrons"></a>Neuronové sítě a perceptrons

Neuronové sítě jsou brain INSPIROVANÉ učení se supervizí pokrývající [multiclass](https://msdn.microsoft.com/library/azure/dn906030.aspx), [dvěma třídami](https://msdn.microsoft.com/library/azure/dn905947.aspx), a [regrese](https://msdn.microsoft.com/library/azure/dn905924.aspx) problémy. Přišli v nekonečné řadě, ale neuronových sítí v Azure Machine Learning jsou všechny formuláře orientované Acyklické grafy. Která znamená, že vstupní funkce jsou předány vpřed (nikdy zpět) prostřednictvím pořadí vrstev před zapnutí do výstupů. V každé vrstvě jsou vstupy váha v různých kombinacích, sčítat a předávají do další vrstva. Tato kombinace jednoduché výpočty výsledkem schopnost další sofistikované třídy hranice a data trendy zdánlivě magic. N vrstvami sítí toto řazení provést "obsáhlého learningu", který zaznamenává díky tolik Odborný generování sestav a Sci-fi.

Tento vysoký výkon nepřejde do stavu zdarma, ale. Neuronové sítě může trvat dlouhou dobu pro trénování, zejména u velkých datových sad s velkým množstvím funkcí. Mají také další parametry, než většina algoritmy, které znamená, že parametr sweeping zajistí rozšíří doba školení.
A pro tyto overachievers, kteří chtějí [zadat své vlastní struktury sítě](https://go.microsoft.com/fwlink/?LinkId=402867), možností je inexhaustible.

![Hranice zjistili neuronových sítí][6]
***hranice zjistili neuronových sítí může být složité a nestandardní***

[Dvěma třídami zprůměrované perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) je neuronových sítí odpověď na časy školení prudce nahoru. Používá strukturu sítě, která poskytuje hranice lineární třídy. Je téměř primitivní standardy dnešní, ale má dlouholeté práce robustní a je dostatečně malá, aby se rychle naučit.

### <a name="svms"></a>SVMs

Support vector počítače (SVMs) najít hranice, který odděluje třídy podle jako široký na okraj co nejvíc. Pokud dvě třídy nemohou být odděleny jasně, algoritmy najít nejlepší hranic, které mohou. Jak je uvedená ve službě Azure Machine Learning [dvěma třídami SVM](https://msdn.microsoft.com/library/azure/dn905835.aspx) to dělá s rovné čáry. (V termínech SVM používá lineární jádra.) Protože to ztěžuje této lineární aproximace, je možné poměrně rychle spouštět. Kde to skutečně vynikne, je s náročnými funkci data, jako text nebo genomický. V těchto případech dokáží SVMs oddělení tříd rychleji a s menším overfitting než většina jiné algoritmy, kromě nutnosti zvýšení množství paměti.

![Hranice třídy počítače vektorové podpory][7]

***Třída hranice typické podporu vektoru počítače maximalizuje okraj oddělujících dvě třídy***

Jiný produkt Microsoft Research [místně hloubkové SVM dvěma třídami](https://msdn.microsoft.com/library/azure/dn913070.aspx) je nelineárních varianta SVM, který bude mít maximálně rychlost a paměti efektivitu lineární verze. Je ideální pro případy, kdy lineární přístup nedává dostatečně správné odpovědi. Vývojáři uchovává ho rychle rozdělení problém na spoustu menších lineární SVM problémů. Přečtěte si [úplný popis](http://proceedings.mlr.press/v28/jose13.html) podrobnosti o jak jejich dali vypnout platí to.

Pomocí dokonalá rozšíření nelineárních SVMs [jedna třída SVM](https://msdn.microsoft.com/library/azure/dn913103.aspx) nakreslí hranici, která úzce popisuje celou datovou sadu. To je užitečné pro detekci anomálií. Jsou dost neobvyklé bude zajímavosti všechny datové body, které spadají úplně mimo tuto hranici.

### <a name="bayesian-methods"></a>Bayesova metody

Bayesova metody mají žádoucí, vysoce kvalitní: vyhnou overfitting. Je to tím, že některé předpoklady předem o pravděpodobně rozdělení odpovědi. Jiné byproduct tohoto přístupu je, že mají velmi malý počet parametrů. Azure Machine Learning má algoritmy Bayesova pro obě klasifikaci ([Two-class Bayes point machine](https://msdn.microsoft.com/library/azure/dn905930.aspx)) a regrese ([lineární regrese Bayesova](https://msdn.microsoft.com/library/azure/dn906022.aspx)).
Všimněte si, že tyto předpokládat, že můžete data rozdělit nebo přizpůsobit přímou čárou.

Na historické Poznámka Bayes' point počítače byly vyvinuty v Microsoft Research. Mají některé výjimečně krásné teoretické práce za nimi stojí. Zúčastněné student směřuje na [původní článek v JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) a [přehledné blogu podle Chris Bishop](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Specializované algoritmy
Pokud máte velmi specifické cílem může být jednoduché. V rámci kolekce Azure Machine Learning jsou algoritmy, které se specializují na:

- RANK – predikce ([ordinální regrese](https://msdn.microsoft.com/library/azure/dn906029.aspx)),
- počet predikcí ([Poissonovo regrese](https://msdn.microsoft.com/library/azure/dn905988.aspx)),
- detekce anomálií (jeden na základě [analýzy hlavní komponenty](https://msdn.microsoft.com/library/azure/dn913102.aspx) a druhý na základě [podporují vektoru počítače](https://msdn.microsoft.com/library/azure/dn913103.aspx))
- Clustering ([K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/))

![Detekce anomálií založená na PCA][8]

***Detekce anomálií založená na PCA*** *-většinu dat spadá do stereotypical distribuce; výrazně odchylují od této distribuční body jsou podezřelé*

![Seskupené pomocí K-means datové sady][9]

***Datové sady jsou rozděleny do pěti clustery pomocí K-means***

K dispozici je také kompletu [víc tříd třídění one-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx), které rozdělí problém klasifikace N-třídy do N-1 dvěma třídami klasifikaci problémy. Přesnost, čas školení a linearity vlastnosti jsou určeny třídění dvěma třídami použít.

![Třídění dvěma třídami utváří klasifikátor tři – třída][10]

***Dvojice třídění dvěma třídami se dá tvoří tři třídy třídění***

Služba Azure Machine Learning zahrnuje také přístup k rozhraní efektivní strojové učení pod názvem [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf).
Zobrazit prosíme, abyste kategorizace tady, protože další klasifikačních a regresních problémy a dokonce i další z částečně bez popisku dat. Můžete nakonfigurovat tak, použijte některou z několika algoritmů, ztráta funkce a algoritmy pro optimalizaci. Byla navržena od základu nahoru efektivní, paralelní a velmi rychlá. Zpracovává sady absurdně vysoké funkcí s malou zřejmý úsilí.
Spuštění a vedou od Microsoft Research vlastní Jan Langford, je zobrazit vzorce jedna položka v poli akcie car algoritmů. Ne každá problém vyhovuje zobrazit, ale pokud je ta vaše, může být vhodné vaše chvíli stoupání křivku postupu zdokonalování v rozhraní. Je také k dispozici jako [samostatné open source kódem](https://github.com/JohnLangford/vowpal_wabbit) v několika jazycích.

## <a name="next-steps"></a>Další kroky

* Infografika ke stažení, která popisuje algoritmy a poskytuje příklady, naleznete v tématu [Infografika ke stažení: Základy s příklady algoritmů služby Machine learning](basics-infographic-with-algorithm-examples.md).

* Seznam podle kategorie všechny dostupných algoritmů strojového učení v nástroji Machine Learning Studio najdete v tématu [inicializovat Model](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/machine-learning-initialize-model) Machine Learning Studio algoritmus a pomůže modulu.

* Úplný abecední seznam algoritmů a modulů v Machine Learning Studio najdete v tématu [seznam A-Z modulů Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/a-z-module-list) Studio algoritmu strojového učení a pomůže modulu.

* Pokud si chcete stáhnout a vytisknout diagram s přehledem funkcí nástroje Machine Learning Studio, nahlédněte do tématu [Diagram s přehledem možností nástroje Machine Learning Studio](studio-overview-diagram.md).

<!-- Media -->

[1]: ./media/algorithm-choice/image1.png
[2]: ./media/algorithm-choice/image2.png
[3]: ./media/algorithm-choice/image3.png
[4]: ./media/algorithm-choice/image4.png
[5]: ./media/algorithm-choice/image5.png
[6]: ./media/algorithm-choice/image6.png
[7]: ./media/algorithm-choice/image7.png
[8]: ./media/algorithm-choice/image8.png
[9]: ./media/algorithm-choice/image9.png
[10]: ./media/algorithm-choice/image10.png
