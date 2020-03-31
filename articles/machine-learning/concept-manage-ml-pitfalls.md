---
title: Spravujte běžné úskalí modelu ML pomocí automatizovaného strojového učení.
titleSuffix: Azure Machine Learning
description: Identifikujte a spravujte běžné úskalí modelů ML pomocí automatizovaných řešení strojového učení Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 03/27/2020
ms.openlocfilehash: e0bc1aa48dfb40ea146fa79fdfd57da841ca1404
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385529"
---
# <a name="manage-ml-pitfalls-with-automated-machine-learning"></a>Správa úskalí ML pomocí automatizovaného strojového učení

Nadměrná a nevyvážená data jsou běžnými úskalími při vytváření modelů strojového učení. Ve výchozím nastavení poskytuje automatizované strojové učení Azure Machine Learning grafy a metriky, které vám pomohou tato rizika identifikovat, a implementuje osvědčené postupy, které jim pomohou je zmírnit. 

## <a name="identify-over-fitting"></a>Identifikovat nadměrné přizpůsobení

K nadměrné montáži ve strojovém učení dochází, když model odpovídá příliš dobře trénovacím datům a v důsledku toho nelze přesně předpovědět neviditelná testovací data. Jinými slovy, model má jednoduše zapamatováno konkrétní vzory a šum v trénovacích dat, ale není dostatečně flexibilní, aby předpovědi na skutečná data.

Zvažte následující trénované modely a jejich odpovídající přesnosti vlaků a zkoušek.

| Model | Přesnost vlaku | Přesnost testu |
|-------|----------------|---------------|
| A | 99,9 % | 95% |
| B | 87% | 87% |
| C | 99,9 % | 45 % |

S ohledem na model **A**existuje běžná mylná představa, že pokud je přesnost testu na neviditelných datech nižší než přesnost trénování, je model přemontován. Přesnost zkoušek by však měla být vždy menší než přesnost tréninku a rozlišení pro nadměrné přizpůsobení vs. vhodně vhodné přizpůsobí se tomu, *o kolik* méně přesné. 

Při porovnávání modelů **A** a **B**je model **A** lepším modelem, protože má vyšší přesnost testu, a přestože je přesnost testu mírně nižší na 95 %, není významný rozdíl, který by naznačoval, že je přítomen přemontování. Model **B** byste si nevybrali jen proto, že přesnost vlaku a testu jsou blíže k sobě.

Model **C** představuje jasný případ nadměrnémontáže; přesnost tréninku je velmi vysoká, ale přesnost testu není ani zdaleka tak vysoká. Tento rozdíl je subjektivní, ale pochází ze znalosti vašeho problému a dat, a jaké velikosti chyb jsou přijatelné.

## <a name="prevent-over-fitting"></a>Zabraňte nadměrnémontáži

V nejkřiklavějších případech bude přemontovaný model předpokládat, že kombinace hodnot prvku, které se zobrazí během tréninku, budou vždy mít za následek přesně stejný výstup pro cíl.

Nejlepší způsob, jak zabránit nadměrnému přizpůsobení, je dodržovat osvědčené postupy ML, včetně:

* Použití více trénovacích dat a eliminace statistické hojné zkreslení
* Zabránění úniku cíle
* Používání méně funkcí
* **Optimalizace regularizace a hyperparametrů**
* **Omezení složitosti modelu**
* **Křížové ověření**

V kontextu automatické ml první tři výše uvedené položky jsou **osvědčené postupy, které implementujete**. Poslední tři tučné položky jsou **doporučené postupy automatické ML implementuje** ve výchozím nastavení k ochraně proti nadměrné montáže. V jiných nastaveních než v automatizovaném ml, všech šest osvědčených postupů stojí za to sledovat, aby se zabránilo over-montáž modely.

### <a name="best-practices-you-implement"></a>Osvědčené postupy, které implementujete

Použití **více dat** je nejjednodušší a nejlepší možný způsob, jak zabránit nadměrné montáži, a jako bonus obvykle zvyšuje přesnost. Při použití více dat, bude pro model obtížnější zapamatovat si přesné vzory a je nucen dosáhnout řešení, která jsou flexibilnější, aby vyhovovaly více podmínek. Je také důležité rozpoznat **statistické zkreslení**, aby vaše trénovací data neobsahovala izolované vzory, které nebudou existovat v datech živé předpovědi. Tento scénář může být obtížné vyřešit, protože nemusí být over-fitting mezi vlak a testovací sady, ale může být over-fitting přítomen ve srovnání s daty živé ho testu.

Únik cíle je podobný problém, kdy se nemusí zobrazit nadměrné zamontážmezi vlakovými/testovacími soupravami, ale spíše se objeví v době předpovědi. Cíl úniku dochází, když váš model "podvádí" během trénování tím, že přístup k datům, které by za normálních okolností nemají v době předpovědi. Pokud je například vaším problémem předpovědět v pondělí, jaká bude cena komodity v pátek, ale jedna z vašich funkcí náhodně zahrnovala data ze čtvrtka, to by byla data, která model nebude mít v době předpovědi, protože nevidí do budoucna. Únik cíle je snadnou chybou, kterou můžete přehlédnout, ale často se vyznačuje abnormálně vysokou přesností vašeho problému. Pokud se pokoušíte předpovědět cenu akcií a vyškolil model s 95% přesností, je pravděpodobné, že cíl únik někde ve vašich funkcích.

Odebrání prvků může také pomoci při nadměrném přizpůsobení tím, že zabrání modelu, aby měl příliš mnoho polí, aby se zapamatovaly konkrétní vzory, což způsobí, že bude flexibilnější. Může být obtížné měřit kvantitativně, ale pokud můžete odstranit prvky a zachovat stejnou přesnost, pravděpodobně jste model učinili flexibilnějším a snížili riziko nadměrnémontáže.

