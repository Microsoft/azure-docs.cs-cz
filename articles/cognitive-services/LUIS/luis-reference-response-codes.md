---
title: Kódy odpovědí HTTP rozhraní API – služba LUIS
titleSuffix: Azure Cognitive Services
description: Pochopit, jaké kódy odpovědí HTTP jsou vráceny z api pro vytváření a koncový bod služby LUIS
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 405381a2d80ee6469b2b90bcb25923004fe55882
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270252"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Běžné kódy odpovědí rozhraní API a jejich význam

Vytváření [authoring](https://go.microsoft.com/fwlink/?linkid=2092087) a [koncový bod](https://go.microsoft.com/fwlink/?linkid=2092356) API vrátit kódy odpovědi HTTP. Zatímco zprávy odpovědí obsahují informace specifické pro požadavek, stavový kód odpovědi HTTP je obecný.

## <a name="common-status-codes"></a>Běžné stavové kódy
V následující tabulce jsou uvedeny některé nejběžnější kódy stavu odpovědi HTTP pro [vytváření](https://go.microsoft.com/fwlink/?linkid=2092087) a [koncová pole](https://go.microsoft.com/fwlink/?linkid=2092356) API:

|kód|rozhraní API|Vysvětlení|
|:--|--|--|
|400|Vytváření, koncový bod|parametry požadavku jsou nesprávné, což znamená, že požadované parametry chybí, jsou poškozeny nebo příliš velké|
|400|Vytváření, koncový bod|tělo požadavku je nesprávné, což znamená, že json chybí, je poškozený nebo příliš velký|
|401|Vytváření obsahu|použitý klíč předplatného koncového bodu namísto vytváření klíče|
|401|Vytváření, koncový bod|neplatný, poškozený nebo prázdný klíč|
|401|Vytváření, koncový bod| klíč neodpovídá oblasti|
|401|Vytváření obsahu|nejste vlastníkem ani spolupracovníkem|
|401|Vytváření obsahu|neplatné pořadí volání rozhraní API|
|403|Vytváření, koncový bod|celkový limit měsíční klíčové kvóty byl překročen|
|409|Koncový bod|aplikace se stále načítá|
|410|Koncový bod|aplikace musí být přeškolena a znovu publikována|
|414|Koncový bod|dotaz překračuje maximální limit znaků|
|429|Vytváření, koncový bod|Překročení limitu rychlosti (požadavky za sekundu)|

## <a name="next-steps"></a>Další kroky

* Dokumentace [pro vytváření](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) rozhraní REST API a [koncového bodu](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)
