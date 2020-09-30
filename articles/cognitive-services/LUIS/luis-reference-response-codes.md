---
title: Kódy odpovědí HTTP API – LUIS
titleSuffix: Azure Cognitive Services
description: Informace o tom, jaké kódy odpovědí HTTP se vracejí z rozhraní API pro vytváření LUIS a koncových bodů
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 03/04/2019
ms.openlocfilehash: 46a7fff86d3a8c4539b77a0f271179d68e7a26d2
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541675"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Běžné kódy odpovědí rozhraní API a jejich význam

Rozhraní API pro [vytváření obsahu](https://go.microsoft.com/fwlink/?linkid=2092087) a [koncových bodů](https://go.microsoft.com/fwlink/?linkid=2092356) vrací kódy odpovědí HTTP. I když zprávy odpovědi obsahují informace, které jsou specifické pro požadavek, je kód stavu odpovědi HTTP obecný.

## <a name="common-status-codes"></a>Běžné stavové kódy
Následující tabulka uvádí některé nejběžnější stavové kódy odpovědí HTTP pro [vytváření](https://go.microsoft.com/fwlink/?linkid=2092087) a [koncové](https://go.microsoft.com/fwlink/?linkid=2092356) rozhraní API:

|Kód|Rozhraní API|Vysvětlení|
|:--|--|--|
|400|Vytváření obsahu, koncový bod|Parametry požadavku jsou nesprávné, což znamená, že požadované parametry chybí, jsou poškozené nebo jsou moc velké.|
|400|Vytváření obsahu, koncový bod|tělo žádosti není správné, což znamená, že JSON chybí, má špatný nebo je moc velký.|
|401|Vytváření obsahu|místo vytváření klíče se použil klíč předplatného koncového bodu.|
|401|Vytváření obsahu, koncový bod|neplatný, poškozený nebo prázdný klíč|
|401|Vytváření obsahu, koncový bod| klíč neodpovídá oblasti|
|401|Vytváření obsahu|nejste vlastníkem nebo spolupracovníka.|
|401|Vytváření obsahu|Neplatné pořadí volání rozhraní API|
|403|Vytváření obsahu, koncový bod|překročilo se celkový limit kvót klíčů pro měsíční klíč.|
|409|Koncový bod|aplikace se pořád načítá.|
|410|Koncový bod|aplikaci je potřeba znovu pronaučit a znovu publikovat.|
|414|Koncový bod|dotaz překročil maximální limit znaků.|
|429|Vytváření obsahu, koncový bod|Překročení limitu přenosové rychlosti (počet požadavků za sekundu)|

## <a name="next-steps"></a>Další kroky

* Dokumentace k [vytváření](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) a [Endpoint](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) REST API
