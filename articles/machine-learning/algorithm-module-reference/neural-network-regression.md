---
title: 'Regrese neuronové sítě: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu regrese neural sítě v Azure Machine Learning vytvořit regresní model pomocí algoritmu přizpůsobitelné neuronové sítě..
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3a591badab29a1669d109f01f8a93732704d2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456094"
---
# <a name="neural-network-regression-module"></a>Regresní modul neuronové sítě

*Vytvoří regresní model pomocí algoritmu neuronové sítě.*  
  
 Kategorie: Strojové učení / Inicializace modelu / Regrese
  
## <a name="module-overview"></a>Přehled modulu  

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k vytvoření regresního modelu pomocí přizpůsobitelného algoritmu neuronové sítě.
  
 Přestože neuronové sítě jsou široce známé pro použití v hlubokéučení a modelování složitých problémů, jako je rozpoznávání obrazu, jsou snadno přizpůsobit regresní problémy. Každá třída statistických modelů může být nazvat neuronové sítě, pokud používají adaptivní váhy a může aproximovat nelineární funkce jejich vstupů. Regrese neuronové sítě je tedy vhodná pro problémy, kde tradiční regresní model se nevejde do řešení.
  
 Regrese neuronové sítě je metoda učení pod dohledem, a proto vyžaduje *tagovované datové sady*, která obsahuje sloupec popisek. Vzhledem k tomu, že regresní model předpovídá číselnou hodnotu, musí být sloupec popisku číselným datovým typem.  
  
 Model můžete trénovat poskytnutím modelu a tagged datové sady jako vstup [do Train Model](./train-model.md). Trénovaný model pak lze předpovědět hodnoty pro nové vstupní příklady.  
  
## <a name="configure-neural-network-regression"></a>Konfigurace regrese neuronové sítě 

Neuronové sítě lze značně přizpůsobit. Tato část popisuje, jak vytvořit model pomocí dvou metod:
  
+ [Vytvoření modelu neuronové sítě pomocí výchozí architektury](#bkmk_DefaultArchitecture)  
  
    Pokud přijmete výchozí architekturu neuronové sítě, použijte podokno **Vlastnosti** k nastavení parametrů, které řídí chování neuronové sítě, jako je například počet uzlů ve skryté vrstvě, rychlost učení a normalizace.

    Začněte zde, pokud jste novým neuronovými sítěmi. Modul podporuje mnoho přizpůsobení, stejně jako ladění modelu, bez hluboké znalosti neuronových sítí. 

+ Definování vlastní architektury pro neuronovou síť 

    Tuto možnost použijte, pokud chcete přidat další skryté vrstvy nebo plně přizpůsobit síťovou architekturu, její připojení a aktivační funkce.
    
    Tato možnost je nejlepší, pokud jste již poněkud obeznámeni s neuronovými sítěmi. K definování síťové architektury se používá jazyk Net#.  

##  <a name="create-a-neural-network-model-using-the-default-architecture"></a><a name="bkmk_DefaultArchitecture"></a>Vytvoření modelu neuronové sítě pomocí výchozí architektury

1.  Přidejte modul **regrese neural sítě** do kanálu v návrháři. Tento modul najdete v části **Strojové učení**, **Inicializovat**, v kategorii **Regrese.** 
  
2. Označte, jak chcete model trénovat, nastavením **možnosti Vytvořit režim trenažéru.**  
  
    -   **Jeden parametr**: Tuto možnost zvolte, pokud již víte, jak chcete model konfigurovat.

    -   **Rozsah parametrů**: Tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit tažení parametrů. Vyberte rozsah hodnot, které chcete iterát přes a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates přes všechny možné kombinace nastavení, které jste zadali k určení hyperparameters, které produkují optimální výsledky.   

3.  Ve **specifikaci Skryté vrstvy**vyberte **Možnost Plně propojený případ**. Tato možnost vytvoří model pomocí výchozí architektury neuronové sítě, která pro regresní model neuronové sítě má tyto atributy:  
  
    + Síť má přesně jednu skrytou vrstvu.
    + Výstupní vrstva je plně připojena ke skryté vrstvě a skrytá vrstva je plně připojena ke vstupní vrstvě.
    + Počet uzlů ve skryté vrstvě může nastavit uživatel (výchozí hodnota je 100).  
  
    Vzhledem k tomu, že počet uzlů ve vstupní vrstvě je určen počtem prvků v trénovacích datech, v regresním modelu může být ve výstupní vrstvě pouze jeden uzel.  
  
4. Do **pole Počet skrytých uzlů**zadejte počet skrytých uzlů. Výchozí hodnota je jedna skrytá vrstva se 100 uzly. (Tato možnost není k dispozici, pokud definujete vlastní architekturu pomocí net#.)
  
5.  Do **pole Učení zadejte**hodnotu, která definuje krok přijatý při každé iteraci před opravou. Vyšší hodnota pro rychlost učení může způsobit, že se model sblíží rychleji, ale může přestřelit místní minima.

6.  Pro **počet iterací učení**zadejte maximální počet, kolikrát algoritmus zpracovává případy školení.


8.  Pro **Momentum**zadejte hodnotu, která se použije během učení jako váhu na uzlech z předchozích iterací.

10. Výběrem možnosti **Náhodné příklady**změňte pořadí případů mezi iteracemi. Pokud zrušíte výběr této možnosti, případy jsou zpracovány v přesně stejném pořadí při každém spuštění kanálu.
  
11. Do **pole Osiva náhodných čísel**můžete volitelně zadat hodnotu, která se má použít jako osivo. Určení hodnoty osiva je užitečné, pokud chcete zajistit opakovatelnost napříč spuštěními stejného kanálu.
  
13. Připojte trénovací datovou sadu a jeden z [trénovacích modulů](module-reference.md): 
  
    -   Pokud nastavíte **režim Vytvořit trenér** na jeden **parametr**, použijte [model vlaku](./train-model.md).  
  
   
14. Odešlete potrubí.  

## <a name="results"></a>Výsledky

Po dokončení tréninku:

- Chcete-li uložit snímek trénovaného modelu, vyberte kartu **Výstupy** v pravém panelu modulu **modelu Vlak.** Vyberte ikonu **Registrovat datovou sadu,** chcete-li model uložit jako opakovaně použitelný modul.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 