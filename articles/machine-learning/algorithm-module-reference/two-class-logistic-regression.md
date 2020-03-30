---
title: 'Dvoutřídní logistická regrese: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu dvoutřídní logistické regrese v Azure Machine Learning vytvořit model logistické regrese, který lze použít k předvídání dvou (a pouze dvou) výsledků.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 7eb1ad00c3c947c3ed6d4ca450bddc0956a08d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455821"
---
# <a name="two-class-logistic-regression-module"></a>Dvoutřídní logistický regresní modul

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k vytvoření logistického regresního modelu, který lze použít k předvídání dvou (a pouze dvou) výsledků. 

Logistická regrese je dobře známá statistická technika, která se používá pro modelování mnoha druhů problémů. Tento algoritmus je *metoda učení pod dohledem;*  proto je nutné zadat datovou sadu, která již obsahuje výsledky pro trénování modelu.  

### <a name="about-logistic-regression"></a>O logistické regresi  

Logistická regrese je dobře známá metoda ve statistikách, která se používá k předvídání pravděpodobnosti výsledku a je obzvláště populární pro klasifikační úkoly. Algoritmus předpovídá pravděpodobnost výskytu události přizpůsobením dat do logistické funkce.
  
V tomto modulu je klasifikační algoritmus optimalizován pro dichotomické nebo binární proměnné. Pokud potřebujete klasifikovat více výsledků, použijte modul [Multiclass Logistic Regression.](./multiclass-logistic-regression.md)

##  <a name="how-to-configure"></a>Jak nakonfigurovat  

Chcete-li trénovat tento model, musíte zadat datovou sadu, která obsahuje popisek nebo sloupec třídy. Vzhledem k tomu, že tento modul je určen pro problémy dvou tříd, musí popisek nebo sloupec třídy obsahovat přesně dvě hodnoty. 

Sloupec popisku může být například [Hlasovalo] s možnými hodnotami "Ano" nebo "Ne". Nebo to může být [Úvěrové riziko], s možnými hodnotami "High" nebo "Low". 
  
1.  Přidejte do kanálu modul **Dvoutřídní logistické regrese.**  
  
2.  Určete, jak má být model trénovaný, nastavením **možnosti Vytvořit režim trenažéru.**  
  
    -   **Jeden parametr**: Pokud víte, jak chcete nakonfigurovat model, můžete zadat určitou sadu hodnot jako argumenty.  

    -   **Rozsah parametrů**: Pokud si nejste jisti nejlepšími parametry, můžete najít optimální parametry pomocí modulu [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Zadáte určitý rozsah hodnot a trenér iterates přes více kombinací nastavení k určení kombinace hodnot, které vytváří nejlepší výsledek.
  
3.  Pro **toleranci optimalizace**zadejte prahovou hodnotu, která se má použít při optimalizaci modelu. Pokud zlepšení mezi iterací klesne pod zadanou prahovou hodnotu, algoritmus se považuje za konvergované na řešení a školení zastaví.  
  
4.  Pro **hmotnost regularizace L1** a **hmotnost regularizace L2**zadejte hodnotu, která se použije pro parametry regularizace L1 a L2. Pro oba se doporučuje nenulová hodnota.  
     *Regularizace* je metoda, která zabraňuje nadměrnému vybavení penalizací modelů hodnotami s extrémními koeficienty. Regularizace funguje přidáním penalizace, která je spojena s hodnotami koeficientu k chybě hypotézy. Přesný model s hodnotami extrémních koeficientů by byl tedy penalizován více, ale méně přesný model s konzervativnějšími hodnotami by byl penalizován méně.  
  
     Legalizace L1 a L2 má různé účinky a použití.  
  
    -   L1 lze použít pro řídké modely, což je užitečné při práci s vysokorozměrnými daty.  
  
    -   Naproti tomu regularizace L2 je vhodnější pro data, která nejsou řídká.  
  
     Tento algoritmus podporuje lineární kombinaci hodnot regularizace L1 <code>x = L1</code> a <code>y = L2</code>L2: to znamená if a , pak <code>ax + by = c</code> definuje lineární rozsah termínů regularizace.  
  
    > [!NOTE]
    >  Chcete se dozvědět více o regularizaci L1 a L2? Následující článek obsahuje diskusi o tom, jak l1 a L2 regularizace se liší a jak ovlivňují přizpůsobení modelu, s ukázkami kódu pro logistické regrese a modely neuronové sítě: [L1 a L2 Regularization pro strojové učení](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Pro logistické regresní modely byly navrženy různé lineární kombinace termínů L1 a L2: například [elastická síťová regularizace](https://wikipedia.org/wiki/Elastic_net_regularization). Doporučujeme odkazovat na tyto kombinace definovat lineární kombinaci, která je účinná ve vašem modelu.
      
5.  Pro **velikost paměti pro L-BFGS**zadejte velikost paměti, která má být využita pro optimalizaci *L-BFGS.*  
  
     L-BFGS je zkratka pro "omezenou paměť Broyden-Fletcher-Goldfarb-Shanno". Jedná se o optimalizační algoritmus, který je populární pro odhad parametrů. Tento parametr označuje počet minulých pozic a přechodů, které mají být uskladněny pro výpočt u dalšího kroku.  
  
     Tento parametr optimalizace omezuje množství paměti, která se používá k výpočtu dalšího kroku a směru. Když zadáte méně paměti, školení je rychlejší, ale méně přesné.  
  
6.  Do **pole Osiva náhodného čísla**zadejte hodnotu celéčíslo. Definování hodnoty osiva je důležité, pokud chcete, aby výsledky reprodukovatelné přes více spuštění stejného kanálu.  
  
  
8. Přidejte tagovitu datovou sadu do kanálu a připojte jeden z [trénovacích modulů](module-reference.md).  
  
    -   Pokud nastavíte **vytvořit režim trenažéru** na **jeden parametr**, použijte modul [Model vlaku.](./train-model.md)  
  
9. Odešlete potrubí.  
  
## <a name="results"></a>Výsledky

Po dokončení tréninku:
 
  
+ Chcete-li vytvořit předpovědi na nová data, použijte trénovaný model a nová data jako vstup do modulu [Model skóre.](./score-model.md) 


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 