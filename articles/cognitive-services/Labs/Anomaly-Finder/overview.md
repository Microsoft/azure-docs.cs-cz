---
title: Co je vyhledávací anomálií? -Microsoft kognitivní služby | Microsoft Docs
description: Pomocí pokročilých algoritmů v hledání anomálií vám pomohou identifikovat anomálie v časových řad dat a vrátí informace ve kognitivní služby společnosti Microsoft.
services: cognitive-services
author: tonyxing
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 04/19/2018
ms.author: tonyxing
ms.openlocfilehash: 1080bb0ad1d901a8b9a5ace4993d4e0d46924a03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343018"
---
# <a name="what-is-anomaly-finder"></a>Co je vyhledávací anomálií?

Vyhledávací anomálií vám umožňuje monitorovat data v čase a zjišťovat anomálie pomocí machine learning, který přizpůsobí jedinečným datům automaticky použitím právo statistické modelu bez ohledu na odvětví, scénář nebo datový svazek. Pomocí časové řady jako vstup, vrátí anomálií vyhledávací API zda datového bodu je anomálií, určuje očekávanou hodnotou a horní a dolní meze pro vizualizaci. Jako předem služba AI anomálií vyhledávací nevyžaduje žádné strojového učení znalosti nad rámec vědět, jak používat rozhraní RESTful API. Díky vývoj jednoduché a všestranné vzhledem k tomu, že ho pracuje se žádná data řady čas a může být vestavěny do dat systémy. Vyhledávací anomálií zahrnuje široké rozpětí případy použití – pro instanci finančních nástrojů pro správu podvod, krádež, změna trhů a potenciální obchodní incidenty nebo monitorování provoz zařízení IoT při zachování anonymity. Toto řešení můžete také peněžně jako součást služby pro koncové zákazníky pochopit změny v datech, výdaje, návratnost investic nebo aktivity uživatelů.
Vyzkoušet rozhraní API vyhledávací anomálií a získali lepší představu o vaše data. 

V tématu, můžete vytvořit s tímto rozhraním API:

* Další informace k předvídání očekávaných hodnot na základě historických dat časové řady
* Zjistit, zda je hodnota datového bodu anomálií mimo historických vzor
* Generovat vzdálené k vizualizaci rozsah "normální" hodnoty

![Anomaly_Finder](./media/anomaly_detection1.png) 

Obrázek 1: Zjišťovat anomálie v prodeje příjmů

![Anomaly_Finder](./media/anomaly_detection2.png)

Obrázek 2: Detekovat vzor změny v žádosti o službu

## <a name="requirements"></a>Požadavky

- Minimální objem dat pro vstup časové řady: minimální 13 dat body pro časové řady bez zrušte periodicity body minimálně 4 cykly data pro časové řady s známé periodicity. 
- Integrita dat: čas datové body jsou oddělené ve stejném intervalu a žádné chybějící body řady. 

## <a name="identify-anomalies"></a>Identifikovat anomálií

Detekce anomálií rozhraní API vrací výsledek, který jestli jakékoli dané datové body jsou anomálií, nebo Ne a poskytuje další informace o následujícím způsobem
* Období - periodicity, který používá rozhraní API pro zjištění anomálií bodů.
* WarningText - možné informace upozornění.
* ExpectedValue - předpovězené hodnoty metodou učení základě modelu
* IsAnomaly - výsledek na zda datové body jsou anomálií.
* IsAnomaly_Neg - výsledek na tom, zda datové body jsou anomálie v záporné směru (vyhrazené)
* IsAnomaly_Pos - výsledek na tom, zda datové body jsou anomálie v kladné směru (špičky)
* UpperMargin – součet ExpectedValue a UpperMargin Určuje, že je stále představit jako normální horní hranice, která datového bodu
* LowerMargin - (ExpectedValue - LowerMargin) Určuje dolní hranice, že datový bod je stále představit jako normální

> [!Note]
> UpperMargin a LowerMargin můžete použít ke generování pásem kolem řady skutečný čas k vizualizaci rozsahu hodnot normální. 

## <a name="adjusting-lower-and-upper-bounds-in-post-processing-on-the-response"></a>Úprava vybírány v následného zpracování v odpovědi

Detekce anomálií rozhraní API vrátí výchozí výsledek toho, jestli hodnota datového bodu anomálií nebo Ne, a horní a dolní mez, lze vypočítat z ExpectedValue a UpperMargin/LowerMargin. Tyto výchozí hodnoty by měly fungovat správně pro většině případů. Ale některé scénáře vyžadují jiný rozsah než výchozí hodnoty. Postup doporučujeme aplikuje coefficiency na UpperMargin nebo LowerMargin upravit dynamické hranice.

### <a name="examples-with-1152-as-coefficiency"></a>Příklady 1/1.5/2 jako coefficiency

![Výchozí velkých a malých písmen](./media/sensitivity_1.png)

![1.5 velkých a malých písmen](./media/sensitivity_1.5.png)

![2 velkých a malých písmen](./media/sensitivity_2.png)

Žádosti s ukázkovými daty

[!INCLUDE [Request](./includes/request.md)]

Ukázková odpověď JSON

[!INCLUDE [Response](./includes/response.md)]
