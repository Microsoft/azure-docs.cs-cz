---
title: Vyhněte se přebudování & nevyvážených dat pomocí AutoML
titleSuffix: Azure Machine Learning
description: Identifikujte a spravujte běžné nástrah modelů ML pomocí automatizovaných řešení strojového učení Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/09/2020
ms.openlocfilehash: ba85bda1e322d3efd467527b48bd4cd90eb7ce8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96922618"
---
# <a name="prevent-overfitting-and-imbalanced-data-with-automated-machine-learning"></a>Ochrana před přeložením a nevyváženými daty pomocí automatizovaného strojového učení

Při sestavování modelů strojového učení jsou běžné nástrah a nevyvážená data. Ve výchozím nastavení poskytuje automatizované Machine Learning Azure Machine Learning grafy a metriky, které vám pomůžou tato rizika identifikovat, a implementuje osvědčené postupy, které jim pomůžou je zmírnit. 

## <a name="identify-over-fitting"></a>Identifikace přebudování

K naplnění ve strojovém učení dochází, když model přesně odpovídá školicím datům, a v důsledku toho nemůže přesně odhadnout data nefungujících testů. Jinými slovy, model jednoduše zapamatovaných konkrétní vzorky a šum ve školicích datech, ale není dostatečně flexibilní, aby předpovědi na skutečná data.

Vezměte v úvahu následující vyškolené modely a jejich odpovídající přesností a testování.

| Modelování | Přesnost vlaku | Přesnost testu |
|-------|----------------|---------------|
| A | 99,9 % | 95 % |
| B | 87% | 87% |
| C | 99,9 % | 45 % |

Zvažujeme model **a**, pokud je přesnost testu u nezobrazených dat nižší než přesnost školení, model je přemontovaný. Přesnost testu by však měla být vždy menší než přesnost školení a rozlišení pro převzetí služeb při selhání, které je vhodné, bude mít za *cíl méně přesné* . 

Při porovnávání modelů **a** a **B** model **a** je lepší model, protože má vyšší přesnost testování, i když je přesnost testu mírně nižší v 95%, nejedná se o významný rozdíl, který navrhuje přemístění, je k dispozici. Model **B** byste nezvolili jednoduše proto, že vlak a test přesností jsou bližší dohromady.

Model **C** představuje nejasný případ přeložení; přesnost školení je velmi vysoká, ale přesnost testu není nikde blízko nejvyšší úrovně. Tento rozdíl je subjektivní, ale pochází ze znalostí o vašem problému a datech a o tom, jaké množství chyb je přijatelné.

## <a name="prevent-over-fitting"></a>Zabránit přebudování

Ve většině případů egregious bude převedený model předpokládat, že kombinace hodnot funkcí zobrazených během školení vždy způsobí přesný stejný výstup pro cíl.

Nejlepším způsobem, jak zabránit přebudování, je sledovat osvědčené postupy ML, včetně těchto:

* Používání dalších školicích dat a odstraňování statistického posunu
* Zabránění úniku cíle
* Použití méně funkcí
* **Pravidelná optimalizace a optimalizace parametrů**
* **Omezení složitosti modelu**
* **Křížové ověření**

V kontextu automatizovaného ML jsou první tři výše uvedené položky **osvědčené postupy, které implementujete**. Poslední tři tučné položky jsou **osvědčenými postupy automatizovaného** STANDARDu ml, které se ve výchozím nastavení implementují k ochraně před umísťováním. V jiných nastaveních než automatizované ML se za každých šest osvědčených postupů doporučuje, abyste se vyhnuli modelům přebudování.

### <a name="best-practices-you-implement"></a>Osvědčené postupy, které implementujete

