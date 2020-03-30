---
title: One-vs-All Multiclass
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu One-vs-All Multiclass v Azure Machine Learning vytvořit vícetřídní klasifikační model ze souboru binárních klasifikačních modelů.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 29934758ab729e0fb888c10b7f834da3d0bf7fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456076"
---
# <a name="one-vs-all-multiclass"></a>One-vs-All Multiclass

Tento článek popisuje, jak používat modul One-vs-All Multiclass v návrháři Azure Machine Learning (preview). Cílem je vytvořit klasifikační model, který může předpovědět více tříd pomocí přístupu *jeden proti všem.*

Tento modul je užitečný pro vytváření modelů, které předpovídají tři nebo více možných výsledků, když výsledek závisí na proměnných průběžného nebo kategorického prediktoru. Tato metoda také umožňuje použít binární metody klasifikace pro problémy, které vyžadují více výstupních tříd.

### <a name="more-about-one-versus-all-models"></a>Více o modelech one-versus-all

Některé klasifikační algoritmy umožňují použití více než dvou tříd podle návrhu. Jiné omezit možné výsledky na jednu ze dvou hodnot (binární nebo dvoutřídní model). Ale i binární klasifikační algoritmy mohou být přizpůsobeny pro vícetřídní klasifikační úkoly prostřednictvím různých strategií. 

Tento modul implementuje metodu one-versus-all, ve které je vytvořen binární model pro každou z více výstupních tříd. Modul vyhodnotí každý z těchto binárních modelů pro jednotlivé třídy proti jeho komplementu (všechny ostatní třídy v modelu), jako by se jednalo o problém binární klasifikace. Modul pak provede předpověď spuštěním těchto binárnítřídění a výběrem předpověď s nejvyšší skóre spolehlivosti.  

V podstatě modul vytvoří soubor jednotlivých modelů a pak sloučí výsledky, chcete-li vytvořit jeden model, který předpovídá všechny třídy. Všechny binární třídění lze použít jako základ pro jeden versus všechny modelu.  

Řekněme například, že nakonfigurujete model [dvoutřídového podpůrného zařízení](two-class-support-vector-machine.md) a poskytnete jej jako vstup do modulu One-vs-All Multiclass. Modul by vytvořit dvoutřídní podporu vektorové modely strojů pro všechny členy výstupní třídy. Potom by použít one-versus-all metoda kombinovat výsledky pro všechny třídy.  

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>Jak nakonfigurovat klasifikátor One-vs-All Multiclass  

Tento modul vytvoří soubor binární chod klasifikačních modelů pro analýzu více tříd. Chcete-li použít tento modul, musíte nejprve nakonfigurovat a trénovat *binární klasifikační* model. 

Binární model se připojí k modulu One-vs-All Multiclass. Potom trénovat soubor modelů pomocí [Train Model](train-model.md) s označenou trénovací datové sady.

Když zkombinujete modely, One-vs-All Multiclass vytvoří více binárních klasifikačních modelů, optimalizuje algoritmus pro každou třídu a pak sloučí modely. Modul provádí tyto úkoly, i když trénovací datová sada může mít více hodnot třídy.

1. Přidejte modul One-vs-All Multiclass do kanálu v návrháři. Tento modul najdete v části **Strojové učení – inicializace**v kategorii **Klasifikace.**

   Klasifikátor One-vs-All Multiclass nemá vlastní konfigurovatelné parametry. Všechny vlastní nastavení musí být provedeno v binární klasifikační model, který je k dispozici jako vstup.

2. Přidejte binární klasifikační model do kanálu a nakonfigurujte tento model. Můžete například použít [dvoutřídní podpůrný vektorový stroj](two-class-support-vector-machine.md) nebo [dvoutřídný posílený rozhodovací strom](two-class-boosted-decision-tree.md).

3. Přidejte modul [Model vlaku](train-model.md) do kanálu. Připojte netrénovaný třídění, které je výstupem One-vs-All Multiclass.

4. Na druhý vstup [Train Model](train-model.md), připojte popisek trénovací datové sady, která má více hodnot třídy.

5. Odešlete potrubí.

## <a name="results"></a>Výsledky

Po dokončení školení můžete použít model k provedení vícetřídní předpovědi.

Alternativně můžete předat netrénovaný třídění [pro křížovou ověření modelu](cross-validate-model.md) pro křížové ověření proti popiskované ověřovací datové sady.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
