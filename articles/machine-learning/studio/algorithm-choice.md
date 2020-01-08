---
title: Jak zvolit algoritmus
titleSuffix: ML Studio (classic) - Azure
description: Jak zvolit Azure Machine Learning Studio (klasické) algoritmy pro dohled nad clustery, klasifikací nebo regresních experimentech a jejich nedohledového studia.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: 2073123a61e919c10caaaea141f776e842f4d717
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427738"
---
# <a name="how-to-choose-algorithms-for-azure-machine-learning-studio-classic"></a>Postup výběru algoritmů pro Azure Machine Learning Studio (Classic)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Odpověď na otázku "jaký algoritmus strojového učení mám použít?" je vždy "závisí". Záleží na velikosti, kvalitě a povaze dat. Záleží na tom, co chcete s odpovědí provést. Závisí na tom, jak byl matematický algoritmus přeložen do pokynů pro počítač, který používáte. A závisí na tom, kolik času máte. I zkušení odborníci přes data nemůžou zjistit, který algoritmus bude nejlépe fungovat, než je vyzkouší.

Machine Learning Studio (Classic) poskytuje špičkové algoritmy, jako jsou škálovatelné zesílené rozhodovací stromy, systémy doporučení bayesovského rozhodování, hluboké Neuronovéové sítě a rozhodovací džungle vyvinuté v Microsoft Research. Dostupné jsou i škálovatelné open-sourcové balíčky pro strojové učení, třeba Vowpal Wabbit. Machine Learning Studio (Classic) podporuje algoritmy strojového učení pro třídu s více třídami a binární klasifikaci, regresi a clusteringu. Podívejte se na úplný seznam [modulů Machine Learning](/azure/machine-learning/studio-module-reference/index).
Dokumentace poskytuje některé informace o jednotlivých algoritmech a způsobech ladění parametrů pro optimalizaci algoritmu pro použití.  


## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Tahákový list algoritmu Machine Learning

**[List tahák Algorithm](../algorithm-cheat-sheet.md)** vám pomůže vybrat správný algoritmus strojového učení pro řešení prediktivní analýzy z knihovny Azure Machine Learning algoritmů. Microsoft Azure Machine Learning
Tento článek vás provede postupem použití tohoto tahák listu.

> [!NOTE]
> Pokud si chcete stáhnout list tahák a postupovat podle tohoto článku, přejděte do [listu tahák algoritmu Machine Learning](../algorithm-cheat-sheet.md).
> 
> 

Tato doporučení jsou kompilována zpětná vazba a tipy z mnoha vědeckých pracovníků a odborníků na strojové učení. Nesouhlasili jsme se na všechno, ale pokusili jsme se harmonizovat naše názory na hrubou shodu. Většina prohlášení o neshodě začíná na "to závisí..."


> [!TIP]
> Informace o oblíbených algoritmech, které se používají k zodpovězení běžných otázek strojového učení, najdete v článku [základy strojového učení s příklady algoritmů](basics-infographic-with-algorithm-examples.md).

## <a name="flavors-of-machine-learning"></a>Charakter strojového učení

### <a name="supervised"></a>Pod dohledem

Algoritmy pro učení pod dohledem vedou předpovědi na základě sady příkladů. Například historické ceny akcií lze použít k odhadu budoucích cen. Každý příklad, který se používá pro školení, je označený hodnotou zájem – v tomto případě se jedná o cenu akcií. Algoritmus učení pod dohledem hledá v těchto popiscích hodnot vzory. Může využít jakékoli informace, které mohou být relevantní – den v týdnu, období, finanční data společnosti, typ odvětví, přítomnost rušivých geopolitických událostí – a každý algoritmus vyhledává různé typy vzorů. Až algoritmus najde nejlepší vzorek, použije tento model k tomu, aby předpovědi pro neoznačená testovací data, a to za zítřejší ceny.

Pod dohledem učení je oblíbený a užitečný typ strojového učení. S jednou výjimkou jsou všechny moduly v Azure Machine Learning Studio (Classic) pod dohledem výukové algoritmy. K dispozici je několik specifických typů kurzů pod dohledem, které jsou zastoupeny v Azure Machine Learning Studio (Classic): klasifikace, regrese a detekce anomálií.

