---
title: 1-versus-all Multiclass
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul 1-versus-all Multiclass ve službě Azure Machine Learning k vytvoření modelu klasifikace s více třídami z množiny binárních klasifikačních modelů.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 9ded83dc5b8b8b98af66c8858214e8f82a4aa166
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518161"
---
# <a name="one-vs-all-multiclass"></a>1-versus-all Multiclass

Tento článek popisuje, jak použít modul **1-versus-All Multiclass** pro Azure Machine Learning Designer (Preview) k vytvoření klasifikačního modelu, který může odhadnout více tříd pomocí přístupu "One vs. All".

Tento modul je vhodný pro vytváření modelů, které předpovídá tři nebo více možných výsledků, pokud výsledek závisí na kontinuálním nebo kategorií proměnných prediktivních proměnných. Tato metoda také umožňuje použít binární metody klasifikace pro problémy, které vyžadují více výstupních tříd.

### <a name="more-about-one-vs-all-models"></a>Další informace o jednom a všech modelech

I když některé algoritmy klasifikace umožňují použití více než dvou tříd podle návrhu, jiné omezují možné výsledky na jednu ze dvou hodnot (binární typ nebo model dvou tříd). Dokonce i algoritmy binární klasifikace je však možné přizpůsobit pro úlohy klasifikace více tříd pomocí různých strategií. 

Tento modul implementuje *jednu metodu vs. All*, ve které je vytvořen binární model pro každou z více tříd Output. Každý z těchto binárních modelů pro jednotlivé třídy je hodnocen proti svému doplňku (všechny ostatní třídy v modelu), jako by se jednalo o binární problém klasifikace. Předpověď se pak provede spuštěním těchto binárních klasifikátorů a volbou předpovědi s nejvyšším stupněm spolehlivosti.  

V podstatě se vytvoří komplet jednotlivých modelů a výsledky se pak sloučí, aby se vytvořil jeden model, který předpovídá všechny třídy. Proto lze použít jakýkoli binární klasifikátor, který bude použit jako základ pro model založený na jednom vs-ALL.  

 Řekněme například, že nakonfigurujete vícevrstvý model [strojového modelu podpory](two-class-support-vector-machine.md) a poskytnete ho jako vstup do modulu **1-versus-All Multiclass** . Modul vytvoří pro všechny členy třídy Output modely pro vektorové modely podpory a pak použijte metodu s jednou a všemi pro kombinování výsledků pro všechny třídy.  

## <a name="how-to-configure-the-one-vs-all-classifier"></a>Postup konfigurace třídění One-vs-All  

Tento modul vytvoří komplet binárních modelů klasifikace pro analýzu více tříd. Proto pokud chcete použít tento modul, musíte nejdřív nakonfigurovat a vytvořit **binární klasifikační** model. 

Potom spojíte binární model s **1-versus-All Multiclass** modul a pomocí výukového [modelu](train-model.md) s popisem školicích dat výuky seřadíte sadu modelů.

Při kombinaci modelů, i když může mít datová sada pro školení více hodnot třídy, **1-versus-All Multiclass** vytvoří více binárních modelů klasifikace, optimalizuje algoritmus pro každou třídu a poté sloučí modely.

1. Přidejte **1-versus-All Multiclass** do kanálu v návrháři. Tento modul můžete najít v části Machine Learning-Initialize v kategorii **klasifikace** .

    Klasifikátor **1-versus-All Multiclass** nemá vlastní konfigurovatelné parametry. Jakékoli vlastní nastavení se musí provést v binárním klasifikačním modelu, který je poskytován jako vstup.

2. Přidejte do kanálu binární model klasifikace a nakonfigurujte tento model. Například můžete použít [Mezitřídní vektorový počítač](two-class-support-vector-machine.md) nebo se [dvěma třídami zesílený rozhodovací strom](two-class-boosted-decision-tree.md).

3. Přidejte modul [vlakového modelu](train-model.md) do svého kanálu a připojte nevýukový klasifikátor, který je výstupem **1-versus-All Multiclass**.

4. Na ostatních vstupech [modelu vlaků](train-model.md)spojte sadu dat s popiskem, která má více hodnot třídy.

5. Spuštění kanálu

## <a name="results"></a>Výsledky

Po dokončení školení můžete použít model k vytvoření předpovědi pro více tříd.

Alternativně můžete předat nevýukový klasifikátor do [modelu křížového](cross-validate-model.md) ověřování pro křížové ověřování proti označené sadě dat ověřování.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 
