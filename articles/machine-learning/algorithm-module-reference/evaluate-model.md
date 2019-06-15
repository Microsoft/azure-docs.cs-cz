---
title: 'Vyhodnocení modelu: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu Evaluate Model ve službě Azure Machine Learning k měření přesnost trénovaného modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 40a8247c22da1f7a057e222565ffb2ec4c6b7fb3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028737"
---
# <a name="evaluate-model-module"></a>Vyhodnocení modulu Model

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k měření přesnost trénovaného modelu. Zadejte datovou sadu obsahující skóre, které se generují z modelu a **Evaluate Model** modulu vypočítá sadu standardních metrik.
  
 Metriky vrácené **Evaluate Model** závisí na typu modelu, který hodnocení:  
  
-   **Modelů klasifikace**    
-   **Regresních modelů**    



> [!TIP]
> Pokud jste ještě na vyhodnocení modelu, doporučujeme série videí pomocí zotavení po havárii. Stephen Elston, jako součást [kurz machine learning](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) z EdX. 


Existují tři způsoby, jak používat **Evaluate Model** modul:

+ Generovat skóre nad vašimi daty trénování a vyhodnocení modelu podle tohoto skóre
+ Vygenerování skóre, které se v modelu, ale porovnání ta hodnocení na skóre, které se na vyhrazené testovací sadě
+ Porovnejte výsledky dvou různých ale souvisejících modelů pomocí stejné sady dat.

## <a name="use-the-training-data"></a>Použití trénovacích dat

Evaluate model, musíte připojit datové sady, který obsahuje sadu vstupní sloupce a skóre.  Pokud je k dispozici žádná data, můžete použít původní datové sady.

1. Připojit **skóre datovou sadu** výstupu se zobrazila [určení skóre modelu](./score-model.md) vstupem **Evaluate Model**. 
2. Klikněte na tlačítko **Evaluate Model** modulu a spuštění experimentů k vygenerování skóre hodnocení.

## <a name="use-testing-data"></a>Použití testovacích dat

Běžný scénář, ve službě machine learning, je původní datové sady rozdělit do trénování a testování datových sad pomocí [rozdělení](./split-data.md) modul, nebo [rozdělení a vzorky](./partition-and-sample.md) modulu. 

1. Připojit **skóre datovou sadu** výstupu se zobrazila [určení skóre modelu](score-model.md) vstupem **Evaluate Model**. 
2. Propojte výstup modulu rozdělení dat, který obsahuje testovací data na pravém vstup **Evaluate Model**.
2. Klikněte na tlačítko **Evaluate Model** modul a vyberte **spustit vybrané** k vygenerování skóre hodnocení.

## <a name="compare-scores-from-two-models"></a>Porovnejte výsledky ze dvou modelů

Můžete také připojit druhou sadu skóre k **Evaluate Model**.  Skóre může být sada sdílené hodnocení, který je známý výsledky nebo sadu výsledků z jiného modelu na stejná data.

Tato funkce je užitečná, protože může snadno porovnat výsledky ze dvou různých modelů na stejná data. Nebo vám může porovnat výsledky ze dvou různých běhů přes stejná data s různými parametry.

1. Připojit **skóre datovou sadu** výstupu se zobrazila [určení skóre modelu](score-model.md) vstupem **Evaluate Model**. 
2. Propojte výstup modulu určení skóre modelu pro model druhý pravém vstupem **Evaluate Model**.
3. Klikněte pravým tlačítkem na **Evaluate Model**a vyberte **spustit vybrané** k vygenerování skóre hodnocení.

## <a name="results"></a>Výsledky

Po spuštění **Evaluate Model**, klikněte pravým tlačítkem na modul a vyberte **výsledky vyhodnocení** zobrazíte výsledky. Můžete:

+ Výsledky uložit jako datovou sadu, pro jednodušší analýzu pomocí jiných nástrojů
+ Generovat vizualizace v rozhraní

Pokud se připojujete datové sady do obou vstupy **Evaluate Model**, budou výsledky obsahovat metriky pro obě sady dat nebo obou modelů.
Model nebo data připojená k portu levé nejprve zobrazí v sestavě, za nímž následuje metriky pro datovou sadu, nebo model připojené na správný port.  

Například následující obrázek představuje porovnání výsledky ze dvou modelů clusteringu, které byly vytvořeny na stejná data, ale s různými parametry.  

