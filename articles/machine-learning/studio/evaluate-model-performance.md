---
title: Vyhodnocení výkonu modelu - Azure Machine Learning Studio | Dokumentace Microsoftu
description: Tento článek ukazuje, jak vyhodnotit výkon modelu v nástroji Azure Machine Learning Studio a poskytuje stručný dostupné metriky pro tuto úlohu.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 5dc5348a-4488-4536-99eb-ff105be9b160
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 501a9834e598fc8b1c11a86ef0ae9db1c19a66a7
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269936"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning-studio"></a>Jak vyhodnotit výkon modelu ve službě Azure Machine Learning Studio

Tento článek ukazuje, jak vyhodnotit výkon modelu v nástroji Azure Machine Learning Studio a poskytuje stručný dostupné metriky pro tuto úlohu. Jsou uvedeny tři běžné scénáře učení: 

* Regrese
* Binární klasifikace 
* Klasifikace víc tříd



Vyhodnocení výkonu modelu je jednou z fází jader v vědecké zpracování dat. Označuje s jak velkým bodování (předpovědí) datové sady se podle trénovaného modelu. 

Služba Azure Machine Learning podporuje vyhodnocení modelu projdeme dvě z jeho hlavní strojového učení moduly: [Vyhodnocení modelu] [ evaluate-model] a [Cross-Validate modelu][cross-validate-model]. Tyto moduly vám umožňují zobrazit, jak provádí modelu z hlediska počtu metriky, které se běžně používají v machine learning a statistické údaje.

## <a name="evaluation-vs-cross-validation"></a>Vyhodnocení vs. Křížové ověření
Vyhodnocení a křížového ověření jsou standardní způsoby, jak měřit výkon modelu. Oba generovat metrik, které můžete kontrolovat nebo porovnání u dalších modelů.

[Vyhodnocení modelu] [ evaluate-model] očekává Vyhodnocená datovou sadu jako vstup (nebo v případě byste chtěli porovnat výkon jednotlivých dva různé modely 2). To znamená, že je potřeba trénování modelu pomocí [trénování modelu] [ train-model] modulu a zkontrolujte předpovědí na některé datové sadě pomocí [Score Model] [ score-model]modulu, než můžete vyhodnotit výsledky. Hodnocení je založeno na skóre popisky/pravděpodobnosti spolu s true popisky, které jsou výstupem [Score Model] [ score-model] modulu.

Alternativně můžete křížového ověření provést několik trénování skóre vyhodnotit operací (10 složení) automaticky na různé podmnožiny vstupní data. Vstupní data je rozdělit na části 10, kde jedna je vyhrazena pro testování, a další 9 pro vzdělávání. Tento postup se opakuje 10krát a metrik hodnocení zprůměrovány. To pomáhá určit, jak dobře model by generalize do nové datové sady. [Cross-Validate modelu] [ cross-validate-model] modul přijímá Nezkušený modelu a některé datové sady s popiskem a vypíše výsledky vyhodnocení jednotlivých 10 složení, kromě průměrné výsledky.

V následujících částech budeme vytvářet jednoduché regrese a klasifikace modely a vyhodnotit jejich výkon pomocí obou [Evaluate Model] [ evaluate-model] a [Cross-Validate modelu ] [ cross-validate-model] moduly.

## <a name="evaluating-a-regression-model"></a>Vyhodnocení regresní Model
Předpokládejme, že chceme předpovědět cenu automobilu, používáním některých funkcí, jako je například dimenze, výkon, specifikace modul a tak dále. Toto je typická regresní problém, kdy Cílová proměnná (*cena*) průběžné číselná hodnota. Jsme můžete přizpůsobit model lineární regrese jednoduché daným, funkce hodnoty určitých automobilu, můžete odhadnout cenu této automobilu. Tento regresní model lze použít ke stanovení skóre, které jsme školili na stejné datové sady. Jakmile budeme mít předpokládané ceny platné pro všechny automobilů, jsme pohledem na tom, kolik předpovědi odchylují od skutečné ceny v průměru vyhodnocení výkonu modelu. Pro znázornění, používáme *Automobile price data (Raw) dataset* k dispozici v **uložení datové sady** části v Azure Machine Learning Studio.

