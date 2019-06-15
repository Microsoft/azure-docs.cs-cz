---
title: 'Víc tříd Neuronové sítě: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak použít modul Multiclass Neural Network ve službě Azure Machine Learning k vytvoření modelu neuronové sítě, který můžete použít k předvídání cíl, který má více hodnot.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7bef225c001ebd9bbb9a45c8bcc301cfb49edb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029382"
---
# <a name="multiclass-neural-network-module"></a>Víc tříd modulu Neuronové sítě

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k vytvoření modelu neuronové sítě, který můžete použít k předvídání cíl, který má více hodnot. 

Například neuronových sítí tohoto druhu zneužít v komplexní vize úlohám, jako je například rozpoznávání číslice nebo písmeno, klasifikace dokumentů a rozpoznávání vzorců jako.

Je metoda učení neuronové sítě pomocí klasifikace a vyžaduje tudíž *označené datovou sadu* , který obsahuje popisek sloupce.

Můžou natrénujete tím, že poskytuje model a označené datovou sadu jako vstup do [trénování modelu](./train-model.md). Trénovaného modelu lze pak použít k předpovědi hodnot pro nový vstupní příklady.  

## <a name="about-neural-networks"></a>O neuronových sítí

Neuronové sítě je sada propojených vrstvy. Vstupy jsou první vrstvu a připojených do výstupu vrstvy acyklického grafu vážený hrany a uzly.

Mezi vrstvami vstupní a výstupní můžete vložit více skrytými vrstvami. Nejvíce prediktivní úkoly můžete snadno provést pomocí pouze jednoho nebo několika skrytými vrstvami. Však nedávného průzkumu, ukázalo, že může být výhodnější v složité úkoly, jako je například rozpoznávání obrazu a řeči neuronových sítí (DNN) s více vrstvami. Sousední vrstvy se používají k modelování zvýšení úrovně sémantické hloubky.

Vztah mezi vstupů a výstupů je získané při cvičení neuronové sítě na vstupní data. Směr grafu pokračuje od vstupy prostřednictvím skryté vrstvě a vrstvě výstup. Všechny uzly ve vrstvě propojenými pomocí vážený hran do uzlů v další vrstva.

Pro výpočet výstupu sítě pro konkrétní vstup, hodnota se počítá v každém uzlu ve skryté vrstvě a vrstvě výstup. Hodnota je nastavena výpočtem vážený součet hodnot z uzlů z předchozí vrstvy. Funkce Aktivace protokolem se následně použije na vážený částky.

## <a name="configure-multiclass-neural-network"></a>Konfigurace víc tříd Neuronové sítě

1. Přidat **Neuronové sítě MultiClass** modulu do experimentu v rozhraní. Můžete najít tento modul v rámci **Machine Learning**, **inicializovat**v **klasifikace** kategorie.

2. **Vytvoření trainer režimu**: Tuto možnost použijte k určení způsobu modelu, který má být:

    - **Jeden parametr**: Tuto možnost zvolte, pokud už víte, jak chcete provést konfiguraci modelu.

    

3. **Skryté vrstvě specifikace**: Vyberte typ Síťová architektura pro vytváření.

    - **Plně připojení případ**: Vyberte tuto možnost, chcete-li vytvořit model s použitím výchozí architektura neuronové sítě. Výchozí hodnoty pro modely víc tříd neuronové sítě, jsou následující:

        - Jeden skryté vrstvě
        - Výstupní vrstvě je plně připojení k skryté vrstvě.
        - Skryté vrstvě je plně připojení k vrstvě vstupu.
        - Počet uzlů ve vrstvě vstupní je určeno několik funkcí v trénovací data.
        - Počet uzlů ve skryté vrstvě můžete nastavit uživatelem. Výchozí hodnota je 100.
        - Počet uzlů ve vrstvě výstup závisí na počtu tříd.
  
   

5. **Počet skrytých uzlů:** : Tato možnost umožňuje upravit počet skrytých uzlů: ve výchozím nastavení architektura. Zadejte počet skrytých uzlů. Výchozí hodnota je jeden skryté vrstvě s 100 uzlů.

6. **Rychlost učení**: Definujte velikost kroku v každé iteraci, před opravy. Větší hodnotu kurz vás naučí může způsobit modelu a umožňuje konvergovat rychleji, ale jeho překročení místní minima.

7. **Počet iterací učení**: Zadejte maximální počet pokusů algoritmus by měl zpracovat trénovacích případů.

8. **Počáteční learning oceňuje průměr**: Zadejte váhy uzlu na začátku procesu učení.

9. **Podpora**: Zadejte váhu použít během učení pro uzly z předchozí iterace.
  
11. **Shuffle příklady**: Tuto možnost náhodně případů mezi iteracemi.

    Pokud výběr této možnosti zrušíte, případy se zpracovávají ve stejném pořadí při každém spuštění testu.

12. **Náhodné číslo**: Zadejte hodnotu pro použití jako počáteční hodnotu, pokud chcete pro zajištění opakovatelnosti během spuštění tom stejném experimentu.

14. Připojit trénovací datovou sadu a jeden z [školicí moduly](module-reference.md): 

    - Pokud nastavíte **režimu vytváření trainer** k **jediný parametr**, použijte [Train Model](train-model.md).  
  

## <a name="results"></a>Výsledky

Po dokončení školení:

- Prohlédnout souhrnné informace o modelu parametry, společně s funkci váhy získané při školení a dalších parametrů neuronové sítě, kliknete pravým tlačítkem na výstup [Train Model](./train-model.md) a vyberte **vizualizovat**.  

- Uložte snímek trénovaného modelu, klikněte pravým tlačítkem myši **Trained model** výstupní a vyberte **uložit jako Trénovaného modelu**. Tento model nebude aktualizován v po sobě jdoucích spuštění tom stejném experimentu.


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 