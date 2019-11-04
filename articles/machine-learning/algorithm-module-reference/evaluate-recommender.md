---
title: 'Vyhodnotit doporučeného: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul hodnocení vyhodnocení ve službě Azure Machine Learning k vyhodnocení přesnosti doporučeného modelu předpovědi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: dad11f0e03e55186dfee1e7a4f1f82fb44275693
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517992"
---
# <a name="evaluate-recommender"></a>Vyhodnotit doporučeného

Tento článek popisuje, jak použít modul **hodnocení vyhodnotit** v Návrháři Azure Machine Learning (Preview), abyste si mohli změřit přesnost předpovědi provedených modelem doporučení. Pomocí tohoto modulu můžete vyhodnotit čtyři různé druhy doporučení:  
  
-   Hodnocení bylo předpovězeno pro daného uživatele a položku.  
  
-   Položky, které se doporučují pro daného uživatele  
  
Při vytváření předpovědi pomocí modelu doporučení se pro každý z těchto podporovaných typů předpověď vrátí mírně odlišné výsledky. Modul **hodnocení vyhodnocení** je odvozený druh předpovědi z formátu sloupce pro datovou sadu s skóre. Například **datová sada s skóre** může obsahovat:

- tři položky hodnocení uživatelem
- Uživatelé a jejich Doporučené položky

Modul také aplikuje příslušné metriky výkonu na základě typu předpovědi, kterou provedete. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Jak konfigurovat doporučení pro vyhodnocení

Modul **hodnocení vyhodnocení** porovnává výstup předpovědi podle modelu doporučení s odpovídajícími daty "uzemnění". Například [doporučený modul SVD skóre](score-svd-recommender.md) vytváří datové sady s **vyhodnocenými**skóremi, které lze analyzovat doporučeným nástrojem hodnocení.

### <a name="requirements"></a>Požadavky

**Vyhodnocení doporučení** vyžaduje jako vstup následující datové sady. 
  
#### <a name="test-dataset"></a>Testovací datová sada

**Testovací datová sada** obsahuje "základní" data ve formě **trojí pro hodnocení položek**.  

#### <a name="scored-dataset"></a>Datová sada s skóre

**Datová sada s skóre** obsahuje předpovědi, které byly vygenerovány modelem doporučení.  
  
Sloupce v této druhé datové sadě závisí na druhu předpovědi, kterou jste během bodování prováděli. Například datová sada s skóre může obsahovat následující:

- Uživatelé, položky a hodnocení, které uživatel pro položku nejspíš udělí
- Seznam uživatelů a položek, které jsou pro ně doporučeny 

### <a name="metrics"></a>Metriky

Metriky výkonu pro model jsou generovány na základě typu vstupu. Podrobnosti najdete v těchto částech:

+ [Vyhodnotit předpokládaná hodnocení](#evaluate-predicted-ratings)
+ [Vyhodnotit doporučení pro položky](#evaluate-item-recommendations)

## <a name="evaluate-predicted-ratings"></a>Vyhodnotit předpokládaná hodnocení  

Při vyhodnocování předpokládaných hodnocení musí datovou sadu (druhý vstup pro **vyhodnocení doporučení**) obsahovat **trojnásobné hodnoty pro hodnocení uživatelem**, které splňují tyto požadavky:
  
-   První sloupec datové sady obsahuje identifikátory uživatelů.  
  
-   Druhý sloupec obsahuje identifikátory položek.  
  
-   Třetí sloupec obsahuje odpovídající hodnocení položky uživatele.  
  
> [!IMPORTANT] 
> Aby bylo vyhodnocení úspěšné, musí být názvy sloupců `User`, `Item`a `Rating`, v uvedeném pořadí.  
  
**Vyhodnotit doporučení doporučit** porovná hodnocení v datové sadě s hodnotou pravdy s předpokládaným hodnocením datové sady s skóre a vypočítá střední hodnotu " **absolutní** " (Mae) a **hlavní střední** chybu (RMSE).



## <a name="evaluate-item-recommendations"></a>Vyhodnotit doporučení pro položky

Při vyhodnocování doporučení položky použijte datovou sadu s skóre, která zahrnuje Doporučené položky pro každého uživatele:
  
-   První sloupec datové sady musí obsahovat identifikátor uživatele.  
  
-   Všechny následující sloupce by měly obsahovat odpovídající Doporučené identifikátory položek seřazených podle toho, jak je relevantní položka uživateli. 

    Před připojením této datové sady doporučujeme, abyste datovou sadu seřadili tak, aby se nejdůležitější položky nacházely jako první.  



> [!IMPORTANT] 
> Aby bylo možné **vyhodnotit vhodné** fungování, musí být názvy sloupců `User`, `Item 1`, `Item 2``Item 3` a tak dále.  
  
**Vyhodnotit doporučený** modul vypočítá průměrně Kumulovaný kumulativní nárůst (**NDCG**) a vrátí ho do výstupní datové sady.  
  
Vzhledem k tomu, že není možné znát skutečné "uzemněné pravdivosti" pro doporučené položky, **vyhodnoťte** Doporučené použití hodnocení položek uživatele v testovací sadě jako nárůsty při výpočtu NDCG. Aby bylo možné vyhodnotit, modul pro vyhodnocování doporučení musí pouze vydávat doporučení pro položky s hodnocením uzemnění (v testovací sadě dat).  
  

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 