### <a name="best-practices-automated-ml-implements"></a>Osvědčené postupy automatizované ml implementuje

Regularizace je proces minimalizace funkce nákladů k penalizaci složitých a nadměrně vybavených modelů. Existují různé typy funkcí regularizace, ale obecně všechny penalizují velikost, odchylku a složitost modelu. Automatizované ML používá L1 (Laso), L2 (Ridge) a ElasticNet (L1 a L2 současně) v různých kombinacích s různými nastaveními hyperparametrů modelu, které řídí nadměrné přizpůsobení. Jednoduše řečeno, automatizovaná ml se bude lišit, kolik model je regulován a zvolit nejlepší výsledek.

Automatizovaná funkce ML také implementuje explicitní omezení složitosti modelu, aby se zabránilo nadměrnému přizpůsobení. Ve většině případů je tato implementace specificky pro rozhodovací strom nebo lesní oborové algoritmy, kde je omezená maximální hloubka jednotlivých stromů a celkový počet stromů používaných v doménové struktuře nebo technikách souboru je omezen.

Křížové ověření (CV) je proces s mnoha podmnožiny vašich úplných trénovacích dat a trénování modelu na každé podmnožině. Myšlenka je, že model by mohl dostat "štěstí" a mají velkou přesnost s jednou podmnožinou, ale pomocí mnoha podmnožiny modelu nebude dosáhnout této vysoké přesnosti pokaždé. Při provádění cv, můžete zadat ověření holdout datové sady, zadejte životopis záhyby (počet podmnožiny) a automatizované ML bude trénovat váš model a vyladit hyperparameters minimalizovat chyby na vaší sady ověření. Jeden cv záhyb může být příliš fit, ale použitím mnoha z nich snižuje pravděpodobnost, že váš konečný model je přes-fit. Kompromis je, že životopis má za následek delší tréninkové časy a tím i vyšší náklady, protože místo školení modelu jednou, trénujete jej jednou pro každou *podmřadu n* CV. 

> [!NOTE]
> Křížové ověřování není ve výchozím nastavení povoleno. musí být konfigurován v automatickém nastavení ML. Po konfiguraci křížového ověření a poskytnutí ověřovací ho souboru dat je však proces automatizován za vás. Seznamte se s  

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Identifikace modelů s nevyváženými daty

Nevyvážená data se běžně vyskytují v datech pro scénáře klasifikace strojového učení a odkazuje na data, která obsahuje nepřiměřený poměr pozorování v každé třídě. Tato nerovnováha může vést k falešně vnímanému pozitivnímu efektu přesnosti modelu, protože vstupní data mají předpojatost vůči jedné třídě, což vede k tomu, že trénovaný model napodobuje toto zkreslení. 

Jako klasifikační algoritmy jsou běžně vyhodnocovány přesností, kontrola skóre přesnosti modelu je dobrý způsob, jak zjistit, zda byl ovlivněn nevyváženými daty. Měl opravdu vysokou přesnost nebo opravdu nízkou přesnost pro určité třídy?

Automatizované spuštění ml navíc automaticky generuje následující grafy, které vám pomohou pochopit správnost klasifikací modelu a identifikovat modely potenciálně ovlivněné nevyváženámi daty.

Graf| Popis
---|---
[Matice záměny](how-to-understand-automated-ml.md#confusion-matrix)| Vyhodnotí správně klasifikované popisky proti skutečné popisky dat. 
[Přesné vyvolání](how-to-understand-automated-ml.md#precision-recall-chart)| Vyhodnotí poměr správných popisků k poměru nalezených instancí popisků dat. 
[Roc křivky](how-to-understand-automated-ml.md#roc)| Vyhodnotí poměr správných popisků k poměru falešně pozitivních popisků.

## <a name="handle-imbalanced-data"></a>Zpracování nevyvážených dat 

V rámci svého cíle zjednodušit pracovní postup strojového učení má automatizovaná ml vestavěné funkce, které pomáhají řešit nevyvážená data, jako jsou 

- **Sloupec váhy**: automatizovaný ml podporuje vážený sloupec jako vstup, což způsobuje, že řádky v datech mají být váženy nahoru nebo dolů, což může třídu více či méně "důležité".

- Algoritmy používané automatizované ML může správně zpracovat nerovnováhu až 20:1, což znamená, že nejběžnější třída může mít 20 krát více řádků v datech než nejméně běžné třídy.

Následující techniky jsou další možnosti pro zpracování nevyvážených dat mimo automatizované ML. 

- Převzorkování na i třídy nerovnováhy, buď nahoru vzorkování menší třídy nebo dolů vzorkování větší třídy. Tyto metody vyžadují odborné znalosti pro zpracování a analýzu.

- Použijte metriku výkonu, která lépe řeší nevyvážená data. Například skóre F1 je vážený průměr přesnosti a odvolání. Přesnost měří přesnost klasifikátoru-- nízká přesnost naznačuje vysoký počet falešných poplachů--, zatímco odvolání měří úplnost klasifikátoru-- nízké odvolání naznačuje vysoký počet falešných negativů. 

## <a name="next-steps"></a>Další kroky

Podívejte se na příklady a zjistěte, jak vytvářet modely pomocí automatizovaného strojového učení:

+ Postupujte [podle kurzu: Automatické trénování regresního modelu pomocí Azure Machine Learning](tutorial-auto-train-models.md)

+ Konfigurace nastavení pro automatický tréninkový experiment:
  + V Azure Machine Learning studio, [použijte tyto kroky](how-to-use-automated-ml-for-ml-models.md).
  + Pomocí sady Python SDK [postupujte takto](how-to-configure-auto-train.md).


