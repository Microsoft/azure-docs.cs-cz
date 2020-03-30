---
title: Vyhodnocení výkonu modelu
titleSuffix: ML Studio (classic) - Azure
description: Zjistěte, jak vyhodnotit výkon modelu v Azure Machine Learning Studio (klasické) a o metriky, které jsou k dispozici pro tento úkol.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 3c041834b9ad191817cdf1380b0a75efc7639bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218143"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>Jak vyhodnotit výkon modelu v Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Tento článek ukazuje, jak vyhodnotit výkon modelu v Azure Machine Learning Studio (klasické) a poskytuje stručné vysvětlení metriky, které jsou k dispozici pro tento úkol. Jsou prezentovány tři běžné scénáře učení pod dohledem: 

* Regrese
* binární klasifikace 
* klasifikace více tříd



Vyhodnocení výkonu modelu je jednou z hlavních fází procesu datové vědy. Označuje, jak úspěšné bylo vyhodnocování (předpovědi) datové sady trénovaným modelem. 

Azure Machine Learning Studio (classic) podporuje vyhodnocení modelu prostřednictvím dvou svých hlavních modulů strojového učení: [Vyhodnotit model][evaluate-model] a [model křížového ověření][cross-validate-model]. Tyto moduly umožňují zobrazit, jak si váš model vede z hlediska počtu metrik, které se běžně používají ve strojovém učení a statistikách.

## <a name="evaluation-vs-cross-validation"></a>Vyhodnocení vs. křížové ověření
Hodnocení a křížové ověřování jsou standardní způsoby měření výkonu modelu. Oba generovat metriky hodnocení, které můžete zkontrolovat nebo porovnat s jinými modely.

[Vyhodnotit Model][evaluate-model] očekává scored datové sady jako vstup (nebo dva v případě, že chcete porovnat výkon dvou různých modelů). Proto je třeba trénovat model pomocí modulu [Model vlaku][train-model] a provést předpovědi na některé datové sady pomocí modulu [model skóre][score-model] před vyhodnocením výsledků. Hodnocení je založeno na skórované popisky / pravděpodobnosti spolu s true popisky, z nichž všechny jsou výstupy modulem [Score Model.][score-model]

Případně můžete pomocí křížového ověření automaticky provádět několik operací vyhodnocení skóre vlaku (10 krát) v různých podmnožinech vstupních dat. Vstupní data jsou rozdělena do 10 částí, kde je jedna vyhrazena pro testování a druhá 9 pro školení. Tento proces se opakuje 10 krát a metriky hodnocení jsou zprůměrovány. To pomáhá určit, jak dobře by model zobecnit na nové datové sady. [Modul Cross-Validate Model][cross-validate-model] přebírá netrénovaný model a některé označené datové sady a výstupy výsledky hodnocení každého z 10 záhybů, kromě průměrných výsledků.

V následujících částech vytvoříme jednoduché regresní a klasifikační modely a vyhodnotíme jejich výkon pomocí modulů [Vyhodnotit model][evaluate-model] a [Model křížového ověření.][cross-validate-model]

## <a name="evaluating-a-regression-model"></a>Vyhodnocení regresního modelu
Předpokládejme, že chceme předpovědět cenu vozu pomocí funkcí, jako jsou rozměry, výkon, specifikace motoru a tak dále. Jedná se o typický regresní problém, kdy cílová proměnná (*cena*) je průběžná číselná hodnota. Můžeme se vejít do lineárního regresního modelu, který vzhledem k hodnotám prvků určitého auta dokáže předpovědět cenu tohoto vozu. Tento regresní model lze použít ke skóre stejné datové sady jsme trénovaní na. Jakmile budeme mít předpokládané ceny automobilů, můžeme vyhodnotit výkon modelu tím, že se podíváme na to, jak moc se předpovědi odchylují od skutečných cen v průměru. Pro ilustraci používáme *datovou sadu dat o cenách automobilů (Raw),* která je k dispozici v části **Uložené datové sady** v Machine Learning Studiu (klasika).

