---
title: 'Two-Class Neuronové sítě: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu Two-Class Neural Network ve službě Azure Machine Learning k vytvoření modelu neuronové sítě, který můžete použít k předvídání cíl, který má jenom dvě hodnoty.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7ea852fcd312c6f7b1b716278ed538b7accde5bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029217"
---
# <a name="two-class-neural-network-module"></a>Modul Two-Class Neuronové sítě

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k vytvoření modelu neuronové sítě, který můžete použít k předvídání cíl, který má jenom dvě hodnoty.

Je metoda učení neuronové sítě pomocí klasifikace a vyžaduje tudíž *označené datovou sadu*, který obsahuje popisek sloupce. Například můžete použít tento model neuronových sítí k předvídání binární výsledky, jako jestli má pacienta určité stádiu, nebo zda je pravděpodobně selže v rámci zadané časové okno na počítači.  

Po definování model trénovat zadáním příznakem datové sady a modelu jako vstup do [trénování modelu](./train-model.md). Trénovaného modelu lze pak použít k předpovědi hodnot pro nové vstupů.

### <a name="more-about-neural-networks"></a>Další informace o neuronových sítí

Neuronové sítě je sada propojených vrstvy. Vstupy jsou první vrstvu a připojených do výstupu vrstvy acyklického grafu vážený hrany a uzly.

Mezi vrstvami vstupní a výstupní můžete vložit více skrytými vrstvami. Nejvíce prediktivní úkoly můžete snadno provést pomocí pouze jednoho nebo několika skrytými vrstvami. Však nedávného průzkumu, ukázalo, že může být výhodnější v složité úkoly, jako je například rozpoznávání obrazu a řeči neuronových sítí (DNN) s více vrstvami. Sousední vrstvy se používají k modelování zvýšení úrovně sémantické hloubky.

Vztah mezi vstupů a výstupů je získané při cvičení neuronové sítě na vstupní data. Směr grafu pokračuje od vstupy prostřednictvím skryté vrstvě a vrstvě výstup. Všechny uzly ve vrstvě propojenými pomocí vážený hran do uzlů v další vrstva.

Pro výpočet výstupu sítě pro konkrétní vstup, hodnota se počítá v každém uzlu ve skryté vrstvě a vrstvě výstup. Hodnota je nastavena výpočtem vážený součet hodnot z uzlů z předchozí vrstvy. Funkce Aktivace protokolem se následně použije na vážený částky.
  
## <a name="how-to-configure"></a>Postup konfigurace

1.  Přidat **Two-Class Neuronové sítě** modulu do experimentu. Můžete najít tento modul v rámci **Machine Learning**, **inicializovat**v **klasifikace** kategorie.  
  
2.  Určení způsobu modelu zaškolení, tak, že nastavíte **režimu vytváření trainer** možnost.  
  
    -   **Jeden parametr**: Tuto možnost zvolte, pokud už víte, jak chcete provést konfiguraci modelu.  

3.  Pro **skryté vrstvě specifikace**, vyberte typ Síťová architektura pro vytváření.  
  
    -   **Plně připojení případ**: Používá výchozí architektura neuronové sítě definované pro dvěma třídami neuronové sítě následujícím způsobem:
  
        -   Má jeden skryté vrstvě.
  
        -   Výstupní vrstvě je plně připojení k skryté vrstvě a skryté vrstvě plně připojený k vrstvě vstupní.
  
        -   Počet uzlů ve vrstvě vstupní se rovná počtu funkcí v trénovací data.
  
        -   Počet uzlů ve skryté vrstvě nastavená uživatelem. Výchozí hodnota je 100.
  
        -   Počet uzlů se rovná počtu tříd. Pro neuronové sítě dvěma třídami to znamená, že všechny vstupy musí být namapovaný na jednu ze dvou uzlů ve vrstvě výstup.

5.  Pro **rychlost učení**, definujte velikost kroku v každé iteraci, před opravy. Větší hodnotu kurz vás naučí může způsobit modelu a umožňuje konvergovat rychleji, ale jeho překročení místní minima.

6.  Pro **počet iterací učení**, zadejte maximální počet pokusů algoritmus by měl zpracovat trénovacích případů.

7.  Pro **počáteční learning oceňuje průměr**, zadejte váhy uzlu na začátku procesu učení.

8.  Pro **potenciál**, zadejte váhu použít během učení pro uzly z předchozí iterace  

10. Vyberte **Shuffle příklady** možnost přesouvat případů mezi iteracemi. Pokud výběr této možnosti zrušíte, případy se zpracovávají ve stejném pořadí při každém spuštění testu.
  
11. Pro **náhodná počáteční hodnota čísla**, zadejte hodnotu, který se použije jako počáteční hodnoty.
  
     Určení základní hodnota hodnota je užitečná při zajištění opakovatelnosti během spuštění tom stejném experimentu.  V opačném případě hodnota hodiny systému slouží jako počáteční hodnoty, což může způsobit mírně odlišné výsledky při každém spuštění testu.
  
13. Do experimentu přidat označený datové sady a připojení mezi [školicí moduly](module-reference.md).  
  
    -   Pokud nastavíte **režimu vytváření trainer** k **jediný parametr**, použijte [trénování modelu](train-model.md) modulu.  
  
14. Spusťte experiment.

## <a name="results"></a>Výsledky

Po dokončení školení:

+ Prohlédnout souhrnné informace o modelu parametry, společně s funkci váhy získané při školení a dalších parametrů neuronové sítě, kliknete pravým tlačítkem na výstup [Train Model](./train-model.md)a vyberte **vizualizovat**.  

+ Uložte snímek trénovaného modelu, klikněte pravým tlačítkem myši **Trained model** výstupní a vyberte **uložit jako Trénovaného modelu**. Tento model nebude aktualizován v po sobě jdoucích spuštění tom stejném experimentu.


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 