* **Klasifikace**. Když se data používají k předpovědi kategorie, je pod dohledem učení také označována jako klasifikace. Jedná se o případ, kdy se přiřadí obrázek jako obrázek buď Cat nebo pes. Pokud jsou k dispozici pouze dvě volby, nazývá se to **dvě třída** nebo **binomické klasifikace**. Pokud existuje více kategorií, jako při předvídání vítěze NCAA Madness turnajových, je tento problém známý jako **klasifikace s více třídami**.
* **Regrese**. Pokud je hodnota předpovězena, stejně jako u uložených cen, se pod dohledem učení říká regrese.
* **Detekce anomálií**. V některých případech je cílem určit jenom datové body, které jsou neobvyklé. Při detekci podvodů jsou například podezřelé všechny vysoce neobvyklé vzory výdajů na platební karty. Možné variace jsou tak velké množství a příklady výukových programů tak pár, takže není možné zjistit, jaké podvodné aktivity vypadá jako. Přístup k detekci anomálií je jednoduchým způsobem, jak najít normální aktivitu (pomocí historie nepodvodných transakcí) a určit něco, co se významně liší.

### <a name="unsupervised"></a>Bez dohledu

V bezdohledovém učení se k datovým bodům nevztahují žádné popisky. Místo toho je cílem nesledovaného výukového algoritmu způsob, jak uspořádat data nějakým způsobem nebo popsat její strukturu. To může znamenat seskupování do clusterů nebo hledání různých způsobů prohlížení složitých dat tak, aby se zobrazovala jednodušší nebo organizovanější.

### <a name="reinforcement-learning"></a>Zpětnovazební učení

V procesu posílení učení se algoritmus při reakci na každý datový bod vybere jako akce. Výukový algoritmus také obdrží krátkou dobu a později indikuje, jak dobrá bylo rozhodnutí.
Na základě tohoto algoritmu upraví jeho strategii, aby se dosáhlo nejvyšší odměny. V současné době nejsou v Azure Machine Learning Studio (Classic) žádné moduly pro výukové algoritmy. Posílení učení je běžné v autorobotics, kde sada čtecích senzorů v jednom okamžiku v čase je datový bod a algoritmus musí zvolit další akci robota. Je také přirozené přizpůsobení pro Internet věcí aplikace.

## <a name="considerations-when-choosing-an-algorithm"></a>Důvody pro výběr algoritmu

### <a name="accuracy"></a>Přesnost

Získání nejpřesnější možné odpovědi není vždy nutné.
V závislosti na tom, co chcete použít pro, je někdy sblížení vhodné. Pokud je to tento případ, může být možné zkrátit čas zpracování tím, že se dokončí více přibližnými metodami. Další výhodou více přibližných metod je, že přirozeně brání v jejich přebudování.

### <a name="training-time"></a>Doba trénování

Počet minut nebo hodin nutných ke vzdělávání modelu se v různých algoritmech liší. Čas školení je často úzce vázaný na přesnost – jedna obvykle je součástí druhé. Kromě toho jsou některé algoritmy citlivější na počet datových bodů než jiné.
Když je čas omezený, může nastavit algoritmus, zejména v případě, že je datová sada velká.

### <a name="linearity"></a>Linearita

Spousta algoritmů strojového učení využívá linearitu. Algoritmy lineární klasifikace předpokládají, že třídy mohou být odděleny rovnou čárou (nebo s vyšším rozměrem analogového). Mezi ně patří Logistická regrese a podpora vektorových počítačů (jak je implementováno v Azure Machine Learning Studio (Classic)).
Algoritmy lineární regrese předpokládají, že trendy dat následují rovnou čáru. Tyto předpoklady nejsou u některých problémů chybné, ale u ostatních přináší přesnost na více počítačů.

![Hranice jiné než lineární třídy](./media/algorithm-choice/image1.png)

***Nelineární hranice třídy*** *– spoléhání se na algoritmus lineární klasifikace by způsobil nízkou přesnost* .

![Data s nelineárním trendem](./media/algorithm-choice/image2.png)

***Data s nelineárním trendem*** *– při použití metody lineární regrese by vygenerovala mnohem větší chybu, než je potřeba* .

Bez ohledu na jejich nebezpečí jsou lineární algoritmy velmi oblíbené jako první útok. Mají za následek algorithmically jednoduché a rychlé ke školení.

### <a name="number-of-parameters"></a>Počet parametrů