### <a name="creating-the-experiment"></a>Vytvoření experimentu
Přidejte do svého pracovního prostoru ve službě Azure Machine Learning Studio (klasické) následující moduly:

* Údaje o cenách automobilů (Raw)
* [Lineární regrese][linear-regression]
* [Model vlaku][train-model]
* [Model skóre][score-model]
* [Vyhodnotit model][evaluate-model]

Připojte porty, jak je znázorněno níže na obrázku 1 a nastavte sloupec Popisek modulu [Model vlaku][train-model] na *cenu*.

![Vyhodnocení regresního modelu](./media/evaluate-model-performance/1.png)

Obrázek 1: Vyhodnocení regresního modelu.

### <a name="inspecting-the-evaluation-results"></a>Kontrola výsledků hodnocení
Po spuštění experimentu můžete kliknout na výstupní port modulu [Vyhodnotit model][evaluate-model] a vybrat *možnost Visualize,* chcete-li zobrazit výsledky hodnocení. Metriky hodnocení, které jsou k dispozici pro regresní modely, jsou: *Střední absolutní chyba*, *Kořenová střední absolutní chyba*, Relativní absolutní *chyba*, *Relativní kvadratická chyba*a Koeficient *určení*.

Termín "chyba" zde představuje rozdíl mezi předpovídanou hodnotou a skutečnou hodnotou. Absolutní hodnota nebo čtverec tohoto rozdílu se obvykle vypočítá tak, aby byla zachycena celková velikost chyby ve všech instancích, protože rozdíl mezi předpokládanou a skutečnou hodnotou může být v některých případech záporný. Metriky chyb měří prediktivní výkon regresního modelu z hlediska střední odchylky jeho předpovědi od skutečné hodnoty. Nižší chybové hodnoty znamenají, že model je přesnější při vytváření předpovědí. Celková metrika chyb nula znamená, že model dokonale odpovídá datům.

Koeficient stanovení, který je také známý jako R na druhou, je také standardní způsob měření, jak dobře model odpovídá datům. Může být interpretován jako podíl variací vysvětlený modelem. Vyšší podíl je lepší v tomto případě, kde 1 znamená dokonalé uchycení.

![Metriky lineárníregrese](./media/evaluate-model-performance/2.png)

Obrázek 2. Metriky lineárníregrese.

### <a name="using-cross-validation"></a>Použití křížového ověření
Jak již bylo zmíněno dříve, můžete provádět opakované školení, vyhodnocování a hodnocení automaticky pomocí modulu [Cross-Validate Model.][cross-validate-model] Vše, co potřebujete v tomto případě je datová sada, neomezený model a [modul modelu křížového ověření][cross-validate-model] (viz obrázek níže). Sloupec popisku je třeba nastavit na *cenu* ve vlastnostech modulu [Křížová ověření modelu.][cross-validate-model]

![Křížové ověření regresního modelu](./media/evaluate-model-performance/3.png)

Obrázek 3: Křížové ověřování regresního modelu.

Po spuštění experimentu můžete zkontrolovat výsledky hodnocení kliknutím na správný výstupní port modulu [Model křížového ověření.][cross-validate-model] To poskytne podrobný přehled metrik pro každou iteraci (fold) a zprůměrované výsledky každé metriky (obrázek 4).

![Výsledky křížového ověření regresního modelu](./media/evaluate-model-performance/4.png)

Obrázek 4. Výsledky křížového ověření regresního modelu.

