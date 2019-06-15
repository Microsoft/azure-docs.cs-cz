---
title: 'Two-Class logistické regrese: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu Two-Class logistické Regrese ve službě Azure Machine Learning vytvořte Logistický regresní model, který můžete použít k predikci výsledků dvou (a pouze dvě).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: aacaf6c64ef77d0e694f97e3675060eca33794ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029247"
---
# <a name="two-class-logistic-regression-module"></a>Modul logistické regrese Two-Class

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k vytvoření modelu logistické regrese, který můžete použít k predikci výsledků dvou (a pouze dvě). 

Logistické regrese je dobře známé statistické technika, který se používá pro modelování různé druhy problémů. Je tento algoritmus *pod dohledem learning* metody.  Proto je třeba zadat datovou sadu, která už obsahuje výsledky pro trénování modelu.  

### <a name="about-logistic-regression"></a>O logistické regrese  

Logistické regrese je známá metoda statistiku, který se používá k předpovědi pravděpodobnosti nejistým a je zvláště oblíbených úloh klasifikace. Algoritmus předpovídá pravděpodobnost výskytu události montáží data logistické funkci.
  
V tomto modulu je klasifikační algoritmus optimalizovaná pro proměnné dichotomous nebo binary. Pokud potřebujete ke klasifikaci více výsledků, použijte [logistické regrese Multiclass](./multiclass-logistic-regression.md) modulu.

##  <a name="how-to-configure"></a>Postup konfigurace  

K trénování tento model, je nutné zadat datovou sadu, která obsahuje sloupec popisek nebo třídy. Protože tento modul je určená pro problémy s dvěma třídami, popisek nebo třída sloupec musí obsahovat přesně dvě hodnoty. 

Například popisek sloupce může být [hlasovali] s možných hodnot "Ano" nebo "Ne". Nebo může být [úvěrové riziko] s možných hodnot "Vysoká" nebo "Nízká". 
  
1.  Přidat **Two-Class logistické regrese** modulu do experimentu.  
  
2.  Určení způsobu modelu zaškolení, tak, že nastavíte **režimu vytváření trainer** možnost.  
  
    -   **Jeden parametr**: Pokud víte, jak chcete provést konfiguraci modelu, můžete zadat konkrétní sadu hodnot jako argumenty.  
  
3.  Pro **optimalizace tolerance**, zadejte prahovou hodnotu pro použití při optimalizaci modelu. Pokud zlepšení mezi iteracemi klesne pod zadanou prahovou hodnotu, algoritmus je považován za mít konvergované na řešení a zastaví školení.  
  
4.  Pro **L1 regularizace váha** a **L2 regularizace váha**, zadejte hodnotu pro parametry regularizace L1 a L2. Nenulové hodnoty se doporučuje pro obojí.  
  
     *Regularizace* je metoda kvůli overfitting podle ukládání sankcí modely s koeficient extrémní hodnoty. Regularizace funguje tak, že přidáte sankce, který je spojen s hodnotami koeficient chybu hypotézu. Proto přesný model s extrémně koeficient hodnoty by penalizován víc, ale méně přesný model s konzervativnější hodnoty by penalizován méně.  
  
     L1 a míry regularizace L2 mají různé účinky a používá.  
  
    -   L1 lze použít k modelům zhuštěný, což je užitečné při práci s vysokým počtem rozměrů data.  
  
    -   Naproti tomu regularizace L2 je vhodnější pro data, která není zhuštěné.  
  
     Tento algoritmus podporuje lineární kombinace hodnot regularizace L1 a L2: to znamená, pokud <code>x = L1</code> a <code>y = L2</code>, pak <code>ax + by = c</code> definuje lineární rozsah podmínky regularizace.  
  
    > [!NOTE]
    >  Chcete se dozvědět víc o L1 a L2 regularizace? Následující článek obsahuje informace o tom, jak se liší L1 a L2 regularizace a jejich vliv přizpůsobení modelu pomocí ukázky kódu pro logistické regrese a modely neuronových sítí:  [L1 a míry Regularizace L2 pro Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Našli různé kombinace lineární L1 a L2 podmínek pro logistické regresní modely: například [elastické net regularizace](https://wikipedia.org/wiki/Elastic_net_regularization). Doporučujeme odkazují na tyto kombinace k určení kombinace lineární, která platí ve vašem modelu.
      
5.  Pro **velikost paměti pro L-BFGS**, zadejte množství paměti pro *L-BFGS* optimalizace.  
  
     L-BFGS zastupuje "omezenou pamětí Broyden Fletcher Goldfarb Shanno". Je optimalizace algoritmus, který se používá pro parametr odhadu. Tento parametr označuje počet posledních pozic a přechody pro ukládání pro výpočet na další krok.  
  
     Tento parametr optimalizace omezuje množství paměti, která se používá k výpočtu dalšího kroku a směr. Pokud určíte méně paměti, školení je rychlejší, ale méně přesné.  
  
6.  Pro **náhodná počáteční hodnota čísla**, zadejte celočíselnou hodnotu. Definuje počáteční hodnoty je důležité, pokud chcete výsledky bude reprodukovatelné přes více běhů tom stejném experimentu.  
  
  
8. Do experimentu přidat označený datové sady a připojení mezi [školicí moduly](module-reference.md).  
  
    -   Pokud nastavíte **režimu vytváření trainer** k **jediný parametr**, použijte [trénování modelu](./train-model.md) modulu.  
  
9. Spusťte experiment.  
  
## <a name="results"></a>Výsledky

Po dokončení školení:

+ Chcete-li zobrazit souhrn parametry modelu, společně s funkcí váhy získané při školení, klikněte pravým tlačítkem na výstup [Train Model](./train-model.md) a vyberte **vizualizovat**.   
  
+ K vytvoření predikcí nová data, použijte jako vstup pro trénovaný model a nových dat [Score Model](./score-model.md) modulu. 


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 