Použití **většího množství dat** je nejjednodušší a nejlepší možný způsob, jak zabránit převzetí služeb při selhání, protože přidaný bonus obvykle zvyšuje přesnost. Když použijete více dat, bude model mnohem obtížnější, aby nepamatují přesné vzory a bylo nuceno dosáhnout řešení, která jsou pružnější, aby se vešly další podmínky. Je také důležité rozpoznat **statistickou bias**, abyste zajistili, že vaše školicí data neobsahují izolované vzory, které neexistují v datech živé předpovědi. Tento scénář může být obtížné vyřešit, protože nemůžete překládat mezi vlakem a sadami testů, ale ve srovnání s živými testovacími daty může být přítomno více.

**Netěsnost cíle** je podobný problém, ve kterém se možná nezobrazuje přemístění mezi sadami vlaků a testování, ale místo toho se zobrazí v době předpovědi. K úniku cíle dojde, když během školení model "podvádět" použijete přístup k datům, která by neměla být normálně v době předpovědi. Například pokud váš problém má předpovídat pondělí, jakou má cena za komoditu v pátek, ale jedna z vašich funkcí omylem zahrnovala data z čtvrtek, která by byla data modelu, nebude mít v čase předpovědi, protože nemůže být v budoucnu. Prosakování cíle představuje snadnou chybu, ale často je charakterizována neobvyklou vysokou přesností pro váš problém. Pokud se pokoušíte odhadnout cenu akcií a vyškolený model s 95% přesností, je pravděpodobná netěsné úniky do vašich funkcí.

**Odebrání funkcí** může také pomáhat s převzetím, protože brání modelu v použití příliš velkého množství polí k nepamatujíí specifických vzorů, což způsobí, že bude pružnější. Může být obtížné změřit kvantitativní měření, ale pokud můžete odebrat funkce a zachovat stejnou přesnost, pravděpodobně jste model lépe flexibilní a snížili rizika při přebudování.

### <a name="best-practices-automated-ml-implements"></a>Osvědčené postupy automatizované implementace ML

**Pravidelná** operace je proces minimalizace nákladové funkce k postihu složitých a namontovaných modelů. Existují různé typy probíhající funkce, ale obecně všechny postihují velikost modelu, rozptyl a složitost. Automatizované ML používá L1 (laso), L2 (Ridge) a ElasticNet (L1 a L2 současně) v různých kombinacích s různými nastaveními parametrů modelu, které ovládají přizpůsobení. V jednoduchých výrazech se automatizovaná ML bude lišit v tom, kolik modelu je regulováno, a zvolit nejlepší výsledek.

Automatizované ML také implementuje explicitní **omezení složitosti modelu** , aby se zabránilo přebudování. Ve většině případů tato implementace slouží konkrétně pro rozhodovací strom nebo algoritmy doménové struktury, přičemž maximální hloubka jednotlivého stromu je omezená a celkový počet stromů používaných v doménové struktuře nebo v technikech kompletování jsou omezené.

**Křížové ověření (CV)** je proces, který zabírá mnoho podmnožin vašich úplných školicích dat a školení modelu v každé podmnožině. Nápad je, že model by mohl získat "štěstí" a mít skvělou přesnost s jednou podmnožinou, ale s využitím mnoha dílčích sad model nedosáhne pokaždé, když se vyhodnotí. Při provádění CV zadáte datovou sadu pro blokování ověřování, určíte vaše přeložená omezení (počet podmnožin) a automatizovaná ML nasadí váš model a optimalizuje parametry pro minimalizaci chyby v sadě ověřování. Jedno přeložení CV může být příliš velké, ale s využitím mnoha z nich snižuje pravděpodobnost, že je finální model vyhodnocený. Kompromisy jsou v tom, že CV má za následek delší dobu školení, takže větší náklady, protože místo toho, abyste model provedli jednou, je pro každou *n* -podmnožinu CV jednou vyškolí. 

> [!NOTE]
> Křížové ověření není ve výchozím nastavení povolené. musí být nakonfigurovaný v nastavení automatizovaného ML. Po nakonfigurování křížového ověření a zadání ověřovací sady dat je však proces automatizován. Další informace o [konfiguraci vzájemného ověřování najdete v automatickém ml](how-to-configure-cross-validation-data-splits.md) .

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Identifikace modelů s nevyváženými daty