## <a name="evaluating-a-binary-classification-model"></a>Vyhodnocení binárního klasifikačního modelu
V binárním scénáři klasifikace má cílová proměnná pouze dva možné výsledky, například: {0, 1} nebo {false, true}, {negative, positive}. Předpokládejme, že dostanete datovou sadu dospělých zaměstnanců s určitými demografickými proměnnými a proměnnými zaměstnání a že budete vyzváni k předvídání úrovně příjmu, binární proměnné s hodnotami {"<=50 K", ">50 K"}. Jinými slovy, negativní třída představuje zaměstnance, kteří dělají méně než nebo rovno 50 K za rok a kladné třídy představuje všechny ostatní zaměstnance. Stejně jako v regresní scénář, bychom trénování modelu, skóre některá data a vyhodnotit výsledky. Hlavní rozdíl je zde výběr metrik Azure Machine Learning Studio (klasické) výpočetní ch odpočítává a výstupy. Pro ilustraci scénáře predikce úrovně příjmu použijeme datovou sadu [pro dospělé](https://archive.ics.uci.edu/ml/datasets/Adult) k vytvoření studiového (klasického) experimentu a vyhodnocení výkonu dvoutřídního logistického regresního modelu, běžně používaného binárního třídění.

### <a name="creating-the-experiment"></a>Vytvoření experimentu
Přidejte do svého pracovního prostoru ve službě Azure Machine Learning Studio (klasické) následující moduly:

* Dospělý sčítání lidu příjem binární klasifikace datový soubor
* [Logistická regrese se dvěma třídami][two-class-logistic-regression]
* [Model vlaku][train-model]
* [Model skóre][score-model]
* [Vyhodnotit model][evaluate-model]

Připojte porty, jak je znázorněno níže na obrázku 5, a nastavte sloupec Popisek modulu [Model vlaku][train-model] na *příjem*.

![Vyhodnocení binárního klasifikačního modelu](./media/evaluate-model-performance/5.png)

Obrázek 5. Vyhodnocení binárníklasifikační model.

### <a name="inspecting-the-evaluation-results"></a>Kontrola výsledků hodnocení
Po spuštění experimentu můžete kliknout na výstupní port modulu [Vyhodnotit model][evaluate-model] a vybrat *možnost Visualize,* chcete-li zobrazit výsledky hodnocení (obrázek 7). Hodnotící metriky, které jsou k dispozici pro binární klasifikační modely, jsou: *Přesnost*, *Přesnost*, *Odvolání*, *Skóre F1*a *AUC*. Kromě toho modul výstupy zmatek matice zobrazující počet true positives, falešně negativní, falešně pozitivní a skutečné negativy, stejně jako *ROC*, *Přesnost / Odvolání*, a *Výtah* křivky.

Přesnost je jednoduše podíl správně klasifikovaných případů. Obvykle se jedná o první metriku, na kterou se podíváte při vyhodnocování třídění. Však pokud jsou data testu nevyvážená (kde většina instancí patří do jedné z tříd) nebo vás více zajímá výkon na jedné z tříd, přesnost ve skutečnosti nezachycuje účinnost třídění. Ve scénáři klasifikace úrovně příjmu předpokládejme, že testujete na některých datech, kde 99 % instancí představuje lidi, kteří vydělávají méně než 50 tisíc ročně nebo se jí rovná. Je možné dosáhnout přesnosti 0,99 předvídáním třídy "<= 50 kB" pro všechny instance. Třídění v tomto případě se zdá být odvádí dobrou práci celkově, ale ve skutečnosti, to nedokáže klasifikovat některý z high-příjmy jednotlivců (1%) Správně.

Z tohoto důvodu je užitečné vypočítat další metriky, které zachycují konkrétnější aspekty hodnocení. Před vstupem do podrobností těchto metrik, je důležité pochopit zmatek matice binární klasifikace hodnocení. Popisky tříd v trénovací sadě mohou převzít pouze dvě možné hodnoty, které obvykle označujeme jako kladné nebo záporné. Kladné a záporné instance, které klasifikátor předpovídá správně, se nazývají true positives (TP) a true negatives (TN). Podobně nesprávně klasifikované instance se nazývají falešně pozitivní (FP) a falešné negativy (FN). Matice záměny je jednoduše tabulka zobrazující počet instancí, které spadají do každé z těchto čtyř kategorií. Azure Machine Learning Studio (classic) automaticky rozhodne, které ze dvou tříd v datové sadě je kladná třída. Pokud jsou popisky třídy logické nebo celá čísla, pak jsou instance s popiskem "true" nebo "1" přiřazeny kladné třídě. Pokud popisky jsou řetězce, například s datovou sadou příjmů, popisky jsou seřazeny abecedně a první úroveň je vybrána jako záporná třída, zatímco druhá úroveň je kladná třída.

![Binární klasifikace Zmatek Matice](./media/evaluate-model-performance/6a.png)

Obrázek 6. Binární klasifikace Zmatek Matice.

Vrátíme-li se k problému klasifikace příjmů, chtěli bychom se zeptat na několik hodnotících otázek, které nám pomáhají pochopit výkon použitého klasifikátoru. Přirozená otázka zní: "Z jednotlivců, které model předpověděl, že budou vydělávat >50 K (TP + FP), kolik bylo správně klasifikováno (TP)?" Na tuto otázku lze odpovědět na **otázku Přesnost** modelu, což je podíl kladek, které jsou klasifikovány správně: TP/(TP+FP). Další častá otázka je "Ze všech vysoce vydělávajících zaměstnanců s příjmem >50k (TP + FN), kolik se klasifikátor klasifikovat správně (TP)". Ve skutečnosti se jedná o **vyvolání**nebo skutečnou kladnou míru: TP/(TP+FN) klasifikátoru. Můžete si všimnout, že existuje zřejmý kompromis mezi přesností a odvoláním. Například vzhledem k relativně vyvážené datové sady, třídění, který předpovídá většinou pozitivní instance, by měl vysoké odvolání, ale poměrně nízká přesnost, protože mnoho negativních instancí by bylo chybně klasifikováno, což by vedlo k velkému počtu falešných poplachů. Chcete-li zobrazit obrázek o tom, jak se tyto dvě metriky liší, můžete kliknout na křivku **PŘESNOST/ODVOLÁNÍ** na výstupní stránce výsledků hodnocení (vlevo nahoře obrázek 7).

![Výsledky hodnocení binární klasifikace](./media/evaluate-model-performance/7.png)

Obrázek 7. Výsledky hodnocení binární klasifikace.

Další související metrika, která se často používá, je **skóre F1**, které bere v úvahu přesnost i odvolání. Jedná se o harmonický průměr těchto dvou metrik a vypočítá se jako takový: F1 = 2 (přesnost x recall) / (přesnost + vyvolání). Skóre F1 je dobrý způsob, jak shrnout hodnocení v jednom čísle, ale je vždy vhodné podívat se na přesnost a odvolání společně lépe pochopit, jak se klasifikátor chová.

Kromě toho je možné zkontrolovat skutečnou kladnou míru vs. falešně pozitivní rychlost v **křivce provozní charakteristiky přijímače (ROC)** a odpovídající hodnotu **Plocha pod křivkou (AUC).** Čím blíže je tato křivka k levému hornímu rohu, tím lepší je výkon klasifikátoru (to je maximalizace skutečné kladné sazby při minimalizaci falešně pozitivní sazby). Křivky, které se blíží úhlopříčce vykreslování, jsou výsledkem klasifikátorů, které mají tendenci provádět předpovědi, které se blíží náhodnému hádání.

### <a name="using-cross-validation"></a>Použití křížového ověření
Stejně jako v příkladu regrese můžeme provádět křížové ověření opakovaně trénovat, skóre a vyhodnocovat různé podmnožiny dat automaticky. Podobně můžeme použít modul [Cross-Validate Model,][cross-validate-model] neomezený logistický regresní model a datovou sadu. Sloupec popisek musí být nastaven na *příjem* ve vlastnostech modulu [Křížová ověření modelu.][cross-validate-model] Po spuštění experimentu a kliknutí na správný výstupní port modulu [Cross-Validate Model][cross-validate-model] můžeme kromě střední a směrodatné odchylky každého z nich vidět hodnoty binární klasifikace pro každý záhyb. 

![Křížové ověření binárního klasifikačního modelu](./media/evaluate-model-performance/8.png)

Obrázek 8. Křížové ověření binárního klasifikačního modelu.

![Výsledky křížového ověření binárního třídění](./media/evaluate-model-performance/9.png)

Obrázek 9. Výsledky křížového ověření binárního třídění.

## <a name="evaluating-a-multiclass-classification-model"></a>Vyhodnocení vícetřídového klasifikačního modelu
V tomto experimentu použijeme populární datovou sadu [Iris,](https://archive.ics.uci.edu/ml/datasets/Iris "Iris") která obsahuje instance tří různých typů (tříd) rostliny duhovky. Existují čtyři hodnoty prvku (sepal délka/šířka a délka/šířka okvětního lístku) pro každou instanci. V předchozích experimentech jsme trénovali a testovali modely pomocí stejných datových sad. Zde použijeme modul [Rozdělit data][split] k vytvoření dvou podmnožinů dat, trénování na první a skóre a vyhodnocení na druhé. Datová sada Iris je veřejně dostupná v [úložišti strojového učení UCI](https://archive.ics.uci.edu/ml/index.html)a lze ji stáhnout pomocí modulu [Import Data.][import-data]

### <a name="creating-the-experiment"></a>Vytvoření experimentu
Přidejte do svého pracovního prostoru ve službě Azure Machine Learning Studio (klasické) následující moduly:

* [Import dat][import-data]
* [Rozhodovací les s více třídami][multiclass-decision-forest]
* [Rozdělení dat][split]
* [Model vlaku][train-model]
* [Model skóre][score-model]
* [Vyhodnotit model][evaluate-model]

Připojte porty, jak je znázorněno níže na obrázku 10.

Nastavte index sloupce Popisek modulu [Model vlaku][train-model] na 5. Datová sada nemá žádný řádek záhlaví, ale víme, že popisky třídy jsou v pátém sloupci.

Klikněte na modul [Importovat data][import-data] a nastavte vlastnost *Data source* na *webovou adresu URL přes HTTP*a adresu *URL* na http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Nastavte zlomek instancí, které mají být použity pro trénování v modulu [Rozdělit data][split] (0,7 například).

![Vyhodnocení klasifikátoru vícetřídy](./media/evaluate-model-performance/10.png)

Obrázek 10. Vyhodnocení klasifikátoru vícetřídy

### <a name="inspecting-the-evaluation-results"></a>Kontrola výsledků hodnocení
Spusťte experiment a klikněte na výstupní port [evaluate model][evaluate-model]. Výsledky hodnocení jsou v tomto případě prezentovány ve formě matice záměny. Matice zobrazuje skutečné vs. předpokládané instance pro všechny tři třídy.

![Výsledky hodnocení klasifikace více tříd](./media/evaluate-model-performance/11.png)

Obrázek 11. Výsledky hodnocení klasifikace více tříd.

### <a name="using-cross-validation"></a>Použití křížového ověření
Jak již bylo zmíněno dříve, můžete provádět opakované školení, vyhodnocování a hodnocení automaticky pomocí modulu [Cross-Validate Model.][cross-validate-model] Budete potřebovat datovou sadu, neomezený model a modul [modelu křížového ověření][cross-validate-model] (viz obrázek níže). Opět je třeba nastavit sloupec popisku modulu [Cross-Validate Model][cross-validate-model] (index sloupce 5 v tomto případě). Po spuštění experimentu a kliknutí na pravý výstupní port [modelu křížového ověření][cross-validate-model]můžete zkontrolovat hodnoty metrik pro každý záhyb a také střední a směrodatnou odchylku. Zde zobrazené metriky jsou podobné těm, které jsou popsány v případě binární klasifikace. V klasifikaci více tříd se však výpočet skutečných kladítek/negativů a falešně pozitivních hodnot/negativů provádí počítáním na základě třídy, protože neexistuje žádná celkově pozitivní nebo záporná třída. Například při výpočtu přesnosti nebo odvolání třídy "Iris-setosa" se předpokládá, že se jedná o kladnou třídu a všechny ostatní jako negativní.

![Křížové ověření klasifikačního modelu více tříd](./media/evaluate-model-performance/12.png)

Obrázek 12. Křížové ověřování klasifikačního modelu více tříd.

![Výsledky křížové ho ověření vícetřídního klasifikačního modelu](./media/evaluate-model-performance/13.png)

Obrázek 13. Výsledky křížového ověření klasifikačního modelu více tříd.

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
