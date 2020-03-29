---
title: 'Vyhodnotit doporučující: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak použít modul Evaluate Recommender v Azure Machine Learning k vyhodnocení přesnosti předpovědí modelu doporučení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 38144d5df04427a82989b78843466ecd55386196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76312256"
---
# <a name="evaluate-recommender"></a>Vyhodnocení doporučovacího systému

Tento článek popisuje, jak používat modul Vyhodnotit doporučení v návrháři Azure Machine Learning (preview). Cílem je měřit přesnost předpovědi, které model doporučení učinil. Pomocí tohoto modulu můžete vyhodnotit různé druhy doporučení:  
  
-   Hodnocení předpovídaná pro uživatele a položku    
-   Položky doporučené pro uživatele  
  
Při vytváření předpovědi pomocí modelu doporučení mírně odlišné výsledky jsou vráceny pro každý z těchto typů podporovaných předpověď. Modul Evaluate Recommender odvoduje druh předpovědi z formátu sloupce datové sady s končivou. Datová sada s konstatováno může například obsahovat:

- Trojčata hodnocení uživatelských položek
- Uživatelé a jejich doporučené položky

Modul také použije příslušné metriky výkonu na základě typu predikce. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Jak nakonfigurovat hodnotit doporučující

Modul Evaluate Recommender porovnává výstup předpovědi pomocí modelu doporučení s odpovídajícími daty "ground truth". Například [modul Score SVD Recommender](score-svd-recommender.md) vytváří datové sady s hodnocením, které můžete analyzovat pomocí funkce Vyhodnotit doporučení.

### <a name="requirements"></a>Požadavky

Vyhodnocení recommender vyžaduje následující datové sady jako vstup. 
  
#### <a name="test-dataset"></a>Testovací datová sada

Testovací datová sada obsahuje data "základní pravdy" ve formě trojčat hodnocení uživatelských položek.  

#### <a name="scored-dataset"></a>Datová sada se skóre

Datová sada s konzetí obsahuje předpovědi, které model doporučení vygeneroval.  
  
Sloupce v této druhé datové sady závisí na druhu předpověď, kterou jste provedli během procesu vyhodnocování. Datová sada s konstatováno může například obsahovat některou z následujících položek:

- Uživatelé, položky a hodnocení, která by uživatel pravděpodobně poskytl pro položku
- Seznam uživatelů a položek doporučených pro ně 

### <a name="metrics"></a>Metriky

Metriky výkonu pro model jsou generovány na základě typu vstupu. V následujících částech jsou uvedeny podrobnosti.

## <a name="evaluate-predicted-ratings"></a>Vyhodnocení předpovídaných hodnocení  

Při vyhodnocování předpovídaných hodnocení musí datová sada s hodnocením (druhý vstup pro vyhodnocení doporučujícího zařízení) obsahovat trojčata hodnocení uživatelských položek, která splňují tyto požadavky:
  
-   První sloupec datové sady obsahuje identifikátory uživatele.    
-   Druhý sloupec obsahuje identifikátory položek.  
-   Třetí sloupec obsahuje odpovídající hodnocení uživatelských položek.  
  
> [!IMPORTANT] 
> Aby bylo vyhodnocení úspěšné, musí `User` `Item`být `Rating`názvy sloupců , , a , v uvedeném pořadí.  
  
Vyhodnocení doporučující porovná hodnocení v datové sadě "ground truth" s předpovídanými hodnoceními skórované datové sady. Poté vypočítá střední absolutní chybu (MAE) a střední kvadratou chybu (RMSE) kořenové (Kvadratý).



## <a name="evaluate-item-recommendations"></a>Vyhodnotit doporučení položek

Při vyhodnocování doporučení položek použijte datovou sadu s hodnocením, která obsahuje doporučené položky pro každého uživatele:
  
-   První sloupec datové sady musí obsahovat identifikátor uživatele.    
-   Všechny následující sloupce by měly obsahovat odpovídající doporučené identifikátory položek seřazené podle toho, jak je položka pro uživatele relevantní. 

Před připojením této datové sady doporučujeme seřadit datovou sadu tak, aby nejrelevantnější položky byly na prvním místě.  

> [!IMPORTANT] 
> Aby funkce Vyhodnocení doporučujícího funkce fungovala, musí být `User`názvy sloupců , `Item 1`, `Item 2` `Item 3` a tak dále.  
  
Vyhodnocení Recommender vypočítá průměrný normalizovaný diskontovaný kumulativní zisk (NDCG) a vrátí jej ve výstupní datové sadě.  
  
Vzhledem k tomu, že není možné znát skutečnou "základní pravdu" pro doporučené položky, vyhodnotit doporučení používá hodnocení uživatelských položek v testovací datové sadě jako zisky ve výpočtu NDCG. Chcete-li vyhodnotit, modul hodnocení doporučující musí pouze vytvářet doporučení pro položky s hodnocením "základní pravdy" (v testovací datové sadě).  
  

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