Parametry jsou ovladače, které vám pomohou při nastavování algoritmu zapnout datový vědecký pracovník. Jsou to čísla, která mají vliv na chování algoritmu, jako je odolnost proti chybám, počet iterací nebo možnosti mezi variantami, jak se algoritmus chová. Čas školení a přesnost algoritmu může někdy být poměrně citlivý, aby bylo možné získat pouze ta správná nastavení. Algoritmy s velkým počtem parametrů obvykle vyžadují většinu zkušební verze a chybu, aby bylo možné najít dobrou kombinaci.

Alternativně je v Azure Machine Learning Studio (Classic) blok [parametrů](algorithm-parameters-optimize.md) modulu, který se automaticky snaží všechny kombinace parametrů v libovolné členitosti, kterou si zvolíte. I když je to skvělý způsob, jak se ujistit, že jste přepnuli prostor parametrů, čas potřebný ke zvýšení modelu se zvyšuje exponenciálně s počtem parametrů.

Je to, že má mnoho parametrů obvykle indikuje, že algoritmus má větší flexibilitu. Často dosahuje velmi dobré přesnosti, pokud můžete najít správnou kombinaci nastavení parametrů.

### <a name="number-of-features"></a>Počet funkcí

U určitých typů dat může být počet funkcí velmi velký v porovnání s počtem datových bodů. To je často případ s geneticky nebo textovými daty. Velký počet funkcí může zpomalitovat některé algoritmy učení, takže školení se unfeasibly dlouhou dobu. Podpora vektorových počítačů je zvláště vhodná pro tento případ (viz níže).

### <a name="special-cases"></a>Zvláštní případy

Některé výukové algoritmy vedou konkrétní předpoklady ke struktuře dat nebo k požadovaným výsledkům. Pokud zjistíte, které vyhovuje vašim potřebám, může vám dodávat užitečnější výsledky, přesnější předpovědi nebo rychlejší školicí časy.

| **Algoritmus** | **Údajů** | **Čas školení** | **Linearita** | **Parametry** | **Poznámky** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Klasifikace se dvěma třídami** | | | | | |
| [Logistická regrese](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) | |● |● |5 | |
| [rozhodovací doménová struktura](/azure/machine-learning/studio-module-reference/two-class-decision-forest) |● |○ | |6 | |
| [rozhodnutí Jungle](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) |● |○ | |6 |Nedostatečné nároky na paměť |
| [Zesílený rozhodovací strom](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree) |● |○ | |6 |Velké nároky na paměť |
| [Neuronové síť](/azure/machine-learning/studio-module-reference/two-class-neural-network) |● | | |9 |[Další přizpůsobení je možné](azure-ml-netsharp-reference-guide.md) |
| [Průměrná Perceptron](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) |○ |○ |● |4 | |
| [Podpora vektorového počítače](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) | |○ |● |5 |Vhodné pro velké sady funkcí |
| [místně hluboká podpora vektorového počítače](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) |○ | | |8 |Vhodné pro velké sady funkcí |
| [Bayes – Point – počítač](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine) | |○ |● |3 | |
| **Klasifikace více tříd** | | | | | |
| [Logistická regrese](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) | |● |● |5 | |
| [rozhodovací doménová struktura](/azure/machine-learning/studio-module-reference/multiclass-decision-forest) |● |○ | |6 | |
| [rozhodnutí Jungle](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle) |● |○ | |6 |Nedostatečné nároky na paměť |
| [Neuronové síť](/azure/machine-learning/studio-module-reference/multiclass-neural-network) |● | | |9 |[Další přizpůsobení je možné](azure-ml-netsharp-reference-guide.md) |
| [one-v-all](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass) |- |- |- |- |Zobrazí vlastnosti vybrané metody dvě třídy. |
| **Nevýhody** | | | | | |
| [lineární](/azure/machine-learning/studio-module-reference/linear-regression) | |● |● |4 | |
| [Bayesovského rozhodování lineární](/azure/machine-learning/studio-module-reference/bayesian-linear-regression) | |○ |● |2 | |
| [rozhodovací doménová struktura](/azure/machine-learning/studio-module-reference/decision-forest-regression) |● |○ | |6 | |
| [Zesílený rozhodovací strom](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) |● |○ | |5 |Velké nároky na paměť |
| [Rychlá Quantile doménové struktury](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) |● |○ | |9 |Spíše distribuce než bod předpovědi |
| [Neuronové síť](/azure/machine-learning/studio-module-reference/neural-network-regression) |● | | |9 |[Další přizpůsobení je možné](azure-ml-netsharp-reference-guide.md) |
| [Poisson](/azure/machine-learning/studio-module-reference/poisson-regression) | | |● |5 |Technicky protokol – lineární Pro předpověď počty |
| [řadový](/azure/machine-learning/studio-module-reference/ordinal-regression) | | | |0 |Pro předpověď pořadí řazení pořadí |
| **Detekce anomálií** | | | | | |
| [Podpora vektorového počítače](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) |○ |○ | |2 |Obzvláště dobrý pro velké sady funkcí |
| [Detekce anomálií založená na DPS](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) | |○ |● |3 | |
| [K-znamená](/azure/machine-learning/studio-module-reference/k-means-clustering) | |○ |● |4 |Algoritmus clusteringu |

