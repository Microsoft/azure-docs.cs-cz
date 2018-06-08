---
title: Vyhodnocení modelu výkon v Machine Learning | Microsoft Docs
description: Vysvětluje, jak k vyhodnocení výkonu modelu v Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 5dc5348a-4488-4536-99eb-ff105be9b160
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: bb49fd2fe7f72e211fbbda7cffdd2308c2c36fba
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834229"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning"></a>Jak vyhodnotit výkon modelu ve službě Azure Machine Learning
Tento článek ukazuje, jak k vyhodnocení výkonu modelu v Azure Machine Learning Studio a poskytuje stručné vysvětlení metriky, které jsou k dispozici pro tuto úlohu. Tři běžné scénáře pod dohledem learning uvádíme: 

* Regrese
* binární klasifikace 
* více třídami klasifikace

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Vyhodnocení výkonu modelu je jedním z fáze jádra v procesu vědecké účely data. Označuje, jak úspěšné že vyhodnocování (předpověď) datové sady byl pomocí modulu trained model. 

Azure Machine Learning podporuje model vyhodnocení prostřednictvím dvou jeho hlavní strojového učení moduly: [Evaluate Model] [ evaluate-model] a [křížové ověření modelu][cross-validate-model]. Tyto moduly umožňují zobrazit, jak se provádí modelu z hlediska počtu metriky, které běžně se používají v machine learning a statistiky.

## <a name="evaluation-vs-cross-validation"></a>Vyhodnocení vs. Křížové ověření
Vyhodnocení a křížového ověření způsoby standardní měření výkonu modelu. Obě vygenerovat zkušební metriky, které můžete zkontrolovat nebo porovnán těch, které ostatní modely.

[Vyhodnocení modelu] [ evaluate-model] očekává scored datovou sadu jako vstup (nebo 2 v případě, kterou chcete porovnat výkon 2 odlišnými modely). To znamená, že budete muset cvičení váš model pomocí [trénování modelu] [ train-model] modul a zkontrolujte předpovědi na některé datové sady pomocí [Score Model] [ score-model] modulu, než je možné vyhodnotit výsledky. Hodnocení je založeno na scored popisky nebo pravděpodobnostech společně s true popisky, které jsou výstupem [Score Model] [ score-model] modulu.

Alternativně můžete křížového ověření provést několik train – score vyhodnotit operací (10 složení) automaticky na různých podmnožiny vstupní data. Vstupní data je rozdělená do 10 části, kde jeden je vyhrazena pro testování, a dalších 9 pro školení. Tento proces se opakuje 10krát a průměr se metrik hodnocení. To pomáhá při určení, jak dobře model by generalize na nové datové sady. [Křížové ověření modelu] [ cross-validate-model] modul přebírá nevyškoleným modelu a některé s popiskem datové sady a vyhodnocení výsledky jednotlivých 10 složení, kromě zprůměrovanou výsledky.

V následujících částech jsme sestavení jednoduché regrese a klasifikace modely a vyhodnotit jejich výkonu pomocí [Evaluate Model] [ evaluate-model] a [křížové ověření modelu] [ cross-validate-model] moduly.

## <a name="evaluating-a-regression-model"></a>Vyhodnocení regresní Model
Předpokládejme, že chcete předpovídat cenu automobilu použití některých funkcí, jako je například dimenze, koňských sil, modul specifikací a tak dále. Tento problém typické regrese, je kde Cílová proměnná (*cena*) je nepřetržitá číselná hodnota. Model jednoduché lineární regrese, který funkce hodnoty určité automobilu, může předpovídat cenu automobilu, že jsme můžete začlenit. Tato regresní model slouží ke stanovení skóre stejné datové sady, které jsme natrénovali na. Jakmile jsme předpokládaných ceny pro všechny automobilů, jsme prohlížením kolik jsou předpovědi odchylují od skutečné ceny v průměru vyhodnocení výkonu příslušného modelu. Pro znázornění je používáme *Automobile price data (Raw) datovou sadu* k dispozici v **uložit datové sady** část v Azure Machine Learning Studio.

