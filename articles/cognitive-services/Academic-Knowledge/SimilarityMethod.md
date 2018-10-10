---
title: Metoda podobnosti – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Pro výpočet academic podobnosti dva řetězce použijte metodu podobnosti.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ms.openlocfilehash: 76e86eb78a06d98e3d5c6c54b244add3c0c245d2
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900457"
---
# <a name="similarity-method"></a>Metoda podobnosti

**Podobnosti** rozhraní REST API se používá k výpočtu academic podobnosti mezi dva řetězce. 
<br>

**Koncový bod REST:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>Parametry žádosti
Parametr        |Typ dat      |Požaduje se | Popis
----------|----------|----------|------------
**S1**        |Řetězec   |Ano  |Řetězec * která se má porovnat
**S2**        |Řetězec   |Ano  |Řetězec * která se má porovnat
<sub> * Řetězce k porovnání mají o maximální délce 1MB. </sub>
<br>
## <a name="response"></a>Odpověď
Název | Popis
--------|---------
**SimilarityScore**        |Představující kosinovou podobnost s1 a s2 s hodnotami blíž k 1.0 význam podobné a hodnoty blíže-1.0, což znamená menší hodnota s plovoucí desetinnou čárkou
<br>

## <a name="successerror-conditions"></a>Úspěch/chybových podmínek
Stav protokolu HTTP | Důvod | Odpověď
-----------|----------|--------
**200**         |Úspěch | Plovoucí desetinné číslo s desetinnou čárkou
**400**         | Chybný požadavek nebo požadavek není platný | Chybová zpráva      
**500**         |Vnitřní chyba serveru | Chybová zpráva
**Vypršel časový limit**     | Vypršel časový limit žádosti.  | Chybová zpráva
<br>
## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Příklad: Výpočet podobnosti dvě částečné přehledů
#### <a name="request"></a>Požadavek:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
V tomto příkladu se vygeneruje podobnosti skóre mezi dvěma částečné přehledů pomocí **podobnosti** rozhraní API.
#### <a name="response"></a>Odpověď:
```
0.520
```
#### <a name="remarks"></a>Poznámky:
Podobnosti skóre je určen tím, že posoudí academic koncepty prostřednictvím vkládání slov. V tomto příkladu 0.52 znamená, že dvě částečné přehledů poněkud podobný.
<br>