---
title: Jedna a jedna více tříd
titleSuffix: Azure Machine Learning
description: Naučte se používat modul více tříd s více třídami v Azure Machine Learning k vytvoření modelu klasifikace s více třídami ze sady binárních modelů klasifikace.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 92ef5b372113d0d226335b8ec464a75a5a684bc3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94592902"
---
# <a name="one-vs-one-multiclass"></a>Jedna a jedna více tříd

Tento článek popisuje, jak v Návrháři Azure Machine Learning použít modul více tříd s více třídami. Cílem je vytvořit klasifikační model, který může odhadnout více tříd pomocí přístupu *1:1* .

Tento modul je vhodný pro vytváření modelů, které předpovídá tři nebo více možných výsledků, pokud výsledek závisí na kontinuálním nebo kategorií proměnných prediktivních proměnných. Tato metoda také umožňuje použít binární metody klasifikace pro problémy, které vyžadují více výstupních tříd.

### <a name="more-about-one-versus-one-models"></a>Další informace o modelech z jednoho versus po jednom

Některé algoritmy klasifikace umožňují použití více než dvou tříd podle návrhu. Jiné omezují možné výsledky na jednu ze dvou hodnot (binární neboli model se dvěma třídami). Ale dokonce i algoritmy binární klasifikace lze přizpůsobit pro úlohy klasifikace více tříd prostřednictvím celé řady strategií. 

Tento modul implementuje metodu 1:1, v níž je pro dvojici tříd vytvořen binární model. V době předpovědi je vybrána třída, která přijímá nejvíc hlasů. Vzhledem k tomu, že je nutné přizpůsobit `n_classes * (n_classes - 1) / 2` třídění, je tato metoda obvykle pomalejší než jedna z nich vzhledem ke složitosti O (n_classes ^ 2). Tato metoda ale může být výhodná pro algoritmy, jako jsou algoritmy jádra, u kterých se neškáluje dobře `n_samples` . Důvodem je to, že každý jednotlivý problém učení zahrnuje pouze malou podmnožinu dat, zatímco se stejnou datovou sadou používá kompletní datová sada `n_classes` .

V podstatě modul vytvoří komplet jednotlivých modelů a potom sloučí výsledky pro vytvoření jednoho modelu, který předpovídá všechny třídy. Každý binární klasifikátor lze použít jako základ pro model založený na jednom versus jednom.  

Řekněme například, že nakonfigurujete vícevrstvý model [strojového modelu podpory](two-class-support-vector-machine.md) a zadáte to jako vstup do modulu s více třídami a jedním. Modul vytvoří pro všechny členy třídy Output modely vektorového stroje podpory. Pak by se pak použila Metoda 1:1 pro kombinování výsledků pro všechny třídy.  

Modul používá OneVsOneClassifier z skriptu sklearn a další podrobnosti si můžete přečíst [tady](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsOneClassifier.html).

## <a name="how-to-configure-the-one-vs-one-multiclass-classifier"></a>Jak nakonfigurovat třídění více tříd – vs-One  

Tento modul vytvoří komplet binárních modelů klasifikace pro analýzu více tříd. Chcete-li použít tento modul, musíte nejprve nakonfigurovat a vytvořit *binární klasifikační* model. 

Binární model připojíte k modulu s více třídami a jedním. Potom provedete sadu modelů pomocí [výukového modelu](train-model.md) s popisem školicích dat s popisky.

Při kombinaci modelů jedna a více tříd vytvoří více binárních klasifikačních modelů, optimalizuje algoritmus pro každou třídu a poté sloučí modely. Modul provádí tyto úlohy i v případě, že datová sada školení může mít více hodnot třídy.

1. Přidejte do kanálu jeden modul s více třídami, a to v návrháři. Tento modul můžete najít v části **Machine Learning-Initialize** v kategorii **klasifikace** .

   Třídění více tříd s jedním vs-One nemá žádné konfigurovatelné parametry. Jakékoli vlastní nastavení musí být provedeno v binárním klasifikačním modelu, který je zadán jako vstup.

2. Přidejte do kanálu binární model klasifikace a nakonfigurujte tento model. Můžete například použít [dvě třídy podpora vektorového počítače](two-class-support-vector-machine.md) nebo se [dvěma třídami s posíleným rozhodovacím stromem](two-class-boosted-decision-tree.md).

3. Přidejte modul [vlakového modelu](train-model.md) do vašeho kanálu. Připojte nevýukový klasifikátor, který je výstupem jedné a více tříd.

4. Na ostatních vstupech [modelu vlaků](train-model.md)připojte popisek datové sady s popiskem, který má více hodnot třídy.

5. Odešlete kanál.

## <a name="results"></a>Výsledky

Po dokončení školení můžete použít model k vytvoření předpovědi pro více tříd.

Alternativně můžete předat nevýukový klasifikátor do [modelu křížového](cross-validate-model.md) ověření pro křížové ověřování proti popisku datové sady s popiskem.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