### <a name="creating-the-experiment"></a>Vytvoření experimentu
Přidejte následující moduly do pracovního prostoru v nástroji Azure Machine Learning Studio:

* Automobilů price data (Raw)
* [Lineární regrese][linear-regression]
* [Train Model][train-model]
* [Určení skóre modelu][score-model]
* [Vyhodnocení modelu][evaluate-model]

Připojte porty, jak je znázorněno níže na obrázku 1 a nastavte popisek sloupec [Train Model] [ train-model] modulu *cena*.

![Vyhodnocení regresní Model](./media/evaluate-model-performance/1.png)

Obrázek 1. Vyhodnocení regresní Model.

### <a name="inspecting-the-evaluation-results"></a>Probíhá kontrola výsledků vyhodnocení
Po spuštění experimentu, můžete kliknutím na výstupní port modulu [Evaluate Model] [ evaluate-model] modul a vyberte *vizualizovat* zobrazíte výsledky hodnocení. Jsou k dispozici pro modely regrese vyhodnocení metriky: *znamenat absolutní chyba*, *kořenové znamenat absolutní chyba*, *relativní absolutní chyba*, *relativní kvadratická chyba*a *koeficientu spolehlivosti*.

Termín "Chyba" představuje rozdíl mezi předpovězenou a skutečnou hodnotou. Absolutní hodnota nebo druhou mocninu tento rozdíl jsou obvykle vypočtenou hodnotu capture celkový odhad chyba napříč všemi instancemi jako rozdíl mezi předpokládaných a true hodnotou může být v některých případech záporná. Chyba metriky měření prediktivní výkon regresní model z hlediska střední odchylku jeho předpovědi z hodnot true. Nižší chybové hodnoty znamená, že je přesnější při provádění předpovědi modelu. Celkové metrika chyby 0 znamená modelu perfektně vyhovuje data.

Koeficient spolehlivosti, která je také označována jako R spolehlivosti, je také standardní způsob měření, jak dobře model vyhovuje data. Můžete se interpretuje jako podíl variace vysvětlené modelu. A vyšší v tomto případě je lepší míry, kde 1 určuje vzájemně přizpůsobit.

![Lineární regrese vyhodnocení metriky](./media/evaluate-model-performance/2.png)

Obrázek 2. Lineární regrese vyhodnocení metriky.

### <a name="using-cross-validation"></a>Pomocí křížové ověření
Jak už bylo zmíněno dříve, můžete provádět opakovaných školení, vyhodnocování a hodnocení automaticky pomocí [křížové ověření modelu] [ cross-validate-model] modulu. V takovém případě stačí je datová sada, model nevyškoleným a [křížové ověření modelu] [ cross-validate-model] modulu (viz následující obrázek). Všimněte si, budete muset nastavit popisek sloupce na *cena* v [křížové ověření modelu] [ cross-validate-model] vlastnosti modulu.

![Mezi ověřování regresní Model](./media/evaluate-model-performance/3.png)

Obrázek 3. Mezi ověřování regresní Model.

Po spuštění experimentu, si můžete prohlédnout výsledky hodnocení kliknutím na pravém výstupní port modulu [křížové ověření modelu] [ cross-validate-model] modulu. To poskytne podrobné zobrazení metriky pro každé iteraci (násobek) a zprůměrovanou výsledky jednotlivých metriky (obrázek 4).

![Křížové ověření výsledky regresní Model](./media/evaluate-model-performance/4.png)

Obrázek 4. Křížové ověření výsledky regresní Model.

