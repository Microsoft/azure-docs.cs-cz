---
title: Způsob podobnosti – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Použijte metodu podobnosti pro výpočet akademické podobnosti dvou řetězců.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 855d29d2c55b841bbbe4e9eadce8c29ad85fad90
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704853"
---
# <a name="similarity-method"></a>Podobnost – metoda

REST API **podobnosti** se používá k výpočtu akademické podobnosti mezi dvěma řetězci. 
<br>

**Koncový bod REST:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>Parametry žádosti

Parametr        |Typ dat      |Požadováno | Popis
----------|----------|----------|------------
**s1**        |Řetězec   |Ano  |Řetězec * pro porovnání
**s2**        |Řetězec   |Ano  |Řetězec * pro porovnání

<sub>* Řetězce pro porovnání mají maximální délku 1 MB.</sub>
<br>

## <a name="response"></a>Odpověď

Name | Popis
--------|---------
**SimilarityScore**        |Hodnota s plovoucí desetinnou čárkou, která představuje podobnost kosinu S1 a S2 s hodnotami blíže 1,0 znamená více podobných hodnot a hodnot blíž k-1,0 znamená méně

<br>

## <a name="successerror-conditions"></a>Podmínky úspěchu/chyby

Stav HTTP | Reason | Odpověď
-----------|----------|--------
**200**         |Úspěch | Číslo s plovoucí desetinnou čárkou
**400**         | Chybný požadavek nebo neplatný požadavek | Chybová zpráva      
**500**         |Vnitřní chyba serveru | Chybová zpráva
**Vypršel časový limit**     | Vypršel časový limit žádosti.  | Chybová zpráva

<br>

## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Příklad: Výpočet podobnosti dvou částečných abstrakcí
#### <a name="request"></a>Požadavek:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
V tomto příkladu vygenerujeme skóre podobnosti mezi dvěma částečnými abstrakcemi pomocí rozhraní API pro podobnosti.
#### <a name="response"></a>Odpověď:
```
0.520
```
#### <a name="remarks"></a>Mark
Skóre podobnosti je určeno vyhodnocením akademických konceptů prostřednictvím vkládání slov. V tomto příkladu 0,52 znamená, že dvě částečné abstrakce jsou poněkud podobné.
<br>