### <a name="creating-the-experiment"></a>Vytvoření experimentu
Přidejte následující moduly do pracovního prostoru Azure Machine Learning Studio:

* Automobilů price data (Raw)
* [Lineární regrese][linear-regression]
* [Trénování modelu][train-model]
* [Určení skóre modelu][score-model]
* [Vyhodnocení modelu][evaluate-model]

Připojení portů, jak je znázorněno níže na obrázku 1 a nastavte sloupec popisek [Train Model] [ train-model] modulu *cena*.

![Vyhodnocení regresní Model](./media/evaluate-model-performance/1.png)

Obrázek 1. Vyhodnocení regresní Model.

### <a name="inspecting-the-evaluation-results"></a>Kontrola výsledků vyhodnocení
Po spuštění testu, můžete kliknout na výstupní port modulu [Evaluate Model] [ evaluate-model] modul a vyberte *vizualizovat* zobrazíte výsledky hodnocení. Jsou k dispozici pro regresní modely hodnocení metriky: *Střední absolutní chyba*, *Root střední absolutní chyba*, *relativní absolutní chyba*, *relativní spolehlivosti chyba*a *koeficient Stanovení*.

Termín "Chyba" představuje rozdíl mezi předpovězenou a skutečnou hodnotou. Absolutní hodnota nebo druhou mocninu tohoto rozdílu je obvykle vypočtenou hodnotu capture celková velikost chyby napříč všemi instancemi jako rozdíl mezi hodnotou předpokládané a true může být v některých případech záporná. Chybové metriky měření výkonu prediktivních regresní model z hlediska odchylku střední jeho predikcím ze skutečné hodnoty. Nižší hodnoty chyba bude model přesnější při vytváření předpovědí. Celková metrika chyba nula znamená, že model odpovídá zpracovávaným datům dokonale.

Koeficient rozhodnutí, která se také označuje jako R spolehlivosti, je také standardní způsob měření, jak dobře model odpovídá zpracovávaným datům. Může být interpretován jako podíl varianta je vysvětleno v modelu. Vyšší poměr v tomto případě je lepší kde 1 označuje ideální řešení.

![Lineární regrese metrik](./media/evaluate-model-performance/2.png)

Obrázek 2. Lineární regrese metrik.

### <a name="using-cross-validation"></a>Použití křížového ověření
Jak už bylo zmíněno dříve, můžete provést opakovaný školení, vyhodnocení a hodnocení automaticky pomocí [Cross-Validate modelu] [ cross-validate-model] modulu. V takovém případě stačí datové sady, model Nezkušený a [Cross-Validate modelu] [ cross-validate-model] modulu (viz obrázek níže). Je nutné nastavit sloupci popisek *cena* v [Cross-Validate modelu] [ cross-validate-model] vlastnosti modulu.

![Křížové ověření regresní Model](./media/evaluate-model-performance/3.png)

Obrázek 3. Křížové ověření regresní Model.

Po spuštění experimentu si můžete prohlédnout výsledky vyhodnocení po kliknutí na správný výstupní port modulu [Cross-Validate modelu] [ cross-validate-model] modulu. To poskytne podrobné zobrazení metrik pro každou iteraci (fáze) a průměrné výsledky jednotlivých metrik (obrázek 4).

![Regresní Model výsledků křížového ověřování](./media/evaluate-model-performance/4.png)

Obrázek 4. Cross-Validation výsledky regresní Model.

