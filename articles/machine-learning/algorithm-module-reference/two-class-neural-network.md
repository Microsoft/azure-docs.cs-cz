---
title: 'Dvoutřídní neuronová síť: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu dvoutřídní neuronové sítě v Azure Machine Learning vytvořit model neuronové sítě, který lze použít k předvídání cíle, který má pouze dvě hodnoty.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3a5cf2b210781d43ddd1a7aa87736a53df222cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477387"
---
# <a name="two-class-neural-network-module"></a>Dvoutřídní modul neuronové sítě

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k vytvoření modelu neuronové sítě, který lze předpovědět cíl, který má pouze dvě hodnoty.

Klasifikace pomocí neuronových sítí je metoda učení pod dohledem, a proto vyžaduje *tagovované datové sady*, která obsahuje sloupec popisek. Tento model neuronové sítě můžete například použít k předvídání binárních výsledků, například zda má pacient určitou nemoc nebo zda je pravděpodobné selhání počítače v zadaném časovém období.  

Po definování modelu, trénování tím, že poskytuje tagované datové sady a model jako vstup [do train model](./train-model.md). Trénovaný model pak lze předpovědět hodnoty pro nové vstupy.

### <a name="more-about-neural-networks"></a>Více o neuronových sítích

Neuronová síť je sada vzájemně propojených vrstev. Vstupy jsou první vrstvou a jsou spojeny s výstupní vrstvou cyklickým grafem složeným z vážených hran a uzlů.

Mezi vstupní a výstupní vrstvy můžete vložit více skrytých vrstev. Většinu prediktivních úkolů lze snadno provádět pouze s jednou nebo několika skrytými vrstvami. Nedávný výzkum však ukázal, že hluboké neuronové sítě (DNN) s mnoha vrstvami mohou být účinné při složitých úlohách, jako je rozpoznávání obrazu nebo řeči. Následné vrstvy se používají k modelování zvyšujících úrovní sémantické hloubky.

Vztah mezi vstupy a výstupy se učí z trénování neuronové sítě na vstupní data. Směr grafu pokračuje ze vstupů přes skrytou vrstvu a do výstupní vrstvy. Všechny uzly ve vrstvě jsou spojeny váženými hranami s uzly v další vrstvě.

Pro výpočet výstupu sítě pro konkrétní vstup se vypočítá hodnota v každém uzlu ve skrytých vrstvách a ve výstupní vrstvě. Hodnota je nastavena výpočtem váženého součtu hodnot uzlů z předchozí vrstvy. Na tento vážený součet se pak použije aktivační funkce.
  
## <a name="how-to-configure"></a>Jak nakonfigurovat

1.  Přidejte do svého kanálu modul **Dvoutřídní neuronová síť.** Tento modul najdete v části **Strojové učení**, **Inicializovat**, v kategorii **Klasifikace.**  
  
2.  Určete, jak má být model trénovaný, nastavením **možnosti Vytvořit režim trenažéru.**  
  
    -   **Jeden parametr**: Tuto možnost zvolte, pokud již víte, jak chcete model konfigurovat.

    -   **Rozsah parametrů**: Pokud si nejste jisti nejlepšími parametry, můžete najít optimální parametry pomocí modulu [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Zadáte určitý rozsah hodnot a trenér iterates přes více kombinací nastavení k určení kombinace hodnot, které vytváří nejlepší výsledek.  

3.  V **části Specifikace skryté vrstvy**vyberte typ síťové architektury, kterou chcete vytvořit.  
  
    -   **Plně připojen případ**: Používá výchozí architekturu neuronové sítě, definované pro neuronové sítě dvou třídy takto:
  
        -   Má jednu skrytou vrstvu.
  
        -   Výstupní vrstva je plně připojena ke skryté vrstvě a skrytá vrstva je plně připojena ke vstupní vrstvě.
  
        -   Počet uzlů ve vstupní vrstvě se rovná počtu funkcí v trénovacích datech.
  
        -   Počet uzlů ve skryté vrstvě je nastaven uživatelem. Výchozí hodnota je 100.
  
        -   Počet uzlů se rovná počtu tříd. Pro neuronové sítě dvou tříd, to znamená, že všechny vstupy musí mapovat na jeden ze dvou uzlů ve výstupní vrstvě.

5.  Pro **rychlost učení**definujte velikost kroku provedeného při každé iteraci před opravou. Vyšší hodnota pro rychlost učení může způsobit, že se model sblíží rychleji, ale může přestřelit místní minima.

6.  Pro **počet iterací učení**zadejte maximální počet, kolikrát by měl algoritmus zpracovat případy školení.

7.  **Pro počáteční učení tloušťky průměr**, zadejte tloušťky uzlů na začátku procesu učení.

8.  Pro **momentum**, zadejte váhu použít při učení se uzly z předchozích iterací  

10. Vyberte volbu **Zamíchat příklady,** chcete-li zamíchat případy mezi iteracemi. Pokud zrušíte výběr této možnosti, případy jsou zpracovány v přesně stejném pořadí při každém spuštění kanálu.
  
11. Do **pole Osiva náhodných čísel**zadejte hodnotu, která se má použít jako osiva.
  
     Určení hodnoty osiva je užitečné, pokud chcete zajistit opakovatelnost napříč spuštěními stejného kanálu.  V opačném případě se jako osiva používá hodnota systémových hodin, což může při každém spuštění kanálu způsobit mírně odlišné výsledky.
  
13. Přidejte tagovitu datovou sadu do kanálu a připojte jeden z [trénovacích modulů](module-reference.md).  
  
    -   Pokud nastavíte **vytvořit režim trenažéru** na **jeden parametr**, použijte modul [Model vlaku.](train-model.md)  
  
14. Odešlete potrubí.

## <a name="results"></a>Výsledky

Po dokončení tréninku:

+ Chcete-li uložit snímek trénovaného modelu, vyberte kartu **Výstupy** v pravém panelu modulu **modelu Vlak.** Vyberte ikonu **Registrovat datovou sadu,** chcete-li model uložit jako opakovaně použitelný modul.

+ Chcete-li použít model pro vyhodnocování, přidejte modul **Modelu skóre** do kanálu.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