## <a name="evaluating-a-binary-classification-model"></a>Vyhodnocení modelu binární klasifikace
Ve scénáři binární klasifikace Cílová proměnná má jenom dvě možné výsledky, například: {0, 1} nebo {false, true}, {záporná, kladné}. Předpokládejme, jsou uvedeny datové sady pro dospělé zaměstnancům některé demografické údaje a jejich zaměstnání proměnné a že se zobrazí výzva k předvídání úroveň příjmů, binární proměnné s hodnotami {"< = 50 tisíc", "> 50 tisíc"}. Jinými slovy záporné třída reprezentuje zaměstnanci, kteří žádají menší než nebo rovna 50 tisíc za jeden rok. a kladné třída reprezentuje všechny ostatní zaměstnanci. Jako ve scénáři regrese jsme by Trénink modelu, stanovení skóre některá data a vyhodnoťte výsledky. Hlavní rozdíl je volba metriky, které se vypočítá Azure Machine Learning a výstupy. Pro ilustraci scénář úrovně předpovědi příjem, budeme používat [pro dospělé](http://archive.ics.uci.edu/ml/datasets/Adult) datovou sadu, která vytvoření experimentu Azure Machine Learning a vyhodnocení výkonu příslušného two-class logistic regresní model, běžně používané binární třídění.

### <a name="creating-the-experiment"></a>Vytvoření experimentu
Přidejte následující moduly do pracovního prostoru v nástroji Azure Machine Learning Studio:

* Datové sady pro dospělé úplné zjišťování příjem binární klasifikace
* [Two-Class Logistic Regression][two-class-logistic-regression]
* [Train Model][train-model]
* [Určení skóre modelu][score-model]
* [Vyhodnocení modelu][evaluate-model]

Připojte porty, jak je znázorněno níže na obrázku 5 a nastavte popisek sloupec [Train Model] [ train-model] modulu *příjem*.

![Vyhodnocení modelu binární klasifikace](./media/evaluate-model-performance/5.png)

Obrázek 5. Vyhodnocení modelu binární klasifikace.

### <a name="inspecting-the-evaluation-results"></a>Probíhá kontrola výsledků vyhodnocení
Po spuštění experimentu, můžete kliknutím na výstupní port modulu [Evaluate Model] [ evaluate-model] modul a vyberte *vizualizovat* zobrazíte výsledky hodnocení (obrázek 7). Jsou k dispozici pro binární klasifikaci modely vyhodnocení metriky: *přesnost*, *přesnost*, *odvolat*, *F1 skóre*, a *AUC*. Kromě toho modul výstupy nedorozuměním matice zobrazující počet true pozitivních, chybná hlášení, falešně pozitivních a true negativy, a také *ROC*, *přesnost nebo odvolání*, a *navýšení* křivek.

Přesnost je jednoduše podíl správně klasifikovaný instancí. Obvykle je první metriku, které si prohlédnete při vyhodnocování třídění. Nicméně, pokud je v testovacích datech nevyvážené (kde většinu instance patřit do jedné ze třídy), nebo vás zajímá více výkonu na jednu z tříd, přesnost není skutečně zaznamenat efektivitu třídění. Ve scénáři příjem úrovně klasifikace předpokládá, že testujete na některá data, kde % 99 instance představovat osobu, která vám menší než nebo rovna 50 tisíc za jeden rok. Je možné dosáhnout 0.99 přesnost Odhadnutím toho, jaká třída "< = 50 tisíc" pro všechny instance. Třídění v tomto případě je to dobrý úlohy celkové, ale ve skutečnosti se nepodaří klasifikovat všechny vysokými příjmy jednotlivce (1 %) správně.

Z tohoto důvodu je užitečné k výpočtu další metriky, které zaznamenat více konkrétních aspektů vyhodnocení. Před přechodem na podrobné informace o tyto metriky, je důležité si uvědomit, matice nedorozuměním vyhodnocení binární klasifikace. Popisky třídy v sadě školení může trvat na pouze 2 možné hodnoty, které se obvykle označují jako kladné nebo záporné. Kladné a záporné instancí, které třídění předpovídá správně se nazývají true pozitivních (TP) a true negativy (TN). Podobně se nesprávně klasifikovaného instancí nazývají falešně pozitivních (FP) a výsledkům (FN). Matice nejasnosti se jednoduše tabulka zobrazující počet instancí, které spadají pod každé z těchto 4 kategorií. Azure Machine Learning automaticky rozhoduje, které dvě třídy v sadě dat je kladné třída. Pokud třída popisky jsou typu logická hodnota nebo celá čísla, jsou přiřazeny 'true' nebo '1' s popiskem instancí kladné třídy. Pokud popisky jsou řetězce, jako v případě sady příjem dat, jsou popisky abecedním a první úroveň je zvolen je záporný třídy, zatímco druhou úroveň je kladné třída.

![Matice nedorozuměním binární klasifikace](./media/evaluate-model-performance/6a.png)

Obrázek 6. Binární klasifikační matice nejasnostem.

Po návratu do příjem klasifikace problému, by chceme zeptejte se, že několik otázek hodnocení, které nám pomůže pochopit výkon třídění použít. Je velmi přirozené dotaz: ' mimo jednotlivce, kterým předpovědět modelu k získání být > 50 tisíc (transakční program + FP), kolik byly správnou klasifikaci (TP)?. Tento dotaz může odpovědi prohlížením **přesnost** modelu, který je poměr položky, které jsou klasifikovány správně: TP/(TP+FP). Další běžné otázky je "mimo všechny vysoce získávat zaměstnancům příjem > 50 tisíc (transakční program + FN), kolik třídění klasifikovat správně (TP)". Toto je ve skutečnosti **odvolat**, nebo hodnota true, kladné rychlost: TP/(TP+FN) třídění. Můžete si všimnout, že je zřejmé kompromis mezi přesnost a odvolání. Například zadány relativně vyrovnáváním datovou sadu, třídění, který bude předpovídat většinou kladné instancí, by měla mít vysokou odvolání, ale spíš nízkou přesnost jako řadu záporné instance by misclassified výsledkem velký počet falešně pozitivních zjištění. Pokud chcete zjistit, jak se tyto dvě metriky liší se graf, můžete kliknutím na křivku 'Přesnost nebo odvolání' na stránce výstupu výsledek vyhodnocení (nahoře vlevo součástí obrázek 7).

![Výsledky vyhodnocení binární klasifikace](./media/evaluate-model-performance/7.png)

Na obrázku 7. Výsledky vyhodnocení binární klasifikace.

Další související metrika, který se často používá **F1 skóre**, což trvá přesnost i pro vyvolání v úvahu. Je průměr těchto 2 metrik a jako takový je počítaný: F1 = 2 (přesnost x odvolání) / (přesnost + odvolání). Skóre F1 je dobrý způsob, jak shrnout vyhodnocení v jediné číslo, ale je vždy vhodné podívat se na přesnost i pro vyvolání společně, abyste lépe pochopili, jak se chová třídění.

Kromě toho jeden můžete prohlédnout true kladné rychlost oproti false kladné rychlost v **příjemce operační vlastnosti (ROC)** křivky a odpovídající **oblasti v části the křivky (AUC)** hodnotu. Čím bližší je tento křivky levém horním rohu, tím lepší výkon třídění je (která je maximalizace true kladné rychlost a současně minimalizujete její false kladné rychlost). Křivek, které již brzy bude dosaženo diagonálních grafu, výsledek z třídění, které jsou obvykle provádět předpovědi, které již brzy bude dosaženo náhodné hádání.

### <a name="using-cross-validation"></a>Pomocí křížové ověření
Jako v příkladu regrese jsme provedení křížového ověření opakovaně cvičení, skóre a vyhodnotit různé podmnožiny dat automaticky. Podobně, můžeme použít [křížové ověření modelu] [ cross-validate-model] modulu, model nevyškoleným logistic regression a datové sady. Popisek sloupce musí být nastavena na *příjem* v [křížové ověření modelu] [ cross-validate-model] vlastnosti modulu. Po spuštění experimentu a na pravé straně klikněte na výstupní port [křížové ověření modelu] [ cross-validate-model] modulu, uvidíme hodnoty metriky binární klasifikaci pro každý násobek kromě střední a směrodatnou odchylku jednotlivých. 

![Křížové ověření modelu binární klasifikace](./media/evaluate-model-performance/8.png)

Obrázek 8. Mezi ověřování modelu binární klasifikace.

![Křížové ověření výsledky binární třídění](./media/evaluate-model-performance/9.png)

Obrázek 9. Křížové ověření výsledky binární třídění.

## <a name="evaluating-a-multiclass-classification-model"></a>Vyhodnocení modelu více třídami klasifikace
V této experimentu budeme používat oblíbených [Iris](http://archive.ics.uci.edu/ml/datasets/Iris "Iris") datové sady, který obsahuje instance 3 různé typy (třídy) iris závodu. Existují 4 hodnot funkce (sepal délky a šířky a délky a šířky Okvětní lístek) pro každou instanci. V předchozí experimenty jsme natrénovali a otestovat modelů pomocí stejné datové sady. Zde použijeme [rozdělení dat] [ split] modul k vytváření 2 podmnožiny dat, cvičení na první a stanovení skóre a vyhodnocení na druhý. Je Iris datová sada veřejně dostupné na [UCI Machine Learning úložiště](http://archive.ics.uci.edu/ml/index.html)a můžete je stáhnout pomocí [importovat Data] [ import-data] modulu.

### <a name="creating-the-experiment"></a>Vytvoření experimentu
Přidejte následující moduly do pracovního prostoru v nástroji Azure Machine Learning Studio:

* [Umožňuje importovat Data][import-data]
* [Doménové struktury rozhodnutí multiclass][multiclass-decision-forest]
* [Rozdělení dat][split]
* [Train Model][train-model]
* [Určení skóre modelu][score-model]
* [Vyhodnocení modelu][evaluate-model]

Připojte porty, jak je znázorněno níže na obrázku 10.

Nastavte popisek index sloupce [Train Model] [ train-model] modulu 5. Datová sada má žádný řádek záhlaví, ale víme, že popisky třídy jsou ve sloupci pátý.

Klikněte na [importovat Data] [ import-data] modul a sadu *zdroj dat* vlastnost *adresa URL webové prostřednictvím protokolu HTTP*a *URL* k http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Nastavte podíl instance, které chcete použít pro školení v [rozdělení dat] [ split] modulu (0,7 např.).

![Vyhodnocení více třídami třídění](./media/evaluate-model-performance/10.png)

Obrázek 10. Vyhodnocení více třídami třídění

### <a name="inspecting-the-evaluation-results"></a>Probíhá kontrola výsledků vyhodnocení
Spusťte experiment a klikněte na výstupní port modulu [Evaluate Model][evaluate-model]. Výsledky hodnocení jsou uvedené ve formě matice nejasnostem, v takovém případě. Matice zobrazuje skutečné vs. předpokládaných instancí pro všechny 3 třídy.

![Výsledky vyhodnocení více třídami klasifikace](./media/evaluate-model-performance/11.png)

Obrázek 11. Výsledky vyhodnocení více třídami klasifikace.

### <a name="using-cross-validation"></a>Pomocí křížové ověření
Jak už bylo zmíněno dříve, můžete provádět opakovaných školení, vyhodnocování a hodnocení automaticky pomocí [křížové ověření modelu] [ cross-validate-model] modulu. Potřebovali byste datovou sadu, model nevyškoleným a [křížové ověření modelu] [ cross-validate-model] modulu (viz následující obrázek). Akci je nutné nastavit sloupec popisek [křížové ověření modelu] [ cross-validate-model] modulu (index sloupce 5 v tomto případě). Po spuštění experimentu a právo kliknete na výstupní port [křížové ověření modelu][cross-validate-model], si můžete prohlédnout metriky hodnoty pro každý násobek i odchylky střední a standard. Metriky zobrazí tady jsou podobná těm, které jsou popsané v případě, že binární klasifikace. Ale Všimněte si, že v více třídami klasifikaci, výpočetních pozitivních/negativy true a false pozitivních/negativy se provádí počítání na základě na třídu, protože neexistuje žádná třída celkové kladné a záporné. Například při výpočtu přesnost nebo odvolání 'Iris-setosa' třídy, se předpokládá, že toto je kladné třídy a všechny ostatní jako záporné.

![Křížové ověření modelu více třídami klasifikace](./media/evaluate-model-performance/12.png)

Obrázek 12. Mezi ověřování modelu více třídami klasifikace.

![Výsledků křížového ověřování modelu více třídami klasifikace](./media/evaluate-model-performance/13.png)

Obrázek 13. Výsledků křížového ověřování modelu více třídami klasifikace.

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

