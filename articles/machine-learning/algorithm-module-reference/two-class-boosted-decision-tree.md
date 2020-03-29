---
title: 'Dvoutřídní posílený rozhodovací strom: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Two-Class Boosted Decision Tree v Azure Machine Learning vytvořit model strojového učení, který je založený na algoritmu posílené rozhodovací stromy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 1d144a48f79e59b35c88c5b338747d3186ebceda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920735"
---
# <a name="two-class-boosted-decision-tree-module"></a>Modul dvoutřídního posíleného rozhodovacího stromu

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k vytvoření modelu strojového učení, který je založen na algoritmu boosted rozhodovací stromy. 

Posílený rozhodovací strom je metoda učení souboru, ve které druhý strom opravuje chyby prvního stromu, třetí strom opravuje chyby prvního a druhého stromu a tak dále.  Předpovědi jsou založeny na celém souboru stromů dohromady, který dělá předpověď.
  
Obecně platí, že při správné konfiguraci, posílené rozhodovací stromy jsou nejjednodušší metody, pomocí kterých získat nejvyšší výkon na širokou škálu úloh strojového učení. Jsou však také jedním z více paměťově náročných studentů a současná implementace má vše v paměti. Proto model posíleného rozhodovacího stromu nemusí být schopen zpracovat velké datové sady, které mohou zpracovat někteří lineární studenti.

## <a name="how-to-configure"></a>Jak nakonfigurovat

Tento modul vytvoří netrénovaný klasifikační model. Vzhledem k tomu, že klasifikace je metoda učení pod dohledem, potřebujete k trénování modelu *tagovařovnou datovou sadu,* která obsahuje sloupec popisku s hodnotou pro všechny řádky.

Tento typ modelu můžete trénovat pomocí [train modelu](././train-model.md). 

1.  V Azure Machine Learning přidejte do kanálu **posílený rozhodovací strom.**
  
2.  Určete, jak má být model trénovaný, nastavením **možnosti Vytvořit režim trenažéru.**
  
    + **Jeden parametr**: Pokud víte, jak chcete nakonfigurovat model, můžete zadat určitou sadu hodnot jako argumenty.
  
    + **Rozsah parametrů**: Pokud si nejste jisti nejlepšími parametry, můžete najít optimální parametry pomocí modulu [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Zadáte určitý rozsah hodnot a trenér iterates přes více kombinací nastavení k určení kombinace hodnot, které vytváří nejlepší výsledek.
  
3.  Pro **maximální počet listů na strom**, uveďte maximální počet koncových uzlů (listů), které lze vytvořit v libovolném stromu.
  
     Zvýšením této hodnoty můžete potenciálně zvětšit velikost stromu a získat lepší přesnost s rizikem nadměrného vybavení a delší doby tréninku.
  
4.  Pro **minimální počet vzorků na uzel listu**, uveďte počet případů potřebných k vytvoření libovolného koncového uzlu (listu) ve stromu.  
  
     Zvýšením této hodnoty zvýšíte prahovou hodnotu pro vytváření nových pravidel. Například s výchozí hodnotou 1 může i jeden případ způsobit vytvoření nového pravidla. Pokud zvýšíte hodnotu na 5, data školení by musel obsahovat alespoň pět případů, které splňují stejné podmínky.
  
5.  Do **pole Učení**zadejte číslo mezi 0 a 1, které definuje velikost kroku při učení.  
  
     Rychlost učení určuje, jak rychle nebo pomalu se student konverguje s optimálním řešením. Pokud je velikost kroku příliš velká, můžete přestřelit optimální řešení. Pokud je velikost kroku příliš malá, školení trvá déle, než se sbíhají na nejlepší řešení.
  
6.  V **části Počet vytvořených stromů**uveďte celkový počet rozhodovacích stromů, které mají být vytvořeny v souboru. Vytvořením více rozhodovacích stromů můžete potenciálně získat lepší pokrytí, ale doba tréninku se zvýší.
  
     Tato hodnota také určuje počet stromů zobrazených při vizualizaci trénovaného modelu. Pokud chcete zobrazit nebo vytisknout jeden strom, nastavte hodnotu na 1. Pokud tak však učiníte, je vytvořen pouze jeden strom (strom s počáteční sadou parametrů) a žádné další iterace jsou prováděny.
  
7.  Pro **osivo náhodného čísla**volitelně zadejte nezáporné celé číslo, které se použije jako náhodná hodnota osiva. Určení osiva zajišťuje reprodukovatelnost napříč spuštěními, které mají stejná data a parametry.  
  
     Náhodné osivo je ve výchozím nastavení nastaveno na hodnotu 0, což znamená, že počáteční hodnota osiva je získána ze systémových hodin.  Postupné spuštění pomocí náhodného osiva může mít různé výsledky.
  

9. Trénujte model.
  
    + Pokud nastavíte **režim Vytvořit trenér** na jeden **parametr**, připojte tagovované datové sady a modul [model vlaku.](./train-model.md)  
   
## <a name="results"></a>Výsledky

Po dokončení tréninku:

+ Chcete-li uložit snímek trénovaného modelu, vyberte kartu **Výstupy** v pravém panelu modulu **modelu Vlak.** Vyberte ikonu **Registrovat datovou sadu,** chcete-li model uložit jako opakovaně použitelný modul.

+ Chcete-li použít model pro vyhodnocování, přidejte modul **Modelu skóre** do kanálu.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 