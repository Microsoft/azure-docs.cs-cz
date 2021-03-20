---
title: 1-versus-all Multiclass
titleSuffix: Azure Machine Learning
description: Naučte se používat modul 1-versus-all Multiclass v Návrháři Azure Machine Learning k vytvoření kompletu binárních modelů klasifikace.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 4dfe284a00052cbd1915d62355e1d7772f3712ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94591865"
---
# <a name="one-vs-all-multiclass"></a>1-versus-all Multiclass

Tento článek popisuje, jak používat modul 1-versus-all Multiclass v Návrháři Azure Machine Learning. Cílem je vytvořit klasifikační model, který může odhadnout více tříd pomocí přístupu typu *1:1* .

Tento modul je vhodný pro vytváření modelů, které předpovídá tři nebo více možných výsledků, pokud výsledek závisí na kontinuálním nebo kategorií proměnných prediktivních proměnných. Tato metoda také umožňuje použít binární metody klasifikace pro problémy, které vyžadují více výstupních tříd.

### <a name="more-about-one-versus-all-models"></a>Další informace o jednotlivých modelech – srovnání

Některé algoritmy klasifikace umožňují použití více než dvou tříd podle návrhu. Jiné omezují možné výsledky na jednu ze dvou hodnot (binární neboli model se dvěma třídami). Ale dokonce i algoritmy binární klasifikace lze přizpůsobit pro úlohy klasifikace více tříd prostřednictvím celé řady strategií. 

Tento modul implementuje metodu 1:1, ve které je vytvořen binární model pro každou z více tříd Output. Modul posuzuje každý z těchto binárních modelů pro jednotlivé třídy proti svému doplňku (všechny ostatní třídy v modelu), jako by se jednalo o binární problém klasifikace. Kromě výpočetní efektivity (je potřeba použít jenom `n_classes` klasifikátory) je jednou z výhod tohoto přístupu jeho výklad. Vzhledem k tomu, že každá třída je zastoupena pouze jedním a jedním klasifikátorem, je možné získat znalosti o třídě kontrolou jejího odpovídajícího třídění. Toto je nejčastěji používaná strategie pro klasifikaci více tříd a je to reálná výchozí volba. Modul pak provede předpověď spuštěním těchto binárních klasifikátorů a výběrem předpovědi s nejvyšším skóre spolehlivosti. 

V podstatě modul vytvoří komplet jednotlivých modelů a potom sloučí výsledky pro vytvoření jednoho modelu, který předpovídá všechny třídy. Každý binární klasifikátor lze použít jako základ pro model s jedním i všemi.  

Řekněme například, že nakonfigurujete vícevrstvý model [strojového modelu podpory](two-class-support-vector-machine.md) a poskytnete ho jako vstup do modulu 1-versus-All Multiclass. Modul vytvoří pro všechny členy třídy Output modely vektorového stroje podpory. Pak by se pak použila Metoda 1:1 pro kombinování výsledků pro všechny třídy.  

Modul používá OneVsRestClassifier z skriptu sklearn a další podrobnosti si můžete přečíst [tady](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsRestClassifier.html).

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>Postup konfigurace klasifikátoru 1-versus-all Multiclass  

Tento modul vytvoří komplet binárních modelů klasifikace pro analýzu více tříd. Chcete-li použít tento modul, musíte nejprve nakonfigurovat a vytvořit *binární klasifikační* model. 

Binární model připojíte k modulu 1-versus-all Multiclass. Potom provedete sadu modelů pomocí [výukového modelu](train-model.md) s popisem školicích dat s popisky.

Při kombinaci modelů 1-versus-all Multiclass vytvoří více binárních klasifikačních modelů, optimalizuje algoritmus pro každou třídu a poté sloučí modely. Modul provádí tyto úlohy i v případě, že datová sada školení může mít více hodnot třídy.

1. Přidejte modul 1-versus-all Multiclass do kanálu v návrháři. Tento modul můžete najít v části **Machine Learning-Initialize** v kategorii **klasifikace** .

   Klasifikátor 1-versus-all Multiclass nemá vlastní konfigurovatelné parametry. Jakékoli vlastní nastavení musí být provedeno v binárním klasifikačním modelu, který je zadán jako vstup.

2. Přidejte do kanálu binární model klasifikace a nakonfigurujte tento model. Můžete například použít [dvě třídy podpora vektorového počítače](two-class-support-vector-machine.md) nebo se [dvěma třídami s posíleným rozhodovacím stromem](two-class-boosted-decision-tree.md).

3. Přidejte modul [vlakového modelu](train-model.md) do vašeho kanálu. Připojte nevýukový klasifikátor, který je výstupem 1-versus-all Multiclass.

4. Na ostatních vstupech [modelu vlaků](train-model.md)připojte popisek datové sady s popiskem, který má více hodnot třídy.

5. Odešlete kanál.

## <a name="results"></a>Výsledky

Po dokončení školení můžete použít model k vytvoření předpovědi pro více tříd.

Alternativně můžete předat nevýukový klasifikátor do [modelu křížového](cross-validate-model.md) ověření pro křížové ověřování proti popisku datové sady s popiskem.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
