---
title: Jak zvolit algoritmy strojového učení | Microsoft Docs
description: Jak zvolit algoritmy pro učení se supervizí a bez s Azure Machine Learning v clustering, klasifikační nebo regresní experimenty.
services: machine-learning
documentationcenter: ''
author: pakalra
ms.author: pakalra
manager: cgronlun
editor: cgronlun
tags: ''
ms.assetid: a3b23d7f-f083-49c4-b6b1-3911cd69f1b4
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/18/2017
ms.openlocfilehash: e1c7030a5f0c6e13653b302fcb48e7d4efa232c7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-choose-algorithms-for-microsoft-azure-machine-learning"></a>Jak vybrat algoritmy pro službu Microsoft Azure Machine Learning
Odpověď na otázku "Co algoritmus strojového učení mám použít?" je vždy "Závisí." To závisí na velikosti, kvality a povaha data. To závisí na co chcete udělat s odpověď. To závisí na tom, jak byl výpočty algoritmu přeložit na pokyny k počítači, který používáte. A závisí na tom, jak dlouho je k dispozici. I nejvíce zkušeného datových vědců nelze zjistit, který algoritmus provede než to zkusíte je nejvhodnější.

## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Algoritmus Machine Learning Tahák
**Microsoft Azure Machine Learning algoritmus cheaty list** pomáhá zvolíte právo počítač algoritmus učení pro vaše řešení prediktivní analýzy v knihovně Microsoft Azure Machine Learning algoritmů.
Tento článek vás provede způsobu jeho použití.

> [!NOTE]
> Ke stažení tahák a postupovat, přejděte na [strojového učení rychlý přehled algoritmů pro Microsoft Azure Machine Learning Studio](algorithm-cheat-sheet.md).
> 
> 

Tato tahák má velmi určitou cílovou skupinu v paměti: začátku dat vědecký pracovník s úrovni škole machine learning, která pokusu vybrat algoritmus začínat v Azure Machine Learning Studio. To znamená, že umožňuje některé Generalizace a oversimplifications, ale jeho bodů bezpečné směrem. Taky to znamená, že jsou velké množství algoritmy, které zde nejsou uvedeny. S růstem Azure Machine Learning zahrnuje více kompletní sadu dostupných metod přidáme je.

Tato doporučení jsou kompilované zpětné vazby a od mnoha datových vědců a odborníky machine learning. Všechno, co jsme nebyla souhlas, ale I jste se pokusili sladit naše názory do hrubý konsenzu. Většinu příkazů neshody začínat řetězcem "Závisí..."

### <a name="how-to-use-the-cheat-sheet"></a>Postup použití tahák
Číst cestu a algoritmus popisky v grafu jako "pro  *&lt;popisek cesty&gt;*, použijte  *&lt;algoritmus&gt;*." Například "pro *rychlost*, použijte *dvě třídy logistic regression*." Vztahuje se někdy více než jeden větev.
Někdy žádný z nich jsou vzájemně přizpůsobit. Jste má být pravidlo jezdec doporučení, proto nemusíte si dělat starosti o něm se přesný.
Několik datových vědců, které I jsme se věnovali měla s uvedené které pouze zda způsob, jak najít algoritmus velmi nejlepší je vyzkoušet všechny z nich.