**Vlastnosti algoritmu:**

**●** – zobrazuje skvělou přesnost, dobu rychlého školení a používání linearity.

**○** – zobrazí dobrou přesnost a střední dobu školení

## <a name="algorithm-notes"></a>Poznámky k algoritmům

### <a name="linear-regression"></a>Lineární regrese

Jak bylo zmíněno dříve, [lineární regrese](/azure/machine-learning/studio-module-reference/linear-regression) odpovídá datové sadě jako čára (nebo rovina neboli schéma). Je to WorkHorse, jednoduché a rychlé, ale může se stát, že se v některých problémech zjednodušený.

![Data s lineárním trendem](./media/algorithm-choice/image3.png)

***Data s lineárním trendem***

### <a name="logistic-regression"></a>Logistické regrese

I když obsahuje klíčové slovo "regrese" v názvu, je Logistická regrese vlastně výkonným nástrojem pro klasifikaci [dvou tříd](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) a více [tříd](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) . Je to rychlé a jednoduché. Fakt, že používá tvarovou křivku namísto rovné čáry, je přirozeným způsobem rozdělit data do skupin. Logistická regrese poskytuje lineární hranice tříd, takže když ji použijete, ujistěte se, že je lineární aproximace něco, co můžete živě využít.

![Logistická regrese pro data dvou tříd pomocí jediné funkce](./media/algorithm-choice/image4.png)

***Logistická regrese pro data dvou tříd s pouze jednou funkcí*** *– hranice třídy je bod, ve kterém je křivka logistická, stejně blízko obou tříd* .

### <a name="trees-forests-and-jungles"></a>Stromy, doménové struktury a džungle

Rozhodovací doménové struktury[(regrese](/azure/machine-learning/studio-module-reference/decision-forest-regression), [dvě třídy](/azure/machine-learning/studio-module-reference/two-class-decision-forest)a [třídy s více třídami](/azure/machine-learning/studio-module-reference/multiclass-decision-forest)), rozhodnutí džungle ([dvě třídy](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) [a více tříd)](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle)a posílené rozhodovací stromy ([regrese](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) a [dvě třídy](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree)) jsou všechny založené na rozhodovacích větvích, konceptu základu strojového učení. Existuje spousta variant rozhodovacích stromů, ale všechny mají stejnou věc, rozdělte prostor funkcí do oblastí s většinou stejný popisek. Můžou to být oblasti konzistentní kategorie nebo konstantní hodnoty v závislosti na tom, jestli provádíte klasifikaci nebo regresi.

![Rozhodovací strom rozděluje prostor funkcí.](./media/algorithm-choice/image5.png)

***Rozhodovací strom rozděluje prostor funkcí do oblastí zhruba stejnorodých hodnot.***

Vzhledem k tomu, že je možné prostor funkcí rozdělit do menších malých oblastí, je snadné ho rozdělit tak, aby měl jeden datový bod pro každou oblast. Toto je extrémní příklad přebudování. Aby k tomu nedocházelo, je velká sada stromů vytvořena se speciální matematický péčí pro zajištění, že stromy nejsou korelační. Průměrná hodnota této "rozhodovací doménové struktury" je strom, který brání přebudování. Rozhodovací doménové struktury můžou používat spoustu paměti. Rozhodnutí džungle jsou variantou, která spotřebovává méně paměti na úkor delší doby školení.

