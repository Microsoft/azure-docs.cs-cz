---
title: 'Regrese neuronové sítě: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu Neuronové sítě Regrese ve službě Azure Machine Learning k vytvoření regresní model pomocí algoritmu přizpůsobitelné neuronové sítě...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bc6a7505ab09e929e5add61eea687f871aedf242
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029307"
---
# <a name="neural-network-regression-module"></a>Modul neuronové sítě regrese

*Vytvoří regresní model pomocí algoritmu neuronové sítě*  
  
 Kategorie: Machine Learning / inicializovat Model / regrese
  
## <a name="module-overview"></a>Přehled modulu  

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k vytvoření regresní model pomocí algoritmu přizpůsobitelné neuronové sítě.
  
 Přestože neuronové sítě jsou známé pro použití v obsáhlého learningu a modelování složitých problémů, jako je rozpoznávání obrazu, jsou snadno přizpůsobit regresní problémy. Všechny třídy statistických modelů můžete označovaných neuronové sítě, je-li použít adaptivní váhy a můžete ji odhadnout nelineárních funkce jejich vstupech. Regrese neuronové sítě je proto vhodné kde tradičnější regresní model se nemůže vejít řešení problémů.
  
 Představuje metodu učení regrese neuronové sítě a vyžaduje tudíž *označené datovou sadu*, který obsahuje popisek sloupce. Protože regresní model předpovídá číselnou hodnotu, popisek sloupce musí být typu číselná data.  
  
 Můžou natrénujete tím, že poskytuje model a označené datovou sadu jako vstup do [trénování modelu](./train-model.md). Trénovaného modelu lze pak použít k předpovědi hodnot pro nový vstupní příklady.  
  
## <a name="configure-neural-network-regression"></a>Konfigurace regrese Neuronové sítě 

Neuronové sítě je možné výrazně přizpůsobit. Tato část popisuje, jak vytvořit model dvěma způsoby:
  
+ [Vytvoření modelu neuronové sítě pomocí výchozí architektura](#bkmk_DefaultArchitecture)  
  
    Pokud přijmete výchozí architektura neuronové sítě, použijte **vlastnosti** podokně můžete nastavit parametry, které řídí chování neuronové sítě, jako je počet uzlů ve skryté vrstvě, rychlost učení a normalizace.

    Pokud jste ještě na neuronových sítí, začněte tady. Modul podporuje mnoho vlastní nastavení, stejně jako model ladění bez znalosti neuronových sítí. 

+ Definovat vlastní architekturu pro neuronové sítě 

    Tuto možnost použijte, pokud chcete přidat další skrytými vrstvami, nebo můžete plně přizpůsobit síťové architektury, připojení a aktivace funkce.
    
    Tato možnost je vhodná, pokud jste už nějaké zkušenosti s neuronových sítí. Definování síťové architektury pomocí jazyka Net #.  

##  <a name="bkmk_DefaultArchitecture"></a> Vytvoření modelu neuronové sítě pomocí výchozí architektura
  
1.  Přidat **Neuronové sítě regrese** modulu do experimentu v rozhraní. Můžete najít tento modul v rámci **Machine Learning**, **inicializovat**v **regrese** kategorie. 
  
2. Určete, jak mají modelu zaškolení, tak, že nastavíte **režimu vytváření trainer** možnost.  
  
    -   **Jeden parametr**: Tuto možnost zvolte, pokud už víte, jak chcete provést konfiguraci modelu.  

3.  V **skryté vrstvě specifikace**vyberte **plně připojení případ**. Tato volba vytvoří model s použitím výchozí architektura neuronové sítě, který regresního modelu neuronové sítě má tyto atributy:  
  
    + Síť obsahuje přesně jeden skryté vrstvě.
    + Výstupní vrstvě je plně připojení k skryté vrstvě a skryté vrstvě plně připojený k vrstvě vstupní.
    + Počet uzlů ve skryté vrstvě můžete nastavit uživatelem (výchozí hodnota je 100).  
  
    Vzhledem k tomu, že počet uzlů ve vrstvě vstupní je určeno několik funkcí v trénovacích dat, v regresní model může existovat jenom jeden uzel ve vrstvě výstup.  
  
4. Pro **počet skrytých uzlů:**, zadejte počet skrytých uzlů. Výchozí hodnota je jeden skryté vrstvě s 100 uzlů. (Tato možnost není k dispozici, pokud definujete vlastní architektury pomocí Net #.)
  
5.  Pro **rychlost učení**, zadejte hodnotu, která definuje krok v každé iteraci, před opravy. Větší hodnotu kurz vás naučí může způsobit modelu a umožňuje konvergovat rychleji, ale jeho překročení místní minima.

6.  Pro **počet iterací učení**, zadejte maximální počet pokusů algoritmus zpracovává trénovacích případů.

7.  Pro ** počáteční learning oceňuje průměr, zadejte hodnotu, která určuje váhu uzlu na začátku procesu učení.

8.  Pro **potenciál**, zadejte hodnotu, která se účtují během učení jako váhu na uzly z předchozí iterace.

10. Vyberte možnost, **Shuffle příklady**, chcete-li změnit pořadí případů mezi iteracemi. Pokud výběr této možnosti zrušíte, případy se zpracovávají ve stejném pořadí při každém spuštění testu.
  
11. Pro **náhodná počáteční hodnota čísla**, Volitelně můžete zadat hodnotu pro použití jako počáteční hodnoty. Určení základní hodnota hodnota je užitečná při zajištění opakovatelnosti během spuštění tom stejném experimentu.
  
13. Připojit trénovací datovou sadu a jeden z [školicí moduly](module-reference.md): 
  
    -   Pokud nastavíte **režimu vytváření trainer** k **jediný parametr**, použijte [Train Model](./train-model.md).  
  
   
14. Spusťte experiment.  

## <a name="results"></a>Výsledky

Po dokončení školení:

+ Prohlédnout souhrnné informace o modelu parametry, společně s funkci váhy získané při školení a dalších parametrů neuronové sítě, kliknete pravým tlačítkem na výstup [Train Model](./train-model.md)a vyberte **vizualizovat**.  

+ Uložte snímek trénovaného modelu, klikněte pravým tlačítkem myši **Trained model** výstupní a vyberte **uložit jako Trénovaného modelu**. Tento model nebude aktualizován v po sobě jdoucích spuštění tom stejném experimentu.


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 