Tady je příklad z [Azure AI Galerie](http://gallery.cortanaintelligence.com/) experimentu několik algoritmů proti stejná data se pokusí a porovná výsledky: [porovnat více třída třídění: písmeno rozpoznávání](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

> [!TIP]
> Pokud si chcete stáhnout a vytisknout diagram s přehledem funkcí nástroje Machine Learning Studio, nahlédněte do tématu [Diagram s přehledem možností nástroje Machine Learning Studio](studio-overview-diagram.md).
> 
> 

## <a name="flavors-of-machine-learning"></a>Charakteristikami machine learning
### <a name="supervised"></a>Pod dohledem.
Algoritmy pod dohledem učení provádět předpovědi na základě sady příklady. Například historických uložených ceny slouží k nebezpečí pokusů v budoucnu ceny. Každý příklad používá pro školení s názvem bez přípony s hodnotou zájmu – v tomto případě uložených cena. Algoritmus pod dohledem učení Hledat v těchto hodnota popisky. Může použít všechny informace, které by mohly být důležité – dne v týdnu, sezóny, finanční data společnosti, průmyslových, přítomnost rušivý geopolitické události – a jednotlivých algoritmů vypadá pro různé typy schémat. Jakmile algoritmus nejlepší vzorek najde ho můžete, použije tento vzor aby předpovědi bez popisku testovacích dat – budoucí ceny.

Pod dohledem learning je Oblíbené a užitečné typ strojové učení. S jednou výjimkou všechny moduly v Azure Machine Learning jsou pod dohledem učení algoritmy. Existuje několik typů konkrétní pod dohledem učení, které představuje v rámci Azure Machine Learning: detekce anomálií, klasifikace a regrese.

* **Klasifikace**. Pokud data se používají k předvídání kategorii, pod dohledem učení se také nazývá klasifikace. To je případ, kdy přiřazení bitovou kopii jako obrázek 'cat' nebo 'PSA. Pokud jsou dostupné pouze dvě možnosti, se nazývá **two-class** nebo **binomický klasifikace**. Když existují další kategorie, jako, předpověď Vítěz turnaj Madness března NCAA, potíže se označuje jako **více třída klasifikace**.
* **Regrese**. Pokud hodnota je právě předpovědět, stejně jako u uložených ceny, se nazývá pod dohledem learning regrese.
* **Detekce anomálií**. Cílem je někdy k identifikaci datové body, které jsou jednoduše neobvyklé. Při zjišťování podvodů například všechny vzorky útraty hodně neobvyklý platební karty se podezřelá. Je možné variace tak mnoho a vypadá tak několik, že není možné zjistěte, jaké podvodné aktivity školení příklady. Přístup, který přebírá detekce anomálií je jednoduše zjistěte, jaké normální aktivity bude vypadat takto (pomocí-podvodné transakce historie) a identifikovat všechno, co se výrazně liší.

### <a name="unsupervised"></a>Bez dohledu
V bez dohledu learning datové body mají žádné popisky s nimi spojených. Cílem algoritmus učení bez dohledu místo toho je k uspořádání dat nějakým způsobem nebo k popisu jeho struktury. To může znamenat seskupení do clusterů nebo při hledání různé způsoby prohlížení komplexní data tak, aby se jednodušší nebo více uspořádány.

### <a name="reinforcement-learning"></a>Posílení učení
V posílení učení získá algoritmus vybrat akci v reakci na každý datový bod. Algoritmus učení dále přijímá signál potřebu po krátkou dobu později, znamenající, jak dobrý byla rozhodnutí.
Na základě algoritmus upravuje jeho strategie pro dosažení nejvyšší potřebu. Aktuálně neexistují žádné posílení učení algoritmu modulů v Azure Machine Learning. Posílení learning je běžné v robotics, kde sada odečty snímačů v jednom bodě v čase je hodnota datového bodu, a algoritmus musíte zvolit další akce robot. Je také, že přírodní vhodné pro Internet věcí aplikace.

## <a name="considerations-when-choosing-an-algorithm"></a>Důležité informace k výběru algoritmus
### <a name="accuracy"></a>Přesnost
Získávání nejpřesnější odpovědí možná není vždy nutné.
Někdy je dostačující, v závislosti na tom, co chcete použít pro sblížení. Pokud je to tento případ, bude pravděpodobně možné vyjmout doby zpracování výrazně podle provedením vykrvovacího vpichu s další přibližnou metody. Další výhodou další přibližnou metody je, že se přirozeně mívají předejdete [overfitting](https://youtu.be/DQWI1kvmwRg).

### <a name="training-time"></a>Čas školení
Počet minut nebo hodin, které jsou nezbytné pro trénování modelu značnou část liší algoritmy. Cvičení čas často úzce souvisí přesnost – jeden na druhý obvykle doprovází. Kromě toho některé algoritmy jsou citlivější na počet datových bodů než jiné.
Po omezenou dobu je jednotka vybrat algoritmus, zejména v případě, že je velké datové sady.

### <a name="linearity"></a>Linearity
Ujistěte se, velké množství algoritmy strojového učení použít linearity. Lineární klasifikace algoritmy předpokládá, že třídy je možné oddělit přímku (nebo její vyšší dimenzí analogovým). Patří logistic regression a podporují vektoru počítače (jak je implementované v Azure Machine Learning).
Lineární regrese algoritmy předpokládají, podívejte se na data trendy přímce. Tyto předpoklady nejsou chybný pro některé problémy, ale na jiných přinášejí přesnost.

![Hranice non lineární – třída][1]

***Lineární bez třída hranic*** *-spoléhat na lineární klasifikační algoritmus by mělo za následek nízkou přesnost*

![Data s nelineární trendu][2]

***Data s nelineární trend*** *-pomocí jiné metody lineární regrese by vygeneroval mnohem větší chyby, než je nutné*

Bez ohledu na jejich nebezpečích lineární algoritmy jsou velmi oblíbených jako první řádek útoku. Že jsou obvykle algorithmically jednoduchým a rychlým ke cvičení.

### <a name="number-of-parameters"></a>Počet parametrů
Parametry jsou knoflíky, který získá vědecký pracovník dat. Chcete-li při nastavování algoritmu. Jsou čísla, která ovlivňují chování algoritmus, například tolerance chyb nebo počet iterací nebo možnosti mezi varianty chování algoritmus. Přesnost algoritmu a čas školení může být někdy velmi citlivé na získávání právě správné nastavení. Algoritmy s parametry velké počty obvykle vyžadují většina chyba zkušební verze a najít dobrou kombinaci.

Případně, není [parametr (vymetání) komínů](algorithm-parameters-optimize.md) bloku modulu v Azure Machine Learning, která automaticky se pokusí všechny kombinace parametrů v jakémkoli členitosti zvolíte. Přestože se skvělý způsob, jak Ujistěte se, že jste předané parametr místa, zvyšuje čas potřebný k natrénování modelu exponenciálnímu s počtem parametrů.

Vzhůru je, že má mnoho parametrů obvykle znamená, že algoritmus má větší flexibilitu. Často může dosáhnout velmi dobré přesnost. Pokud že najdete tu správnou kombinaci nastavení parametrů.

### <a name="number-of-features"></a>Počet funkcí
Pro některé typy dat, řadu funkcí, může být velmi velké ve srovnání s počet datových bodů. To je často případ s genetika nebo textová data. Hodně funkcí můžete bog dolů některé learning algoritmy, provedení školení čas unfeasibly dlouho. Podpora vektoru počítače jsou zvláště skvěle hodí pro tento případ (viz níže).

### <a name="special-cases"></a>Zvláštní případy
Některé algoritmy učení zkontrolujte určité předpoklady o struktuře data nebo požadovaných výsledků. Pokud můžete najít ten, který vyhovuje vašim potřebám, se vám může poskytnout užitečnější výsledky, přesnější předpovědi nebo školení rychlejší.

| **Algoritmus** | **Přesnost** | **Čas školení** | **Linearity** | **Parametry** | **Poznámky** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Klasifikace dva – třída** | | | | | |
| [Logistic regression](https://msdn.microsoft.com/library/azure/dn905994.aspx) | |● |● |5 | |
| [rozhodnutí doménové struktury](https://msdn.microsoft.com/library/azure/dn906008.aspx) |● |○ | |6 | |
| [rozhodovací Džungle](https://msdn.microsoft.com/library/azure/dn905976.aspx) |● |○ | |6 |Nároky nedostatku paměti |
| [Vylepšené rozhodovací strom](https://msdn.microsoft.com/library/azure/dn906025.aspx) |● |○ | |6 |Nároky velké paměti |
| [neuronové sítě](https://msdn.microsoft.com/library/azure/dn905947.aspx) |● | | |9 |[Je možné další přizpůsobení](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [zprůměrovanou perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) |○ |○ |● |4 | |
| [Support vector machine](https://msdn.microsoft.com/library/azure/dn905835.aspx) | |○ |● |5 |Dobré pro velké funkce sady |
| [počítač vektoru místně přímé podpory](https://msdn.microsoft.com/library/azure/dn913070.aspx) |○ | | |8 |Dobré pro velké funkce sady |
| [Počítač bodu Bayes.](https://msdn.microsoft.com/library/azure/dn905930.aspx) | |○ |● |3 | |
| **Klasifikace více – třída** | | | | | |
| [Logistic regression](https://msdn.microsoft.com/library/azure/dn905853.aspx) | |● |● |5 | |
| [rozhodnutí doménové struktury](https://msdn.microsoft.com/library/azure/dn906015.aspx) |● |○ | |6 | |
| [rozhodovací Džungle ](https://msdn.microsoft.com/library/azure/dn905963.aspx) |● |○ | |6 |Nároky nedostatku paměti |
| [neuronové sítě](https://msdn.microsoft.com/library/azure/dn906030.aspx) |● | | |9 |[Je možné další přizpůsobení](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [one-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx) |- |- |- |- |Informace naleznete ve vlastnostech vybrané metody dva – třída |
| **Regrese** | | | | | |
| [linear](https://msdn.microsoft.com/library/azure/dn905978.aspx) | |● |● |4 | |
| [Bayesova lineární](https://msdn.microsoft.com/library/azure/dn906022.aspx) | |○ |● |2 | |
| [rozhodnutí doménové struktury](https://msdn.microsoft.com/library/azure/dn905862.aspx) |● |○ | |6 | |
| [Vylepšené rozhodovací strom](https://msdn.microsoft.com/library/azure/dn905801.aspx) |● |○ | |5 |Nároky velké paměti |
| [quantile rychlé doménové struktury](https://msdn.microsoft.com/library/azure/dn913093.aspx) |● |○ | |9 |Distribuce spíše než předpovědi bodu |
| [neuronové sítě](https://msdn.microsoft.com/library/azure/dn905924.aspx) |● | | |9 |[Je možné další přizpůsobení](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [Poissonovo rozdělení](https://msdn.microsoft.com/library/azure/dn905988.aspx) | | |● |5 |Technicky protokolu – lineární. Pro predikci počty |
| [pořadí](https://msdn.microsoft.com/library/azure/dn906029.aspx) | | | |0 |Pro predikci pořadí řazení |
| **Detekce anomálií** | | | | | |
| [Support vector machine](https://msdn.microsoft.com/library/azure/dn913103.aspx) |○ |○ | |2 |Zvlášť vhodné pro velké funkce sady |
| [Detekce anomálií založený na PCA](https://msdn.microsoft.com/library/azure/dn913102.aspx) | |○ |● |3 | |
| [K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/) | |○ |● |4 |Clustering algoritmus |

**Vlastnosti algoritmu:**

**●** -zobrazuje vynikající přesnost, časy rychlé školení a použití linearity

**○** -ukazuje dobrý přesnost a časy, střední školení

## <a name="algorithm-notes"></a>Algoritmus poznámky
### <a name="linear-regression"></a>Lineární regrese
Jak je uvedeno nahoře, [lineární regrese](https://msdn.microsoft.com/library/azure/dn905978.aspx) zapadá řádek (nebo rovině nebo hyperplane) a datové sady. Je centrem, jednoduchým a rychlým, ale může být příliš zneužívající vlastností prohlížeče pro některé problémy.
Zde můžete zkontrolovat [lineární regrese kurzu](linear-regression-in-azure.md).

![Data s lineární trend][3]

***Data s lineární trend***

### <a name="logistic-regression"></a>Logistic regression
I když confusingly obsahuje "regrese" v názvu se logistic regression je ve skutečnosti výkonný nástroj pro [two-class](https://msdn.microsoft.com/library/azure/dn905994.aspx) a [multiclass](https://msdn.microsoft.com/library/azure/dn905853.aspx) klasifikace. Je rychlé a jednoduché. Fakt, který používá k na '-tvarované křivky místo přímku umožňuje přirozené přizpůsobit pro dělení dat do skupin. Hranice lineární třída poskytuje logistic regression, tak při použití, ujistěte se, že lineární aproximace je něco, co můžete za provozu s.

![Logistic regression k datům dvě třídy s jedním funkcí][4]

***Logistic regression k datům dvě třídy s jedním funkcí*** *-bod, ve kterém logistic křivka je právě co nejblíže obě třídy je hranicí – třída*

### <a name="trees-forests-and-jungles"></a>Džungle, stromy a doménové struktury
Decision doménovými strukturami ([regrese](https://msdn.microsoft.com/library/azure/dn905862.aspx), [two-class](https://msdn.microsoft.com/library/azure/dn906008.aspx), a [multiclass](https://msdn.microsoft.com/library/azure/dn906015.aspx)), decision Džungle ([two-class](https://msdn.microsoft.com/library/azure/dn905976.aspx) a [ multiclass](https://msdn.microsoft.com/library/azure/dn905963.aspx)) a boosted decision trees ([regrese](https://msdn.microsoft.com/library/azure/dn905801.aspx) a [two-class](https://msdn.microsoft.com/library/azure/dn906025.aspx)) jsou všechny na základě rozhodovacích stromů, základní strojového učení koncept. Existuje mnoho variant rozhodovací stromy, ale všechny stejnou věc udělat – místo funkce rozdělte oblasti s většinou stejný popisek. To mohou být oblasti konzistentní kategorie nebo konstantní hodnoty, v závislosti na tom, jestli dělají klasifikační nebo regresní.

![Rozhodovací strom rozděluje prostor funkce][5]

***Rozhodovací strom rozděluje prostor funkce do oblasti hodnot zhruba uniform***

Protože funkce místa lze rozdělit na nahodile malé oblasti, je snadné Představte si dělení dostatečně podrobně tak, aby měl jeden datový bod každou oblast. Toto je příklad extrémně overfitting. Chcete-li předejít, jsou velké sady stromy zkonstruovat s zvláštní pozornost matematickém prováděné, že nejsou korelační stromy. Průměr této "rozhodnutí doménové struktury" je stromové struktury, která zabraňuje overfitting. Rozhodnutí doménové struktury mohou pomocí velké množství paměti. Rozhodovací Džungle jsou hodnotu typu variant, která využívá méně paměti za cenu mírně delší dobu školení.

Vylepšené rozhodovací stromy vyhnout overfitting omezením kolikrát se můžete rozdělit a jak několik datových bodů jsou povoleny v každé oblasti. Algoritmus vytvoří posloupnost stromy, z nichž každý se učí kompenzovat chyby zanechaný stromu před. Výsledkem je velmi přesná student, která se obvykle používají velké množství paměti. Úplná technická popis, podívejte se na [původního dokumentu na Friedman](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Rychlý doménové struktury quantile regrese](https://msdn.microsoft.com/library/azure/dn913093.aspx) se odlišuje od rozhodovací stromy pro zvláštní případ, kam chcete vědět, ne jenom typické (střední) hodnotu dat v rámci oblasti, ale také jeho distribuci ve formě quantiles.

### <a name="neural-networks-and-perceptrons"></a>Neuronové sítě a perceptrons
Neuronové sítě jsou mozku inspirovaných učení algoritmy pokrývajících [multiclass](https://msdn.microsoft.com/library/azure/dn906030.aspx), [two-class](https://msdn.microsoft.com/library/azure/dn905947.aspx), a [regrese](https://msdn.microsoft.com/library/azure/dn905924.aspx) problémy. Se mohou nekonečné různé, ale neuronové sítě v rámci Azure Machine Learning jsou všechny formu směrovanou necyklické grafy. Která znamená, že vstupní funkce jsou předány dál (nikdy zpátky) prostřednictvím pořadí vrstev před se převedena na výstupy. V každé vrstvě jsou vstupy vážené v různých kombinacích, sčítají a k předání další vrstva. Tato kombinace jednoduché výpočty výsledkem možnost zdánlivě další sofistikované třída hranice a data trendy ve magic. Vrstvený mnoho sítí toto řazení provést "hloubkové studiu" paliva mnoho reporting technické a vědecké účely fiction.

Tento vysoký výkon nepřejde do stavu zdarma, ale. Neuronové sítě může trvat dlouhou dobu cvičení, hlavně pro velké sady dat s mnoha funkcí. Mají také další parametry než většina algoritmů, které znamená parametr (vymetání) komínů značnou část rozšíří čas školení.
A pro tyto overachievers, kteří chtějí [zadat své vlastní struktury sítě](http://go.microsoft.com/fwlink/?LinkId=402867), možnosti jsou inexhaustible.

![Hranice poznat neuronové sítě][6]
***hranice poznat neuronové sítě může být složité a nestandardní***

[Two-class průměrem perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) je neuronové sítě odpovědí skyrocketing časy školení. Využívá strukturu sítě, která poskytuje lineární třída hranice. Je téměř primitivní standardy dnešní, ale má dlouhou práce robustní a je dostatečně malé, aby další rychle.

### <a name="svms"></a>SVMs
Podpora vektoru počítače (SVMs) najít hranice, která odděluje třídy podle jako celý okraj nejblíže. Při dvou tříd nelze jasně oddělené, algoritmy najít nejlepší hranice, na které může provádět tyto akce. Zapsaný v Azure Machine Learning [two-class SVM](https://msdn.microsoft.com/library/azure/dn905835.aspx) tomu s pouze přímce. (V řeči SVM použije lineární jádra.) Vzhledem k tomu, že umožňuje tento lineární aproximace, je možné velmi rychlé. Kde je skutečně září je s daty bohaté funkce, jako text nebo genomických. V těchto případech SVMs dokážou k oddělení tříd rychleji a s menší overfitting než většinu dalších algoritmů, kromě nutnosti pouze mírné množství paměti.

![Hranice podpory vector machine – třída][7]

***Okraj oddělujících dvě třídy maximalizuje hranici typické podporu počítač vector – třída***

Jiného produktu Microsoft Research [two-class místně hloubkové SVM](https://msdn.microsoft.com/library/azure/dn913070.aspx) je – lineární varianta SVM, který bude mít většinu rychlost a paměť efektivitu lineární verze. Je ideální pro případy, kdy lineární přístup nedává dostatečně přesné odpovědi. Vývojáři je udržováno rychlého rozdělením problém do spoustu malé lineární SVM potíže. Pro čtení [úplný popis](http://proceedings.mlr.press/v28/jose13.html) podrobnosti o tom, jak se vyžádat vypnout platí to.

Pomocí inteligentní rozšíření nelineární SVMs [SVM jedna třída](https://msdn.microsoft.com/library/azure/dn913103.aspx) nevykresluje hranici, která úzce popisuje celé datové sady. Je užitečné pro zjišťování anomálií. Všechny nové datové body, které daleko spadal mimo tuto hranici neobvyklá dostatečně být pozoruhodné.

### <a name="bayesian-methods"></a>Bayesova metody
Metody Bayesova mají vysokou žádoucí kvality: vyhnou overfitting. Je to tím, že některé předpoklady předem o pravděpodobně distribuci odpovědi. Jiné byproduct tohoto přístupu je, ke kterým mají velmi málo parametrů. Azure Machine Learning má i Bayesova algoritmy pro obě klasifikaci ([Two-class Bayes point machine](https://msdn.microsoft.com/library/azure/dn905930.aspx)) a regrese ([lineární regrese Bayesova](https://msdn.microsoft.com/library/azure/dn906022.aspx)).
Všimněte si, že tyto předpokládá, že můžete data rozdělení nebo přizpůsobit s přímce.

Na historické Poznámka byly počítače bodu se Bayes vyvinuté v Microsoft Research. Mají některé výjimečně Krásný teoretické pracovní za ně. Dotčené student se přesměruje [původní článek v JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) a [pronikavého blog podle Jan Bishop](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Specializované algoritmy
Pokud máte velmi konkrétní cílem může být jednoduché. V kolekci Azure Machine Learning jsou algoritmy, které se specializují na:

- RANK předpovědi ([pořadí regrese](https://msdn.microsoft.com/library/azure/dn906029.aspx)),
- počet předpovědi ([Poissonovo rozdělení regrese](https://msdn.microsoft.com/library/azure/dn905988.aspx)),
- detekce anomálií (jeden na základě [hlavní součásti analysis](https://msdn.microsoft.com/library/azure/dn913102.aspx) a jeden na základě [support vector machine](https://msdn.microsoft.com/library/azure/dn913103.aspx)s)
- Clustering ([K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/))

![Detekce anomálií založený na PCA][8]

***Detekce anomálií založený na PCA*** *-velká většina dat, které patří do stereotypical distribuční; výrazně odchylují od tohoto distribuční body jsou podezřelé*

![Datové sady, které jsou seskupeny pomocí K-means][9]

***Datové sady jsou rozděleny do pěti clustery pomocí K-means***

Je také kompletu [více třídami třídění one-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx), která dělí klasifikace problému N třída do N-1 two-class klasifikaci problémy. Přesnost, čas školení a linearity vlastnosti jsou určeny two-class třídění použít.

![Dvě třídy třídění spolu tvoří tři třídy třídění][10]

***Pár two-class třídění se kombinují k formuláři třídění tři – třída***

Azure Machine Learning obsahuje také přístup k rozhraní výkonné strojové učení pod názvem [Vowpal k dispozici](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf).
Zobrazit prosíme, abyste kategorizaci tady, protože další klasifikace a regrese problémy a i další z částečně bez popisku data. Můžete nakonfigurovat na používání některého počtu učení algoritmy, ztrátě funkce a algoritmy optimalizace. Byl navržen od základů si být efektivní, paralelní a velmi rychlé. Zpracovává ridiculously velké funkce sady s malým množstvím zřejmá úsilí.
Spuštění a vedla podle Microsoft Research na vlastní Jan Langford, je zobrazit vzorec jednu položku v poli stock car algoritmů. Ne každý problém vyhovuje zobrazit, ale bezdrátové sítě může být vhodné vaší dobu vylézt křivku na jeho rozhraní. Je také k dispozici jako [samostatné otevřít zdrojový kód](https://github.com/JohnLangford/vowpal_wabbit) v několika jazycích.

## <a name="more-help-with-algorithms"></a>Další pomoc s algoritmy
* Infografice ke stažení, který popisuje algoritmy a obsahuje příklady, najdete v části [ke stažení Infografice: strojového učení základy s příklady algoritmus](basics-infographic-with-algorithm-examples.md).
* Seznam podle kategorie všechny algoritmy strojového učení dostupné v Azure Machine Learning Studio najdete v tématu [inicializovat Model] [ initialize-model] v Machine Learning Studio algoritmus a pomáhají modulu.
* Dokončení abecední seznam algoritmů a modulů v Azure Machine Learning Studio najdete v tématu [seznam A-Z modulů Machine Learning Studio] [ a-z-list] v Machine Learning Studio algoritmus a pomoci modulu.
* Stáhnout a vytisknout diagram, který poskytne přehled funkcí Azure Machine Learning Studio najdete v tématu [diagram s přehledem funkcí Azure Machine Learning Studio](studio-overview-diagram.md).


<!-- Reference links -->
[initialize-model]: https://msdn.microsoft.com/library/azure/dn905812.aspx
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx

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