Zvýšení rozhodovacích stromů se vyhne přerozdělování tím, že omezuje počet, kolikrát je možné rozdělit a kolik datových bodů je v každé oblasti povolené. Algoritmus sestaví sekvenci stromů, z nichž každý se učí vykompenzovat chybu, která zůstala ve stromu před. Výsledkem je velmi přesný učí, který využívá spoustu paměti. Úplný technický popis najdete v části [původní papír pro Friedman](https://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Quantile regrese rychlé doménové struktury](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) je variací rozhodovacích stromů pro zvláštní případ, kdy chcete znát nejen typickou (medián) hodnotu dat v rámci oblasti, ale také její distribuci ve formě quantiles.

### <a name="neural-networks-and-perceptrons"></a>Neuronové sítě a perceptrons

Sítě neuronové jsou nechte inspirovat výukové algoritmy mozek-, které pokrývají problémy s více [třídami](/azure/machine-learning/studio-module-reference/multiclass-neural-network), [dvěma třídami](/azure/machine-learning/studio-module-reference/two-class-neural-network)a [regresi](/azure/machine-learning/studio-module-reference/neural-network-regression) . Přicházejí do nekonečné odrůdy, ale neuronové sítě v rámci Azure Machine Learning Studio (Classic) jsou všechny acyklickéhoy orientované grafy. To znamená, že vstupní funkce jsou před zapsáním výstupů předávány (nikdy zpětně) prostřednictvím sekvence vrstev. V každé vrstvě jsou vstupy vážené v různých kombinacích, sečtených a předaných do další vrstvy. Tato kombinace jednoduchých výpočtů má za následek možnost seznámit se s důmyslnými hranicemi tříd a trendy dat, zdánlivě Magic. Celá řada sítí tohoto řazení provede "obsáhlý Learning", který má spoustu technologických sestav a vědecký fiktivní.

Tento vysoký výkon se ale nedodává zdarma. Neuronové sítě může trvat dlouhou dobu, zejména u velkých datových sad s velkým množstvím funkcí. Mají také více parametrů než většina algoritmů, což znamená, že při vypisování parametrů se rozšíří doba školení o skvělou práci.
A u těch předaných, kteří chtějí [určit svou vlastní strukturu sítě](azure-ml-netsharp-reference-guide.md), se inexhaustible možnosti.

![Hranice zjištěné neuronové sítěmi](./media/algorithm-choice/image6.png)

***Hranice zjištěné neuronové sítěmi můžou být komplexní a nepravidelné.***

[Průměrná hodnota Perceptron](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) je neuronové sítě odpověď na skyrocketing školicí časy. Používá síťovou strukturu, která poskytuje lineární hranice tříd. Je skoro primitivní díky současným standardům, ale má dlouhou historii práce robustní a je dostatečně malá, aby se rychle seznámila.

### <a name="svms"></a>SVMs

Podpora vektorových počítačů (SVMs): vyhledá hranice, která odděluje třídy podle co nejširšího okraje. Když tyto dvě třídy nemůžete jasně oddělit, algoritmy vyhledají nejlepší hranici, kterou můžou. Jak je uvedeno v Azure Machine Learning Studio (Classic), [SVM v obou třídách](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) tento příkaz pouze s rovnou čárou (v SVM-Speak používá lineární jádro).
Vzhledem k tomu, že se tato lineární aproximace provede, může to být poměrně rychle. V takovém případě se jedná o data s důrazem na funkce, jako je například text nebo genomika dat. V těchto případech je SVMs možné oddělit třídy rychleji a s menším množstvím, než je většina ostatních algoritmů, a také vyžadovat pouze mírné množství paměti.

![Podpora hranice třídy počítače vektoru](./media/algorithm-choice/image7.png)

***Typická hranice třídy strojového počítače maximalizuje okraj, který odděluje dvě třídy.***

Dalším produktem Microsoft Research, který je [místně hlubokým SVMem ze dvou tříd](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) , je nelineární variantou SVM, která zachovává většinu rychlosti a efektivity paměti pro lineární verzi. Je ideální pro případy, kdy lineární přístup neposkytuje přesné odpovědi. Vývojáři ji rychle zapnuli tak, že problém rozdělíte na řadu malých lineárních problémů SVM. Přečtěte si [úplný popis](http://proceedings.mlr.press/v28/jose13.html) podrobností o tom, jak tyto štychy vyžádaly.

Pomocí rozšíření chytřejší nelineárního SVMs vykreslí [SVM jednu třídu](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) hranici, která úzce vyznačuje celou datovou sadu. Je vhodný pro detekci anomálií. Všechny nové datové body, které spadají mimo tuto hranici, jsou neobvykle dostatečně zajímavosti.

### <a name="bayesian-methods"></a>Metody bayesovského rozhodování

Bayesovského rozhodování metody mají vysoce žádoucí kvalitu: Vyhněte se jejich přebudování. Provádějí předem některé předpoklady o pravděpodobných distribucích odpovědi. Další Byproduct tohoto přístupu je, že mají velmi málo parametrů. Azure Machine Learning Studio (Classic) mají bayesovského rozhodování algoritmy pro obě klasifikace ([počítač se dvěma třídami Bayes "Point](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine)) a regrese ([bayesovského rozhodování lineární regrese](/azure/machine-learning/studio-module-reference/bayesian-linear-regression)).
Všimněte si, že se předpokládá, že data lze rozdělit nebo přizpůsobit rovnou spojnici.

Na historických poznámkách byly vyvíjené počítače s Bayes "Point-to-the Microsoft Research. Jsou na nich některé mimořádně atraktivní práce. Zúčastněný student se přesměruje na [původní článek v JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) a blogu přehledné, který je [Chris Bishop](https://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Specializované algoritmy
Pokud máte velmi konkrétní cíl, možná budete mít hodně. V rámci kolekce Azure Machine Learning Studio (Classic) existují algoritmy, které se specializují na:

- Předpověď pořadí ([ordinální regrese](/azure/machine-learning/studio-module-reference/ordinal-regression)),
- odhad počtu ([Poissonova regrese](/azure/machine-learning/studio-module-reference/poisson-regression)),
- detekce anomálií (jedna založená na [analýze hlavních komponent](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) a jedna na základě [podpory vektorových počítačů](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine))
- clusteringu ([K](/azure/machine-learning/studio-module-reference/k-means-clustering))

![Detekce anomálií založená na DPS](./media/algorithm-choice/image8.png)

***Detekce anomálií na základě DPS*** *– Velká většina dat spadá do stereotypical distribuce; body, které se výrazně liší od této distribuce, jsou podezřelé.*

![Sada dat seskupená pomocí K-znamená](./media/algorithm-choice/image9.png)

***Datová sada je seskupena do pěti clusterů pomocí K-znamená***

K dispozici je také objekt komplet s příponou [One-v – všechny třídy třídění s více třídami](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass), které přerušují problém klasifikace n-1 2 – problémy klasifikace třídy. Přesnost, čas školení a vlastnosti linearity se určují pomocí používaných klasifikátorů dvou tříd.

![Třídění dvou tříd kombinované za účelem vytvoření třídění tří tříd](./media/algorithm-choice/image10.png)

***Spojení dvou tříd třídění, aby tvořily třídění tří tříd***

Azure Machine Learning Studio (Classic) také zahrnuje přístup k výkonné architektuře strojového učení pod názvem [pro dostupné](/azure/machine-learning/studio-module-reference/train-vowpal-wabbit-version-7-4-model).
VW defies kategorizaci, protože se může naučit jak problémy s klasifikací, tak s regresí a můžou se i učit z částečně neoznačených dat. Můžete ji nakonfigurovat tak, aby používala některý z mnoha algoritmů učení, funkcí ztráty a algoritmů optimalizace. Byla navržena od základu až po efektivní, paralelní a extrémně rychlou. Zpracovává absurdně velké sady funkcí s málo zjevné úsilí.
VW je jeden záznam v poli algoritmů burzovních automobilů, který zahájila a vedla jako vlastní Jan Langford společnosti Microsoft Research. Ne všechny problémy zapadají do VW, ale pokud k tomu dochází, může to být tím, že se vám poučení výukové křivky na svém rozhraní. Je také k dispozici jako [samostatný open source kód](https://github.com/JohnLangford/vowpal_wabbit) v několika jazycích.

## <a name="next-steps"></a>Další kroky

* Informace o oblíbených algoritmech, které se používají k zodpovězení běžných otázek strojového učení, najdete v článku [základy strojového učení s příklady algoritmů](basics-infographic-with-algorithm-examples.md).

* Seznam podle kategorie všech algoritmů strojového učení, které jsou k dispozici v Machine Learning Studio (Classic), naleznete v tématu [Inicializace modelu](/azure/machine-learning/studio-module-reference/machine-learning-initialize-model) v algoritmu Machine Learning Studio (Classic) a v nápovědě k modulu.

* Úplný Abecední seznam algoritmů a modulů v Machine Learning Studio (Classic) najdete v tématu [seznam modulů Machine Learning Studio (Classic)](/azure/machine-learning/studio-module-reference/a-z-module-list) v Machine Learning Studio (klasickém) algoritmu a v nápovědě k modulu.
