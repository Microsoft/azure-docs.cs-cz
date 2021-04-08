---
title: Referenční příručka stroje standardních pravidel pro Azure CDN | Microsoft Docs
description: Referenční dokumentace pro podmínky a akce shody v modulu Standard rules pro Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 1a0f4456f38939632026645500dd48acbf7dbc88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93242204"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Reference ke stroji pravidel Standard pro Azure CDN

V [modulu Standard Rules](cdn-standard-rules-engine.md) pro Azure Content Delivery Network (Azure CDN) se pravidlo skládá z jedné nebo více podmínek shody a akci. Tento článek obsahuje podrobný popis podmínek shody a funkcí, které jsou k dispozici v modulu Standard rules pro Azure CDN.

Modul pravidel je navržený tak, aby měl koncovou autoritu, jak jsou konkrétní typy požadavků zpracovávány standardním Azure CDN.

**Společná použití pro pravidla**:

- Přepsat nebo definovat vlastní zásadu mezipaměti.
- Přesměrování požadavků.
- Úprava hlaviček požadavků a odpovědí HTTP.

## <a name="terminology"></a>Terminologie

Chcete-li definovat pravidlo v modulu pravidel, nastavte [podmínky](cdn-standard-rules-engine-match-conditions.md) a [Akce](cdn-standard-rules-engine-actions.md)shody:

 ![Struktura Azure CDNch pravidel](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Každé pravidlo může mít až deset podmínek shody a pět akcí. Každý koncový bod Azure CDN může mít až 25 pravidel. 

Součástí tohoto omezení je výchozí *globální pravidlo*. Globální pravidlo nemá podmínky shody. akce, které jsou definovány v globálním pravidle, se vždy aktivují.

   > [!IMPORTANT]
   > Pořadí, ve kterém jsou uvedeny více pravidel, má vliv na způsob zpracování pravidel. Akce, které jsou zadány v pravidle, mohou být přepsány následujícím pravidlem.

## <a name="limits-and-pricing"></a>Omezení a ceny 

Každý koncový bod Azure CDN může mít až 25 pravidel. Každé pravidlo může mít až deset podmínek shody a pět akcí. Ceny pro modul pravidel řídí následující dimenze: 
- Pravidla: $1 na pravidlo za měsíc 
- Počet zpracovaných požadavků: $0,60 za milion požadavků
- Prvních 5 pravidel zůstane zadarmo.

## <a name="syntax"></a>Syntax

Způsob zpracování speciálních znaků v pravidle se liší v závislosti na tom, jak různé podmínky shody a akce zpracovávají textové hodnoty. Podmínka nebo akce shody mohou interpretovat text jedním z následujících způsobů:

- [Hodnoty literálu](#literal-values)
- [Zástupné hodnoty](#wildcard-values)


### <a name="literal-values"></a>Hodnoty literálu

Text, který je interpretován jako hodnota literálu, zpracovává všechny speciální znaky *s výjimkou% symbol* jako součást hodnoty, která musí být v pravidle shodná. Například podmínka shody literálů nastavená na `'*'` je splněna pouze v případě, že `'*'` je nalezena přesná hodnota.

K označení kódování adresy URL (například) se používá znak procenta `%20` .

### <a name="wildcard-values"></a>Zástupné hodnoty

V současné době podporujeme zástupný znak v **podmínce URLPath Match** v modulu Standard Rules. \*Znak je zástupný znak, který reprezentuje jeden nebo více znaků. 

## <a name="next-steps"></a>Další kroky

- [Podmínky shody v modulu Standard Rules](cdn-standard-rules-engine-match-conditions.md)
- [Akce v modulu Standard Rules](cdn-standard-rules-engine-actions.md)
- [Vynucení HTTPS s využitím stroje pravidel Standard](cdn-standard-rules-engine.md)
- [Přehled Azure CDN](cdn-overview.md)
