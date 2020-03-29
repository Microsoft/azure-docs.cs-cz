---
title: 'Vícetřídová neuronová síť: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Multiclass Neural Network v Azure Machine Learning vytvořit model neuronové sítě, který lze použít k předvídání cíle, který má více hodnot.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 653b12ddd54c5ec9e4e7dd23a323f34460daa962
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920412"
---
# <a name="multiclass-neural-network-module"></a>Vícetřídový modul neuronové sítě

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k vytvoření modelu neuronové sítě, který lze předpovědět cíl, který má více hodnot. 

Například neuronové sítě tohoto druhu mohou být použity při složitých úlohách počítačového vidění, jako je rozpoznávání číslic nebo písmen, klasifikace dokumentů a rozpoznávání vzorů.

Klasifikace pomocí neuronových sítí je metoda učení pod dohledem, a proto vyžaduje *tagovované datové sady,* která obsahuje sloupec popisek.

Model můžete trénovat poskytnutím modelu a tagged datové sady jako vstup [do Train Model](./train-model.md). Trénovaný model pak lze předpovědět hodnoty pro nové vstupní příklady.  

## <a name="about-neural-networks"></a>O neuronových sítích

Neuronová síť je sada vzájemně propojených vrstev. Vstupy jsou první vrstvou a jsou spojeny s výstupní vrstvou cyklickým grafem složeným z vážených hran a uzlů.

Mezi vstupní a výstupní vrstvy můžete vložit více skrytých vrstev. Většinu prediktivních úkolů lze snadno provádět pouze s jednou nebo několika skrytými vrstvami. Nedávný výzkum však ukázal, že hluboké neuronové sítě (DNN) s mnoha vrstvami mohou být účinné při složitých úlohách, jako je rozpoznávání obrazu nebo řeči. Následné vrstvy se používají k modelování zvyšujících úrovní sémantické hloubky.

Vztah mezi vstupy a výstupy se učí z trénování neuronové sítě na vstupní data. Směr grafu pokračuje ze vstupů přes skrytou vrstvu a do výstupní vrstvy. Všechny uzly ve vrstvě jsou spojeny váženými hranami s uzly v další vrstvě.

Pro výpočet výstupu sítě pro konkrétní vstup se vypočítá hodnota v každém uzlu ve skrytých vrstvách a ve výstupní vrstvě. Hodnota je nastavena výpočtem váženého součtu hodnot uzlů z předchozí vrstvy. Na tento vážený součet se pak použije aktivační funkce.

## <a name="configure-multiclass-neural-network"></a>Konfigurace vícetřídové neuronové sítě

1. Přidejte modul **Neural network multiclass** do kanálu v návrháři. Tento modul najdete v části **Strojové učení**, **Inicializovat**, v kategorii **Klasifikace.**

2. **Vytvořit režim trenéra**: Pomocí této možnosti můžete určit, jak má být model trénovaný:

    - **Jeden parametr**: Tuto možnost zvolte, pokud již víte, jak chcete model konfigurovat.

    - **Rozsah parametrů**: Tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit tažení parametrů. Vyberte rozsah hodnot, které chcete iterát přes a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates přes všechny možné kombinace nastavení, které jste zadali k určení hyperparameters, které produkují optimální výsledky.  

3. **Specifikace skryté vrstvy**: Vyberte typ síťové architektury, kterou chcete vytvořit.

    - **Plně připojená velká písmena**: Tuto možnost vyberte, chcete-li vytvořit model pomocí výchozí architektury neuronové sítě. Pro modely neuronových sítí s více třídami jsou výchozí hodnoty následující:

        - Jedna skrytá vrstva
        - Výstupní vrstva je plně připojena ke skryté vrstvě.
        - Skrytá vrstva je plně připojena ke vstupní vrstvě.
        - Počet uzlů ve vstupní vrstvě je určen počtem funkcí v trénovacích datech.
        - Počet uzlů ve skryté vrstvě může nastavit uživatel. Výchozí hodnota je 100.
        - Počet uzlů ve výstupní vrstvě závisí na počtu tříd.
  
   

5. **Počet skrytých uzlů**: Tato možnost umožňuje přizpůsobit počet skrytých uzlů ve výchozí architektuře. Zadejte počet skrytých uzlů. Výchozí hodnota je jedna skrytá vrstva se 100 uzly.

6. **Rychlost učení**: Definujte velikost kroku provedeného při každé iteraci před korekcí. Vyšší hodnota pro rychlost učení může způsobit, že se model sblíží rychleji, ale může přestřelit místní minima.

7. **Počet iterací učení**: Zadejte maximální počet, kolikrát by měl algoritmus zpracovat případy školení.

8. **Počáteční učení hmotnostprůměr**: Určete hmotnosti uzlů na začátku procesu učení.

9. **Momentum**: Zadejte váhu, která se má použít během učení se uzlům z předchozích iterací.
  
11. **Příklady náhodného přehrávání**: Tuto možnost vyberte, chcete-li zamíchat případy mezi iteracemi.

    Pokud zrušíte výběr této možnosti, případy jsou zpracovány v přesně stejném pořadí při každém spuštění kanálu.

12. **Osiva náhodných čísel**: Zadejte hodnotu, která se má použít jako osivo, pokud chcete zajistit opakovatelnost napříč spuštěními stejného kanálu.

14. Připojte trénovací datovou sadu a jeden z [trénovacích modulů](module-reference.md): 

    - Pokud nastavíte **režim Vytvořit trenér** na jeden **parametr**, použijte [model vlaku](train-model.md).  
  

## <a name="results"></a>Výsledky

Po dokončení tréninku:

- Chcete-li uložit snímek trénovaného modelu, vyberte kartu **Výstupy** v pravém panelu modulu **modelu Vlak.** Vyberte ikonu **Registrovat datovou sadu,** chcete-li model uložit jako opakovaně použitelný modul.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 