---
title: Metoda podobnost v Academic Knowledge API | Microsoft Docs
description: Pomocí této metody podobnosti vypočítat academic podobnosti dva řetězce v kognitivní služby společnosti Microsoft.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 01/18/2017
ms.author: alch
ms.openlocfilehash: 472498d6bfe06ae4477a30f892d44e79c901acf5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342368"
---
# <a name="similarity-method"></a>Podobnosti – metoda

**Podobnost** REST API se používá k výpočtu academic podobnosti mezi dva řetězce. 
<br>

**Koncový bod REST:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>Parametry žádosti
Parametr        |Typ dat      |Požaduje se | Popis
----------|----------|----------|------------
**S1**        |Řetězec   |Ano  |Řetězec * který se má porovnat
**s2**        |Řetězec   |Ano  |Řetězec * který se má porovnat
<sub> * Řetězce k porovnání mít délku povoleného 1MB. </sub>
<br>
## <a name="response"></a>Odpověď
Název | Popis
--------|---------
**SimilarityScore**        |Plovoucí bodu hodnotu představující kosinus podobnosti s1 a s2 s hodnotami blíže k více podobné 1.0 význam a hodnoty bližší-1.0 znamená menší
<br>

## <a name="successerror-conditions"></a>Úspěch nebo Chyba podmínky
Stav protokolu HTTP | Důvod | Odpověď
-----------|----------|--------
**200**         |Úspěch | Bod číslo s plovoucí čárkou
**400**         | Chybný požadavek nebo neplatný požadavek | Chybová zpráva      
**500**         |Vnitřní chyba serveru | Chybová zpráva
**Vypršel časový limit**     | Vypršel časový limit žádosti.  | Chybová zpráva
<br>
## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Příklad: Vypočítat podobnosti dva částečné přehledů
#### <a name="request"></a>Žádost:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
V tomto příkladu se vygeneruje hodnocení podobnosti mezi dvěma částečné přehledů pomocí **podobnost** rozhraní API.
#### <a name="response"></a>Odpověď:
```
0.520
```
#### <a name="remarks"></a>Poznámky:
Hodnocení podobnosti je určen podle hodnocením academic koncepty prostřednictvím vložení aplikace word. V tomto příkladu 0.52 znamená, že jsou dva částečné přehledů trochu podobné.
<br>