Nevyvážená data se běžně nacházejí v datech pro scénáře klasifikace Machine Learning a odkazují na data, která obsahují neúměrný poměr pozorování v každé třídě. Tato nerovnováha může vést k falešně vnímanému kladnému účinku přesnosti modelu, protože vstupní data mají posun směrem k jedné třídě, což vede k vyškolený model k napodobení tohoto posunu. 

Automatizované spuštění ML navíc generuje následující grafy automaticky, což vám pomůže pochopit správnost klasifikací modelu a identifikovat modely, které mohou ovlivnit data, která jsou potenciálně ovlivněná nevyváženými daty.

Graf| Description
---|---
[Nejasná matice](how-to-understand-automated-ml.md#confusion-matrix)| Vyhodnotí správně klasifikované popisky proti skutečným popiskům dat. 
[Přesnost – odvolání](how-to-understand-automated-ml.md#precision-recall-curve)| Vyhodnotí poměr správných popisků oproti poměru nalezených instancí popisků dat. 
[ROC – křivky](how-to-understand-automated-ml.md#roc-curve)| Vyhodnotí poměr správných jmenovek proti poměru falešně pozitivních popisků.

## <a name="handle-imbalanced-data"></a>Zpracovat nevyvážená data 

V rámci svého cíle zjednodušit pracovní postup strojového učení mají **automatizované funkce ml integrované možnosti** , které vám pomůžou se zabývat nevyváženými daty, jako je například, 

- **Sloupec váhy**: automatizovaná ml podporuje sloupec vah jako vstup, což způsobuje, že řádky v datech se mají navýšit nebo snížit, což lze použít k tomu, aby byla třída větší nebo menší "důležitá".

- Algoritmy používané automatizovanou nevyrovnanou odchylkou v době, kdy je počet vzorků v menšinové třídě rovný nebo méně než 20% počtu vzorků ve třídě většina, kde menšinová třída odkazuje na tu s nejmenšími vzorky a majoritní třídou, odkazuje na tu s většinou vzorků. Následně AutoML spustí experiment s dílčími ukázkovými daty, aby zkontroloval, jestli by použití vah třídy napravilo tento problém a vylepšilo výkon. Pokud se v tomto experimentu zjistí lepší výkon, Tato náprava se použije.

- Využijte metriku výkonu, která se bude lépe dopracovat s nevyváženými daty. Například AUC_weighted je primární metrika, která vypočítá příspěvek každé třídy na základě relativního počtu vzorků představujících tuto třídu, proto je větší odolnost proti nevyrovnanosti.

Následující techniky jsou další možnosti pro zpracování nevyvážených dat **mimo automatizované ml**. 

- Převzorkování na dokonce i nevyrovnanost třídy, a to buď pomocí vzorkování menších tříd, nebo dolů vyvzorkováním větších tříd. Tyto metody vyžadují, aby byly zkušenosti se zpracováním a analýzou.

- Zkontrolujte metriky výkonu pro nevyvážená data. Například skóre F1 je harmonickým průměrem přesnosti a odvolání. Přesnost měří přesnost třídění, kde větší přesnost označuje méně falešně pozitivních hodnot a při odvolání měří úplnost klasifikátoru, kde větší počet chyb značí méně falešně negativních hodnot.

## <a name="next-steps"></a>Další kroky

Podívejte se na příklady a Naučte se vytvářet modely pomocí automatizovaného strojového učení:

+ Postupujte podle [kurzu: Automatické učení regresního modelu s Azure Machine Learning](tutorial-auto-train-models.md)

+ Nakonfigurujte nastavení pro experiment automatických školení:
  + V Azure Machine Learning Studiu [použijte tento postup](how-to-use-automated-ml-for-ml-models.md).
  + Pomocí sady Python SDK [použijte následující postup](how-to-configure-auto-train.md).


