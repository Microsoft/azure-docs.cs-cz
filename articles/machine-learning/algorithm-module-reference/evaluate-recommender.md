---
title: 'Vyhodnotit doporučeného: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul vyhodnocení doporučení v Azure Machine Learning k vyhodnocení přesnosti doporučení předpovědi modelů.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 0890e13acbba8dae31de28d7c78a81bd9b516853
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "76312256"
---
# <a name="evaluate-recommender"></a>Vyhodnocení doporučovacího systému

Tento článek popisuje, jak v Návrháři Azure Machine Learning použít modul pro vyhodnocení doporučených postupů. Cílem je změřit přesnost předpovědi, kterou vytvořil model doporučení. Pomocí tohoto modulu můžete vyhodnotit různé druhy doporučení:  
  
-   Odhad hodnocení pro uživatele a položku    
-   Položky, které se doporučují pro uživatele  
  
Při vytváření předpovědi pomocí modelu doporučení se pro každý z těchto podporovaných typů předpověď vrátí mírně odlišné výsledky. Modul hodnocení vyhodnocení je odvozený druh předpovědi z formátu sloupce pro datovou sadu s skóre. Například datová sada s skóre může obsahovat:

- Tři položky hodnocení uživatelem
- Uživatelé a jejich Doporučené položky

Modul také aplikuje příslušné metriky výkonu na základě typu předpovědi, kterou provedete. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Jak konfigurovat doporučení pro vyhodnocení

Modul hodnocení odhadu porovnává výstup předpovědi pomocí modelu doporučení s odpovídajícími daty "uzemnění". Například [doporučený modul SVD skóre](score-svd-recommender.md) vytváří datové sady s skóre, které můžete analyzovat pomocí nástroje Evaluate doporučit.

### <a name="requirements"></a>Požadavky

Vyhodnocení doporučení vyžaduje jako vstup následující datové sady. 
  
#### <a name="test-dataset"></a>Testovací datová sada

Testovací datová sada obsahuje "základní" data ve formě trojí pro hodnocení položek.  

#### <a name="scored-dataset"></a>Datová sada s skóre

Datová sada s skóre obsahuje předpovědi, který model doporučení vygeneroval.  
  
Sloupce v této druhé datové sadě závisí na druhu předpovědi, kterou jste provedli během procesu bodování. Například datová sada s skóre může obsahovat jednu z následujících hodnot:

- Uživatelé, položky a hodnocení, které by mohl uživatel pro položku přidělit
- Seznam uživatelů a položek, které jsou pro ně doporučeny 

### <a name="metrics"></a>Metriky

Metriky výkonu pro model jsou generovány na základě typu vstupu. Následující části obsahují podrobné informace.

## <a name="evaluate-predicted-ratings"></a>Vyhodnotit předpokládaná hodnocení  

Při vyhodnocování předpokládaných hodnocení musí datová sada s hodnocením (druhý vstup pro vyhodnocení doporučení) obsahovat tři troje ohodnocené uživatelem, které splňují tyto požadavky:
  
-   První sloupec datové sady obsahuje identifikátory uživatelů.    
-   Druhý sloupec obsahuje identifikátory položek.  
-   Třetí sloupec obsahuje odpovídající hodnocení položky uživatele.  
  
> [!IMPORTANT] 
> Aby bylo vyhodnocení úspěšné, názvy sloupců musí být `User` , `Item` a v `Rating` uvedeném pořadí.  
  
Vyhodnotit doporučený postup porovná hodnocení v datové sadě "uzemnění" s předpokládaným hodnocením datové sady s skóre. Pak vypočítá střední absolutní chybu (MAE) a hlavní střední chybu (RMSE).



## <a name="evaluate-item-recommendations"></a>Vyhodnotit doporučení pro položky

Když vyhodnocujete doporučení pro položky, použijte datovou sadu s skóre, která zahrnuje Doporučené položky pro každého uživatele:
  
-   První sloupec datové sady musí obsahovat identifikátor uživatele.    
-   Všechny následující sloupce by měly obsahovat odpovídající Doporučené identifikátory položek seřazených podle toho, jak je relevantní položka uživateli. 

Než tuto datovou sadu připojíte, doporučujeme, abyste datovou sadu seřadili tak, aby se nejdůležitější položky nacházely jako první.  

> [!IMPORTANT] 
> Aby bylo možné vyhodnotit Doporučené doporučení pro práci, názvy sloupců musí být `User` ,, `Item 1` `Item 2` `Item 3` a tak dále.  
  
Vyhodnotit doporučený modul vypočítá průměrně Kumulovaný kumulativní nárůst (NDCG) a vrátí ho do výstupní datové sady.  
  
Vzhledem k tomu, že není možné znát skutečné "uzemněné pravdivosti" pro doporučené položky, vyhodnoťte doporučuje použití hodnocení položek uživatele v testovací sadě jako nárůsty při výpočtu NDCG. Aby bylo možné vyhodnotit, modul pro vyhodnocování doporučení musí pro položky s hodnocením "uzemnění" vydávat jenom doporučení (v testovací sadě dat).  
  

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