## <a name="evaluating-a-binary-classification-model"></a>Vyhodnocení binární klasifikační Model
Ve scénáři binární klasifikace Cílová proměnná obsahuje pouze dva možné výsledky, třeba: {0, 1} nebo {false, true}, {záporný, kladné}. Předpokládejme, máte datovou sadu pro dospělé zaměstnanci s některými demografických a zaměstnání proměnné a zobrazí se výzva k předpovědi úroveň příjmů, binární proměnnou s hodnotami {"< = 50 tis.", "> 50 tis."}. Jinými slovy záporná třída reprezentuje zaměstnanci, kteří žádají menší než nebo rovna 50 tisíc za rok a kladné třída reprezentuje všechny ostatní zaměstnanci. Jako ve scénáři regrese jsme by trénování modelu, stanovení skóre nějaká data a vyhodnoťte výsledky. Hlavní rozdíl je volba metriky, které vypočítá Azure Machine Learning a výstupy. Pro ilustraci úroveň predikce scénář příjmu, budeme používat [dospělé](http://archive.ics.uci.edu/ml/datasets/Adult) datovou sadu, která vytvoření experimentu Azure Machine Learning a vyhodnotit výkon modelu logistické regrese dvěma třídami, běžně používané binární soubor třídění.

### <a name="creating-the-experiment"></a>Vytvoření experimentu
Přidejte následující moduly do pracovního prostoru Azure Machine Learning Studio:

* Datová sada pro dospělé binární klasifikace příjmů v sčítání
* [Logistické regrese Two-Class][two-class-logistic-regression]
* [Trénování modelu][train-model]
* [Určení skóre modelu][score-model]
* [Vyhodnocení modelu][evaluate-model]

Připojení portů, jak je znázorněno níže na obrázku 5 a nastavte sloupec popisek [Train Model] [ train-model] modulu *příjmu*.

![Vyhodnocení binární klasifikační Model](./media/evaluate-model-performance/5.png)

Obrázek 5. Vyhodnocení binární klasifikační Model.

### <a name="inspecting-the-evaluation-results"></a>Kontrola výsledků vyhodnocení
Po spuštění testu, můžete kliknout na výstupní port modulu [Evaluate Model] [ evaluate-model] modul a vyberte *vizualizovat* zobrazíte výsledky vyhodnocení (obrázek 7). Jsou k dispozici pro binární klasifikační modely hodnocení metriky: *Přesnost*, *přesnost*, *odvolat*, *F1 skóre*, a *AUC*. Kromě toho modul výstupy chybovou matici zobrazující počet pravdivě pozitivní, falešně negativní, počet falešně pozitivních výsledků a negativní hodnotu true, stejně jako *roc s více TŘÍDAMI*, *přesnost/spojené s vracením*, a  *Zvedněte* křivky.

Přesnost je jednoduše podíl správně klasifikované instancí. Je to obvykle první metriku, kterou se podíváte na při vyhodnocování třídění. Nicméně, pokud je testovací data nevyvážené uvozovky (kde většina instancí patří do jedné ze tříd), nebo vás zajímá více výkonu na jednu z tříd, přesnost nezachytí skutečně efektivitu třídění. Ve scénáři úrovně klasifikace příjmů se předpokládá, že testujete na nějaká data, kde 99 % instancí představují uživatelů, kteří získají menší než nebo rovna 50 tisíc za rok. Je možné dosáhnout 0.99 přesnost predikce třídy "< = 50 tis." pro všechny instance. Třídění v tomto případě se zobrazí až po být provádění výborně celkové, ale ve skutečnosti selže některý vysokými příjmy jednotlivce (1 %) správně klasifikovat.

Z tohoto důvodu je užitečné vypočítat další metriky, které zachycení více specifické aspekty hodnocení. Před přechodem k podrobnostem tyto metriky, je důležité pochopit, chybová matice vyhodnocení binární klasifikace. Popisky třídy v sadě školení může trvat pouze 2 možných hodnot, které se obvykle označují jako kladné nebo záporné. Kladné a záporné instancí, které klasifikátor předpovídá správně se nazývají (TP) pravdivě pozitivní a negativní hodnotu true (TN). Nesprávně klasifikované instance podobně, jsou označovány jako falešně pozitivních výsledků (FP) a falešně negativní (FN). Chybová matice je jednoduše tabulka znázorňující počet instancí, které spadají pod každým z následujících 4 kategorií. Azure Machine Learning automaticky rozhodne, která dvou tříd v datové sadě je pozitivní třídy. Pokud třída popisky jsou logické hodnoty nebo celá čísla, jsou přiřazeny instance s popiskem "true" nebo "1" pozitivní třídy. Pokud popisky jsou řetězce, jako v případě datová sada příjem popisky jsou seřazená podle abecedy a být záporné třídu druhou úroveň při pozitivní třídy je vybrána první úroveň.

![Binární klasifikace chybová matice](./media/evaluate-model-performance/6a.png)

Obrázek 6. Binární klasifikace chybová matice.

Když se vrátíme k příjmu klasifikace problému, jsme byste měli zadat několik otázek hodnocení, které nám pomáhají porozumět výkonu tohoto třídění použít. Velmi přirozené otázkou je: "Z osob, kterým modelu očekává se vydělávat > 50 TIS (TP + FP), kolik se správnou klasifikaci (TP)?" Tento dotaz může zodpovědět pohledem **přesnost** modelu, který je poměr pozitivní nálezy, které mají správnou klasifikaci: TP/(TP+FP). Další běžné otázkou je "mimo všechny vysoce zvyšovat své zaměstnance s příjmem > 50 tis. (TP + FN), kolik třídění klasifikovat správně (TP)". Toto je ve skutečnosti **odvolat**, nebo true kladné frekvence: TP/(TP+FN) třídění. Můžete si všimnout, že je zřejmé kompromis mezi přesnosti a odvolání. Například poměrně vyvážené datové sady, třídění, který bude předpovídat většinou kladné instancí, by měli vysokou odvolání, ale spíš málo přesnost tolik negativní instance by misclassified což vede k velký počet falešně pozitivních výsledků. Se liší v tom, jak tyto dvě metriky graf zobrazíte klepnutím na **přesnost/spojené s VRACENÍM** křivku na stránce výstup výsledku vyhodnocení (horní levé části Obrázek 7).

![Výsledky vyhodnocení binární klasifikace](./media/evaluate-model-performance/7.png)

Obrázek 7. Výsledky vyhodnocení binární klasifikace.

Jiné související metriky, které se často používá je **F1 skóre**, který by přesnosti a spojené s vracením v úvahu. Je průměr těchto 2 metrik a je vypočítán takto: F1 = 2 (přesnost x odvolání) / (přesnost + spojené s vracením). Skóre F1 je dobrým způsobem, jak vytvořit souhrn vyhodnocení v jedno číslo, ale je vždy vhodné podívat se na přesnost a spojené s vracením společně, abyste lépe pochopili chování třídění.

Kromě toho jednu můžete kontrolovat true kladné míra vs. míru falešně pozitivních výsledků v **příjemce provozních charakteristik (roc s více TŘÍDAMI)** křivky a odpovídající **oblast v rámci the křivky (AUC)** hodnotu. Čím blíž je tato křivka do levého horního rohu, tím lepší výkon klasifikátor je (, který je maximalizace true míru pozitivních výsledků při současné minimalizaci míru falešně pozitivních výsledků). Křivky, která jsou blízko úhlopříčně vykreslení výsledku třídění, které mají tendenci k následné predikci, která jsou blízko náhodné opakovaně uhodnout.

### <a name="using-cross-validation"></a>Použití křížového ověření
Stejně jako v příkladu regrese jsme provedení křížového ověření opakovaně, skóre a vyhodnotit různé podmnožiny dat automaticky. Podobně, můžeme použít [Cross-Validate modelu] [ cross-validate-model] modulu, Nezkušený Logistický regresní model a datové sady. Popisek sloupce musí být nastaveno na *příjem* v [Cross-Validate modelu] [ cross-validate-model] vlastnosti modulu. Po spuštění experimentu a kliknutím na pravé straně výstupní port modulu [Cross-Validate modelu] [ cross-validate-model] modulu, vidíme hodnoty metrik binární klasifikaci pro každý fold kromě pro střední a Směrodatná odchylka každého. 

![Křížové ověření binární klasifikační Model](./media/evaluate-model-performance/8.png)

Obrázek 8. Křížové ověření binární klasifikační Model.

![Výsledků křížového ověřování binární třídění](./media/evaluate-model-performance/9.png)

Obrázek 9. Výsledků křížového ověřování binární třídění.

## <a name="evaluating-a-multiclass-classification-model"></a>Vyhodnocení modelu klasifikace víc tříd
V tomto experimentu budeme používat Oblíbené [Iris](http://archive.ics.uci.edu/ml/datasets/Iris "Iris") datovou sadu, která obsahuje instance 3 různé typy (třídy) zařízení iris. Existují 4 hodnoty funkcí (sepal length či šířku a petal délka a šířka) pro každou instanci. V předchozí pokusy jsme natrénovali a testováním modelů pomocí stejné datové sady. Tady budeme používat [rozdělení dat] [ split] modulu, který chcete vytvořit 2 podmnožiny dat, trénování na první a stanovení skóre a vyhodnotit na druhé. Je veřejně dostupná na datovou sadu Iris [UCI Machine Learning úložiště](http://archive.ics.uci.edu/ml/index.html)a můžete ji stáhnout prostřednictvím [Import dat] [ import-data] modulu.

### <a name="creating-the-experiment"></a>Vytvoření experimentu
Přidejte následující moduly do pracovního prostoru Azure Machine Learning Studio:

* [Import dat][import-data]
* [Rozhodovací les se multiclass][multiclass-decision-forest]
* [Rozdělení dat.][split]
* [Trénování modelu][train-model]
* [Určení skóre modelu][score-model]
* [Vyhodnocení modelu][evaluate-model]

Jak vidíte na obrázku 10 připojení portů.

Nastavit index sloupce popisek [Train Model] [ train-model] modulu je 5. Datová sada nemá žádný řádek záhlaví, ale víme, že popisky třídy jsou v pátém sloupci.

Klikněte na [Import dat] [ import-data] modulu a nastavte *zdroj dat* vlastnost *adresa URL webu přes protokol HTTP*a *URL* k http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Nastavte zlomek instance má být použit pro školení v oblasti [rozdělení dat] [ split] modulu (0,7 příkladu).

![Vyhodnocení víc tříd třídění](./media/evaluate-model-performance/10.png)

Obrázek 10. Vyhodnocení víc tříd třídění

### <a name="inspecting-the-evaluation-results"></a>Kontrola výsledků vyhodnocení
Spusťte experiment a klikněte na výstupní port modulu [Evaluate Model][evaluate-model]. Výsledky hodnocení jsou uvedeny v tomto případě ve formě chybovou matici. Matice zobrazuje skutečné vs. předpovídaný počet instancí pro všechny 3 třídy.

![Výsledky vyhodnocení klasifikace víc tříd](./media/evaluate-model-performance/11.png)

Obrázek 11. Výsledky vyhodnocení klasifikace víc tříd.

### <a name="using-cross-validation"></a>Použití křížového ověření
Jak už bylo zmíněno dříve, můžete provést opakovaný školení, vyhodnocení a hodnocení automaticky pomocí [Cross-Validate modelu] [ cross-validate-model] modulu. Je třeba datové sady, model Nezkušený a [Cross-Validate modelu] [ cross-validate-model] modulu (viz obrázek níže). Znovu musíte nastavit popisek sloupec [Cross-Validate modelu] [ cross-validate-model] modulu (index sloupce 5 v tomto případě). Po spuštění experimentu a kliknutím na pravé straně výstupní port modulu [Cross-Validate modelu][cross-validate-model], si můžete prohlédnout hodnoty metrik pro každé fold, stejně jako na střední a standardní odchylku. Metriky tady zobrazí jsou podobné těm, které jsou popsány v případě binární klasifikace. Mějte však na paměti, že v klasifikace víc tříd, výpočetní prostředí hodnotu true pozitivní nebo negativní a falešně pozitivní nebo negativní probíhá určeno spočítáním na základě každé třídy, neexistuje žádná třída celkové kladné nebo záporné. Například při výpočtu přesnost nebo odvolání třídy "Iris-setosa", předpokládá se, že se jedná pozitivní třídy a všechny ostatní jako záporné.

![Křížové ověření modelu klasifikace víc tříd](./media/evaluate-model-performance/12.png)

Obrázek 12. Křížové ověření Model klasifikace víc tříd.

![Výsledků křížového ověřování modelu klasifikace víc tříd](./media/evaluate-model-performance/13.png)

Obrázek 13. Výsledků křížového ověřování modelu klasifikace víc tříd.

<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/

