---
title: Vyhodnotit výsledky experimentů AutoML
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak zobrazit a vyhodnotit grafy a metriky pro každý z automatizovaných testů strojového učení.
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/09/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, automl
ms.openlocfilehash: d5556ffb18a7a67e9415310f221e470761bf8cb8
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100098635"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Vyhodnotit automatizované výsledky experimentování ve strojovém učení

V tomto článku se dozvíte, jak vyhodnotit a porovnat modely, které proškole automatizované experimenty machine learningu (automatizované ML). V průběhu automatizovaného experimentu ML se vytvoří spousta běhů a při každém spuštění se vytvoří model. Pro každý model automatizované ML generuje metriky vyhodnocení a grafy, které vám pomůžou změřit výkon modelu. 

Například automatizované ML generuje v závislosti na typu experimentu následující grafy.

| Klasifikace| Regrese/prognózování |
| ----------------------------------------------------------- | ---------------------------------------- |
| [Konfuzní matice](#confusion-matrix)                       | [Histogram reziduí](#residuals)        |
| [Křivka provozních charakteristik (ROC) přijímače](#roc-curve) | [Předpověď oproti hodnotě true](#predicted-vs-true) |
| [Křivka odvolání (PR)](#precision-recall-curve)      |                                          |
| [Zvednutí křivky](#lift-curve)                                   |                                          |
| [Křivka kumulativních zisků](#cumulative-gains-curve)           |                                          |
| [Kalibrační křivka](#calibration-curve)                     |                     


## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. (Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree) před tím, než začnete.)
- Azure Machine Learning experiment byl vytvořen buď pomocí:
  - [Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md) (není potřeba žádný kód)
  - [Sada SDK pro Azure Machine Learning Python](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>Zobrazit výsledky spuštění

Po dokončení automatizovaného experimentu ML se dá najít historie běhů prostřednictvím:
  - Prohlížeč s [Azure Machine Learning studia](overview-what-is-machine-learning-studio.md)
  - Jupyter Poznámkový blok s použitím [widgetu RunDetails Jupyter](/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py&preserve-view=true)

Následující kroky a videa ukazují, jak zobrazit historii spuštění a metriky vyhodnocení modelu a grafy v studiu:

1. [Přihlaste se ke studiu](https://ml.azure.com/) a přejděte k pracovnímu prostoru.
1. V nabídce vlevo vyberte **experimenty**.
1. V seznamu experimentů vyberte svůj experiment.
1. V tabulce v dolní části stránky vyberte automatizované spuštění ML.
1. Na kartě **modely** vyberte **název algoritmu** pro model, který chcete vyhodnotit.
1. Na kartě **metriky** můžete pomocí zaškrtávacích políček v levé části Zobrazit metriky a grafy.

![Postup zobrazení metrik v studiu](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>Metriky klasifikace

Automatizované ML vypočítá metriku výkonu pro každý model klasifikace vygenerovaný pro váš experiment. Tyto metriky jsou založené na implementaci scikit s učením. 

Mnoho metrik klasifikace je definováno pro binární klasifikaci dvou tříd a vyžaduje průměry nad třídy, aby se vytvořilo jedno skóre pro klasifikaci více tříd. Scikit – poskytuje několik výpočetních metod, tři z nich, které automatizované ML zveřejňuje: **makro**, **Micro** a **Vážená**.

- **Makro** – vypočítá metriku pro každou třídu a vybere nevážený průměr.
-  Vypočítejte metriku globálně vynásobením celkového počtu skutečných kladů, falešně negativních hodnot a falešně pozitivních hodnot (nezávisle na třídách).
- **Vážená** – vypočítá metriku pro každou třídu a vezme vážený průměr na základě počtu vzorků na třídu.

Zatímco každá metoda pro průměrování má své výhody, jeden běžný aspekt při výběru vhodné metody je nevyrovnanost třídy. Pokud třídy mají odlišný počet vzorků, může být více informativní použít průměr makra, kde jsou menšinové třídy předány stejné vážení na většinu tříd. Přečtěte si další informace o [binárních a vícevrstvých metrikách v automatizovaném ml](#binary-vs-multiclass-classification-metrics). 

Následující tabulka shrnuje metriky výkonu modelu, které automatizované ML vypočítá pro každý model klasifikace vygenerovaný pro váš experiment. Další podrobnosti najdete v dokumentaci scikit-učení propojené v poli **výpočty** každé metriky. 

|Metric|Popis|Výpočet|
|--|--|---|
|AUC | AUC je oblast pod [křivkou s provozní charakteristikou přijímače](#roc-curve).<br><br> **Cíl:** Blíže k 1 lepšímu <br> **Rozsah:** [0, 1]<br> <br>Mezi podporované názvy metrik patří, <li>`AUC_macro`, aritmetický průměr AUC pro každou třídu.<li> `AUC_micro`, vypočítáno kombinací skutečných kladných hodnot a falešně pozitivních hodnot z každé třídy. <li> `AUC_weighted`, aritmetický průměr skóre pro každou třídu váže podle počtu skutečných instancí v každé třídě.   |[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| Přesnost je poměr předpovědi, který přesně odpovídá skutečným popiskům třídy. <br> <br>**Cíl:** Blíže k 1 lepšímu <br> **Rozsah:** [0, 1]|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|Průměrná přesnost shrnuje křivku pro odvolání přesnosti jako vážený průměr přesností dosaženého při každé prahové hodnotě, přičemž se zvýší počet odvolání z předchozí prahové hodnoty použité jako váha. <br><br> **Cíl:** Blíže k 1 lepšímu <br> **Rozsah:** [0, 1]<br> <br>Mezi podporované názvy metrik patří,<li>`average_precision_score_macro`, aritmetický průměr skóre každé třídy přesnosti.<li> `average_precision_score_micro`, vypočítáno kombinací pravdivé kladné hodnoty a falešně pozitivních při každém přerušení.<li>`average_precision_score_weighted`, aritmetický průměr průměrného skóre přesnosti pro každou třídu, váže podle počtu skutečných instancí v každé třídě.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|Vyvážená přesnost je aritmetickým průměrem odvolání jednotlivých tříd.<br> <br>**Cíl:** Blíže k 1 lepšímu <br> **Rozsah:** [0, 1]|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|Známkou F1 je harmonický průměr přesnosti a odvolání. Je dobře vyvážená míra falešně pozitivních i falešně negativních hodnot. Ale nebere v úvahu záporné negativní hodnoty. <br> <br>**Cíl:** Blíže k 1 lepšímu <br> **Rozsah:** [0, 1]<br> <br>Mezi podporované názvy metrik patří,<li>  `f1_score_macro`: aritmetický průměr skóre F1 pro každou třídu. <li> `f1_score_micro`: vypočítáno celkovými skutečnými kladnými, falešně negativními a falešně pozitivními. <li> `f1_score_weighted`: Vážený průměr podle četnosti třídy F1 skóre pro každou třídu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|Jedná se o funkci ztráty použitou v rámci (MULTINOMIAL) logistické regrese a rozšíření, jako jsou například sítě neuronové, definované jako negativní protokol – pravděpodobnost hodnot true pro pravděpodobnostní třídění předpovědi. <br><br> **Cíl:** Blíže k 0, lepší <br> **Rozsah:** [0, INF)|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| Normalizované odvolání makra je vyvolat makro – průměrně a normalizované, takže náhodný výkon má skóre 0 a dokonalý výkon má skóre 1. <br> <br>**Cíl:** Blíže k 1 lepšímu <br> **Rozsah:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>kde `R` je očekávaná hodnota `recall_score_macro` pro Random předpovědi.<br><br>`R = 0.5`&nbsp;pro &nbsp; binární &nbsp; klasifikaci. <br>`R = (1 / C)` pro problémy s klasifikací C-Class.|
matthews_correlation | Korelační koeficient Matthews je vyvážená míra přesnosti, která se dá použít i v případě, že jedna třída má mnoho dalších vzorků než jiné. Koeficient 1 označuje dokonalý předpověď, 0 náhodnou předpověď a-1 inverzní předpověď.<br><br> **Cíl:** Blíže k 1 lepšímu <br> **Rozsah:** [-1, 1]|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
Přesnost|Přesnost je schopnost modelu vyhnout se označování negativních vzorků jako pozitivních. <br><br> **Cíl:** Blíže k 1 lepšímu <br> **Rozsah:** [0, 1]<br> <br>Mezi podporované názvy metrik patří, <li> `precision_score_macro`, aritmetický průměr přesnosti pro každou třídu. <li> `precision_score_micro`, vypočítáno globálně pomocí počítání celkových skutečných kladných hodnot a falešně pozitivních hodnot. <li> `precision_score_weighted`, aritmetický průměr přesnosti pro každou třídu, váže podle počtu skutečných instancí v každé třídě.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|
Úplnost| Odvolání je schopnost modelu detekovat všechny pozitivní vzorky. <br><br> **Cíl:** Blíže k 1 lepšímu <br> **Rozsah:** [0, 1]<br> <br>Mezi podporované názvy metrik patří, <li>`recall_score_macro`: aritmetický průměr odvolání pro každou třídu. <li> `recall_score_micro`: vypočítáno globálně pomocí počítání celkových skutečných hodnot, falešně negativních hodnot a falešně pozitivních hodnot.<li> `recall_score_weighted`: aritmetický průměr odvolání pro každou třídu, vážený podle počtu skutečných instancí v každé třídě.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|Vážená přesnost je přesnost, kdy je každý vzorek vážený celkovým počtem vzorků patřících ke stejné třídě. <br><br>**Cíl:** Blíže k 1 lepšímu <br>**Rozsah:** [0, 1]|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>Binární a vícevrstvé metriky klasifikace

Automatizované ML nerozlišuje mezi binárními a více třídami metriky. Stejné metriky ověřování jsou hlášeny, zda má datová sada dvě třídy nebo více než dvě třídy. Některé metriky jsou však určeny pro klasifikaci s více třídami. Při použití na binární datovou sadu tyto metriky nepovažují žádnou třídu za `true` třídu, jak je možné očekávat. Metriky, které jsou jasně určeny pro více tříd, jsou s příponou `micro` , `macro` nebo `weighted` . Mezi příklady patří,,, `average_precision_score` `f1_score` `precision_score` `recall_score` a `AUC` .

Například namísto výpočtu odvolání jako `tp / (tp + fn)` je průměrně vyvolaná hodnota ve více třídách ( `micro` , `macro` nebo `weighted` ) v obou třídách binární datové sady klasifikace. To je ekvivalentní k výpočtu odvolání pro `true` třídu a `false` třídu samostatně a pak přebírání průměru dvou.

## <a name="confusion-matrix"></a>Konfuzní matice

Záměna matic poskytují vizuální informace o tom, jak model strojového učení provádí v předpovědi systematické chyby pro klasifikační modely. Slovo "zmatené" v názvu pochází z modelu "matoucí" nebo "nepopisované vzorky". Buňka v řádku `i` a sloupci `j` v nejasnostních maticích obsahuje počet vzorků v datové sadě vyhodnocení, které patří ke třídě `C_i` a byly klasifikovány modelem jako třída `C_j` .

V studiu tmavší buňka indikuje větší počet vzorků. Výběr **normalizovaného** zobrazení v rozevíracím seznamu se normalizuje nad každým řádkem matice, aby se zobrazilo procento třídy `C_i` předpokládaného pro třídu `C_j` . Výhodou **výchozího zobrazení** je, že se můžete podívat, zda nevyrovnanost při distribuci skutečných tříd způsobila, že model nezpůsobuje chybnou klasifikaci vzorků z minoritní třídy, což je běžný problém v nevyvážených datových sadách.

Matrice nejasností dobrého modelu bude mít většinu vzorků podél šikmého.

### <a name="confusion-matrix-for-a-good-model"></a>Nejasná matice pro dobrý model 
![Nejasná matice pro dobrý model ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>Nejasná matice pro špatný model
![Nejasná matice pro špatný model](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>ROC – křivka

Křivka s provozní charakteristikou (ROC) vykreslí vztah mezi skutečnou kladovou sazbou (TPR) a falešně pozitivní sazbou (ZAREGISTROVANÝCH zařízeních) jako změnu prahové hodnoty rozhodnutí. Křivka ROC může být méně informativní při výuce modelů u datových sad s vysokou nevyrovnanou třídou, protože většina třídy může drowN příspěvky z minoritních tříd.

Oblast pod křivkou (AUC) může být interpretována jako poměr správně klasifikovaných vzorků. Přesněji je AUC pravděpodobnost, že klasifikátor řadí náhodně zvolenou pozitivní ukázku větší než náhodně zvolený negativní vzorek. Tvar křivky poskytuje Intuition pro vztah mezi TPR a ZAREGISTROVANÝCH zařízeních jako funkce prahové hodnoty klasifikace nebo hranice rozhodnutí.

Křivka, která se blíží levému hornímu rohu grafu, se blíží hodnotě 100% TPR a 0% ZAREGISTROVANÝCH zařízeních, což je nejlepší možný model. Náhodný model vytvoří křivku ROC podél `y = x` čáry od levého dolního rohu po pravé horní části. Horší než náhodný model by měl mít křivku ROC, která je v `y = x` souladu s řádkem.
> [!TIP]
> Pro pokusy o klasifikaci lze každý spojnicové grafy vytvořené pro automatizované modely ML použít k vyhodnocení modelu na třídu nebo průměrně přes všechny třídy. Můžete přepínat mezi různými zobrazeními kliknutím na popisky tříd v legendě napravo od grafu.
### <a name="roc-curve-for-a-good-model"></a>Křivka ROC pro dobrý model
![Křivka ROC pro dobrý model](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>Křivka ROC pro špatný model
![Křivka ROC pro špatný model](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>Přesnost – odvolání křivky

Křivka s přesností na vyčíslení vykreslí vztah mezi přesností a odvoláním jako změny prahové hodnoty rozhodnutí. Odvolání je schopnost modelu detekovat všechny pozitivní vzorky a přesnost je schopnost modelu vyhnout se označování negativních vzorků jako pozitivních. Některé obchodní problémy mohou vyžadovat zvýšené odvolání a určitou vyšší přesnost v závislosti na relativní důležitosti při předcházení falešně negativním a falešně pozitivním hodnotám.
> [!TIP]
> Pro pokusy o klasifikaci lze každý spojnicové grafy vytvořené pro automatizované modely ML použít k vyhodnocení modelu na třídu nebo průměrně přes všechny třídy. Můžete přepínat mezi různými zobrazeními kliknutím na popisky tříd v legendě napravo od grafu.
### <a name="precision-recall-curve-for-a-good-model"></a>Přesnost – odvolání křivky pro dobrý model
![Přesnost – odvolání křivky pro dobrý model](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>Přesnost – odvolání křivky pro špatný model
![Přesnost – odvolání křivky pro špatný model](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>Křivka kumulativních zisků

Křivka kumulativních zisků vykreslí procentuální podíl pozitivních vzorků správně klasifikovaných jako funkce procenta vzorků, které se považují za to, kde bereme v úvahu vzorky v pořadí předpovězené pravděpodobnosti.

Chcete-li vypočítat zisk, nejprve seřaďte všechny vzorky z nejvyšší a nejnižší pravděpodobnosti předpovězené modelem. Pak využijte `x%` nejvyšší předpovědiy důvěry. Rozdělte počet pozitivních vzorků zjištěných v tom, že `x%` Celkový počet pozitivních vzorků má získat zisk. Kumulativní nárůst je procento pozitivních vzorků, které jsme zjistili při zvažování určitého procenta dat, která pravděpodobně patří do kladné třídy.

Dokonalý model se zařadí do všech pozitivních vzorků výše všech negativních vzorků, které poskytují křivku kumulativních zisků tvořené dvěma přímými segmenty. První je řádek se sklonem `1 / x` od do, `(0, 0)` `(x, 1)` kde `x` je zlomek vzorků, které patří do kladné třídy ( `1 / num_classes` Pokud třídy jsou vyváženy). Druhá je vodorovná čára od `(x, 1)` do `(1, 1)` . V prvním segmentu jsou všechny pozitivní vzorky klasifikovány správně a kumulativní nárůst je `100%` v rámci prvního `x%` zvážení vzorků.

Základní model standardních hodnot bude mít křivku kumulativních zisků `y = x` , na kterých se `x%` zjistilo, že vzorky `x%` byly zjištěny pouze z celkového pozitivních vzorků. Dokonalý model bude mít křivku mikroprůměru, která se dotýká levého horního rohu, a průměrnou čáru makra, která má sklon `1 / num_classes` až do 100%, a pak vodorovně, dokud procento dat není 100.
> [!TIP]
> Pro pokusy o klasifikaci lze každý spojnicové grafy vytvořené pro automatizované modely ML použít k vyhodnocení modelu na třídu nebo průměrně přes všechny třídy. Můžete přepínat mezi různými zobrazeními kliknutím na popisky tříd v legendě napravo od grafu.
### <a name="cumulative-gains-curve-for-a-good-model"></a>Křivka kumulativních zisků pro dobrý model
![Křivka kumulativních zisků pro dobrý model](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>Křivka kumulativních zisků pro špatný model
![Křivka kumulativních zisků pro špatný model](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>Zvednutí křivky

Křivka zvednutí ukazuje, kolikrát je model lépe vykonává v porovnání s náhodným modelem. Výtah je definován jako poměr kumulativního nárůstu na kumulativní zisk náhodného modelu.

Tento relativní výkon bere ohled na skutečnost, že klasifikace je obtížnější při zvýšení počtu tříd. (Náhodný model nesprávně předpovídá větší zlomek vzorků z datové sady s 10 třídami v porovnání s datovou sadou se dvěma třídami)

Křivka základního navýšení je `y = 1` řádek, ve kterém je výkon modelu konzistentní s modelem náhodného modelu. Obecně platí, že křivka zvednutí pro dobrý model bude v tomto grafu vyšší a větší než na ose x, což ukazuje, že když je model nejdůležitější v jeho předpovědi, bude vykonávání mnohem lepší než náhodné odhadování.

> [!TIP]
> Pro pokusy o klasifikaci lze každý spojnicové grafy vytvořené pro automatizované modely ML použít k vyhodnocení modelu na třídu nebo průměrně přes všechny třídy. Můžete přepínat mezi různými zobrazeními kliknutím na popisky tříd v legendě napravo od grafu.
### <a name="lift-curve-for-a-good-model"></a>Zvednutí křivky pro dobrý model
![Zvednutí křivky pro dobrý model](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>Zvednutí křivky pro špatný model
![Zvednutí křivky pro špatný model](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>Kalibrační křivka

Kalibrační křivka vykresluje v předpovědi důvěru modelu proti poměru pozitivních vzorků na každé úrovni spolehlivosti. Dobře kalibrovaný model správně klasifikuje 100% předpovědi, ke kterému přiděluje 100% spolehlivost, 50% předpovědi, který přiděluje 50% spolehlivost, 20% předpovědi, přiřadí 20% jistotu atd. Dokonale kalibrovaný model bude mít kalibrační křivku za čárou, `y = x` kde model dokonale předpovídá pravděpodobnost, že vzorky patří do každé třídy.

Model s vysokou jistotou bude přepovídat pravděpodobnosti téměř nula a jedna, zřídka se nejedná o třídu každého vzorku a kalibrační křivka bude vypadat podobně jako zpětně "S". Model s jistotou přiřadí nižší pravděpodobnost průměrně třídě, kterou předpokládá, a přidružená kalibrační křivka bude vypadat podobně jako "S". Kalibrační křivka nepopisuje schopnost modelu správně klasifikovat, ale místo toho je možné správně přiřadit důvěru ke svému předpovědiu. Špatný model může mít stále správnou kalibrační křivku, pokud model správně přiřadí nízkou důvěru a vysokou nejistotu.

> [!NOTE]
> Kalibrační křivka je citlivá na počet vzorků, takže malá sada ověření může způsobit vysokou přesnost výsledků, které mohou být obtížné. To nemusí nutně znamenat, že model není dobře kalibrován.

### <a name="calibration-curve-for-a-good-model"></a>Kalibrační křivka pro dobrý model
![Kalibrační křivka pro dobrý model](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>Kalibrační křivka pro špatný model
![Kalibrační křivka pro špatný model](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>Metriky regrese/prognózování

Automatizované ML vypočítá stejné metriky výkonu pro každý generovaný model bez ohledu na to, zda se jedná o regresi nebo experimenty předpovědi. Tyto metriky se také procházejí normalizací a umožňují porovnání modelů vyškolených na základě dat s různými rozsahy. Další informace najdete v tématu [normalizace metrik](#metric-normalization).  

Následující tabulka shrnuje metriky výkonu modelu vygenerované pro regresi a experimenty. Podobně jako metriky klasifikace jsou tyto metriky také založeny na implementaci scikitch informací. Odpovídající dokumentace ke službě scikit se v této části propojí v poli **Výpočet** .

|Metric|Popis|Výpočet|
--|--|--|
explained_variance|Vysvětluje odchylku míry, na kterou je rozsah pro variaci v cílové proměnné. Jedná se o procento snížení odchylky původních dat s odchylkou chyb. Pokud je střední hodnota chyb 0, rovná se součiniteli určení (viz r2_score níže). <br> <br> **Cíl:** Blíže k 1 lepšímu <br> **Rozsah:** (-INF, 1]|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|Střední absolutní chyba je očekávaná hodnota absolutní hodnoty rozdíl mezi cílem a předpovědi.<br><br> **Cíl:** Blíže k 0, lepší <br> **Rozsah:** [0, INF) <br><br> Druhy <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`, mean_absolute_error dělený rozsahem dat. | [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|Střední absolutní procentuální chyba (MAPE) je míra průměrného rozdílu mezi předpokládanou hodnotou a skutečnou hodnotou.<br><br> **Cíl:** Blíže k 0, lepší <br> **Rozsah:** [0, INF) ||
median_absolute_error|Střední absolutní chyba je medián všech absolutních rozdílů mezi cílem a předpovědi. Tato ztráta je robustní pro odlehlé hodnoty.<br><br> **Cíl:** Blíže k 0, lepší <br> **Rozsah:** [0, INF)<br><br>Druhy <br> `median_absolute_error`<br> `normalized_median_absolute_error`: median_absolute_error dělený rozsahem dat. |[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R ^ 2 je koeficient stanovení nebo procento snížení počtu kvadratických chyb v porovnání se základním modelem, který má za následek výstup středníku. <br> <br> **Cíl:** Blíže k 1 lepšímu <br> **Rozsah:** [-1, 1] <br><br> Poznámka: R ^ 2 má často rozsah (-INF, 1], ale automatizovaná ML ořízne záporné hodnoty pro velmi špatné modely na-1.|[Výpočet](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |Hlavní střední hodnota chyby (RMSE) je druhá odmocnina očekávaného čtvercového rozdílu mezi cílem a předpovědi. Pro neposunuté Estimator se RMSE rovná směrodatné odchylce.<br> <br> **Cíl:** Blíže k 0, lepší <br> **Rozsah:** [0, INF)<br><br>Druhy<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: root_mean_squared_error dělený rozsahem dat. |[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|Hodnota hlavní střední Chyba protokolu je druhá odmocnina očekávané čtvercové chyby.<br><br>**Cíl:** Blíže k 0, lepší <br> **Rozsah:** [0, INF) <br> <br>Druhy <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: root_mean_squared_log_error dělený rozsahem dat.  |[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| Korelace Spearman je neparametrová míra monotonicity vztahu mezi dvěma datovými sadami. Na rozdíl od korelace Pearsonova nepředpokládá korelace Spearman, že obě datové sady jsou normálně distribuovány. Podobně jako u jiných korelačních korelace se Spearman liší od-1 do 1 s 0, což neimplikuje žádnou korelaci. Korelace-1 nebo 1 implikuje přesný vztah monotónní. <br><br> Spearman je metrika korelace pořadí řazení, což znamená, že změny předpokládaných nebo skutečných hodnot nemění výsledek Spearman, pokud se nezmění pořadí řazení předpokládaných nebo skutečných hodnot.<br> <br> **Cíl:** Blíže k 1 lepšímu <br> **Rozsah:** [-1, 1]|[Výpočet](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>Normalizace metriky

Automatizované ML normalizuje regrese a prognózy metrik, které umožňují porovnání modelů vyškolených na základě dat s různými rozsahy. Model vyškolený na datech s větším rozsahem má vyšší chybu než stejný model, který je vyškolený pro data s menším rozsahem, pokud tato chyba není normalizovaná.

I když neexistuje žádná standardní metoda normalizace chybových metrik, automatizované ML používá společný přístup k dělení chyby v rozsahu dat: `normalized_error = error / (y_max - y_min)`

Při vyhodnocování modelu prognózy v datech časových řad používá automatizované ML dodatečné kroky, aby se zajistilo, že normalizace proběhne podle ID časové řady (zrnitý), protože každá časová řada má pravděpodobně jinou distribuci cílových hodnot.
## <a name="residuals"></a>Rezidua

Graf zbytků je histogram chyb předpovědi (zbytky) generovaných pro regresi a experimenty. Zbytky se počítají jako `y_predicted - y_true` pro všechny ukázky a pak se zobrazují jako histogram pro zobrazení posunu modelu.

V tomto příkladu si všimněte, že oba modely jsou mírně posunuté na předpověď nižší, než je skutečná hodnota. Pro datovou sadu se nejedná o neobvyklou datovou sadu s zkoseným rozdělením skutečných cílů, ale označuje horší výkon modelu. Dobrý model bude mít zbytkovou distribuci, která má špičku na nule s malým množstvím zbytků na extrémních hodnotách. Horší model bude mít rozdělení zbytků s menším počtem vzorků kolem nuly.

### <a name="residuals-chart-for-a-good-model"></a>Graf zbytku pro dobrý model
![Graf zbytku pro dobrý model](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>Graf zbytku pro špatný model
![Graf zbytku pro špatný model](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>Předpověď oproti hodnotě true

V případě regrese a předpovědi Experimentujte předpokládaný vs. true graf relace mezi cílovou funkcí (true/actual Values) a předpovědi modelu. Hodnoty true jsou rozdělený podél osy x a pro každou přihrádku se střední předpokládaná hodnota vykreslí s chybovými řádky. To vám umožní zjistit, jestli je model posunutý směrem k předpovědi určitých hodnot. Na řádku se zobrazí Průměrná předpověď a šedivá oblast indikuje odchylku předpovědi kolem této střední hodnoty.

Nejběžnější hodnota true bude často nejpřesnější předpovědi s nejnižší odchylkou. Vzdálenost trendového trendu z ideální `y = x` čáry, kde existuje několik skutečných hodnot, je dobré měřit výkon modelu u odlehlých hodnot. V dolní části grafu můžete použít histogram k odůvodnění skutečné distribuce dat. Včetně dalších ukázek dat, kde je distribuce zhuštěná, může zlepšit výkon modelu u nezpracovaných dat.

V tomto příkladu si všimněte, že lepší model má předpokládaný vs. true čára, která je blíže k ideální `y = x` čáře.

### <a name="predicted-vs-true-chart-for-a-good-model"></a>Předpovězený a pravdivý graf pro dobrý model
![Předpovězený a pravdivý graf pro dobrý model](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>Předpovězený a pravdivý graf pro špatný model
![Předpovězený a pravdivý graf pro špatný model](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>Vysvětlení modelů a důležitost funkcí

Zatímco metriky vyhodnocení modelu a grafy jsou vhodné pro měření obecné kvality modelu, zkontrolujte, které funkce DataSet mají model používaný k tomu, aby jeho předpovědi byl při vyzkoušení zodpovědného AI nezbytný. To je důvod, proč automatizované ML poskytuje řídicí panel pro vyhodnocení modelu, který umožňuje měřit a hlásit relativní příspěvky funkcí datové sady.

![Důležitost funkcí](./media/how-to-understand-automated-ml/how-to-feature-importance.gif)

Zobrazení řídicího panelu pro výklad v studiu:

1. [Přihlaste se ke studiu](https://ml.azure.com/) a přejděte k pracovnímu prostoru.
2. V nabídce vlevo vyberte **experimenty** .
3. Výběr experimentu ze seznamu experimentů
4. V tabulce v dolní části stránky vyberte AutoML spuštění.
5. Na kartě **modely** vyberte **název algoritmu** pro model, který chcete vysvětlit.
6. Na kartě **vysvětlení** se může zobrazit vysvětlení již vytvořené, pokud byl model nejlepší
7. Pokud chcete vytvořit nové vysvětlení, vyberte **vysvětlit model** a vyberte vzdálené výpočty, se kterými se mají vypočítat vysvětlení.

> [!NOTE]
> Model ForecastTCN se v současné době nepodporují pomocí automatizovaných vysvětlení ML a jiné modely prognózy můžou mít omezený přístup k nástrojům pro vyhodnocení.

## <a name="next-steps"></a>Další kroky
* Vyzkoušejte si [vzor automatizovaného strojového učení s vysvětlením ukázkových poznámkových bloků](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
* Přečtěte si další informace o [zodpovědných nabídkách AI v automatizovaném ml](how-to-machine-learning-interpretability-automl.md).
* V případě specifických otázek pro automatizované ML se můžete obrátit na askautomatedml@microsoft.com .