![AML&#95;Comparing2Models](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Protože to není model clusteringu, výsledky hodnocení jsou jiné než pokud výsledky ze dvou modelů Regrese v porovnání nebo porovnání dvou modelů klasifikace. Celkové prezentace je však stejný. 

## <a name="metrics"></a>Metriky

Tato část popisuje metriky pro určité typy modelů podporovaný pro použití s **Evaluate Model**:

+ [modelů klasifikace](#bkmk_classification)
+ [regresních modelů](#bkmk_regression)

###  <a name="bkmk_classification"></a> Metriky pro modelů klasifikace

Tyto metriky se vykazují při vyhodnocování modelů klasifikace. Pokud srovnáte modelů, jsou seřazené podle metriku, kterou vyberete pro hodnocení.  
  
-   **Přesnost** měří ještě lepší model klasifikace jako podíl true výsledky a celkový počet případů.  
  
-   **Přesnost** je poměr true výsledky přes všechny pozitivní výsledky.  
  
-   **Odvolat** je zlomek všechny správné výsledky vrácené modelu.  
  
-   **F-skóre** je vypočítán vážený průměr přesnosti a spojené s vracením mezi 0 a 1, přičemž ideální F skóre hodnota je 1.  
  
-   **AUC** opatření oblasti pod křivkou vykreslují true pozitivní výsledky na y pozitivní osy a hodnotu false na ose x. Tato metrika je užitečné, protože poskytuje jedno číslo, které umožňuje porovnání modelů různých typů.  
  
- **Průměrná ztráta protokolu** je jeden skóre pro express penalizace pro nesprávné výsledky. Počítá se jako rozdíl mezi dvěma rozdělení pravděpodobnosti – true jeden a jeden v modelu.  
  
- **Školení protokolu ztrátu** je jeden skóre, které představují výhodu třídění přes náhodné předpovědi. Ztráta protokolu měří nejistoty modelu porovnáním pravděpodobností výstupy na známé hodnoty (základu pravdy) v popiscích. Chcete minimalizovat ztráty protokolu pro model jako celek.

##  <a name="bkmk_regression"></a> Metriky pro regresní modely
 
Metriky vrácené pro regresní modely jsou obecně navržené pro odhad množství chyb.  Podle data, pokud je malý rozdíl mezi hodnotami pozorovaných a předpokládané se považuje za model. Ale na vzorec, podle kterého zbytky (rozdíl mezi čárky jeden předpokládané a jeho odpovídající skutečnou hodnotu) můžete zjistit mnohem o potenciální posun v modelu.  
  
 Tyto metriky se vykazují za vaše rozhodnutí vyzkoušet regresních modelů. Když porovnáte modelů, jsou seřazené podle metriku, kterou vyberete pro hodnocení.  
  
- **Střední absolutní chyba (MAE)** měří jak blízko předpovědi se skutečné výsledky; proto je lepší nižším skóre.  
  
- **Kořenové znamenat kvadratické chyby (RMSE)** vytvoří jednu hodnotu, která shrnuje chyby v modelu. Metrika vypočítáním rozdíl, ignoruje rozdíl mezi útoky over-pass-the předpovědi a snížení předpovědí.  
  
- **Relativní absolutní chyba (RAE)** relativní absolutní rozdíl mezi hodnotami očekávaných a aktuálních; je relativní vzhledem k tomu, že mean rozdíl je vydělen aritmetický průměr.  
  
- **Chyba (RSE) spolehlivosti relativní** podobně normalizuje celkový kvadratická chyba předpovězeným hodnotám vydělí celkový kvadratická chyba skutečnými hodnotami.  
  
- **Rozumí jedna chyba nula (MZOE)** Určuje, zda byla do predikce. správný nebo ne.  Jinými slovy: `ZeroOneLoss(x,y) = 1` při `x!=y`; jinak vrátí hodnotu `0`.
  
- **Koeficient spolehlivosti**, která se často označují jako R<sup>2</sup>, představuje výkon prediktivní model jako hodnota mezi 0 a 1. Nula znamená, že je model náhodného (vysvětluje co); 1 znamená, že existuje dokonale vyhovovat. Upozornění by však použít při interpretaci R<sup>2</sup> hodnoty, jak nízké hodnoty mohou být zcela normální a vysoké hodnoty mohou být stavem suspect.
  

## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 