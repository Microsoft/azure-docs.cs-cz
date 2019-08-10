---
title: Kódy odpovědí HTTP API – LUIS
titleSuffix: Azure Cognitive Services
description: Pochopit, jaké kódy odpovědí protokolu HTTP jsou vráceny z LUIS vytváření obsahu a koncový bod rozhraní API
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 43fbca732bbd281ad980ae86e4a89f446fdb69bc
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945865"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Běžné kódy odpovědí rozhraní API a jejich význam

[Vytváření](https://go.microsoft.com/fwlink/?linkid=2092087) a [koncový bod](https://go.microsoft.com/fwlink/?linkid=2092356) rozhraní API vrací kódy odpovědí protokolu HTTP. Zprávy s odezvami zahrnout informace specifické pro požadavek, je obecné stavového kódu odpovědi HTTP. 

## <a name="common-status-codes"></a>Běžné kódy stavu
V následující tabulce jsou uvedeny některé z nejběžnějších kódy stavu odpovědi HTTP pro [vytváření](https://go.microsoft.com/fwlink/?linkid=2092087) a [koncový bod](https://go.microsoft.com/fwlink/?linkid=2092356) rozhraní API:

|Kód|Rozhraní API|Vysvětlení|
|:--|--|--|
|400|Vytváření koncového bodu|Parametry žádosti jsou nesprávné, což znamená, že požadované parametry jsou chybí, je poškozený nebo je příliš velký|
|400|Vytváření koncového bodu|tělo žádosti je nesprávná, což znamená, že je ve formátu JSON chybí, je poškozený nebo je příliš velký|
|401|Vytváření obsahu|použít klíče koncového bodu předplatného, místo vytváření klíč|
|401|Vytváření koncového bodu|klíč neplatný, je poškozený nebo je prázdný|
|401|Vytváření koncového bodu| klíč neodpovídá oblasti|
|401|Vytváření obsahu|nejste vlastníkem nebo spolupracovníka|
|401|Vytváření obsahu|Neplatné pořadí volání rozhraní API|
|403|Vytváření koncového bodu|Celkový měsíční klíčů překročil se limit kvóty|
|409|Koncový bod|aplikace se stále načítá.|
|410|Koncový bod|aplikace musí být retrained a znovu publikovat|
|414|Koncový bod|dotaz překračuje limit maximálního počtu znaků|
|429|Vytváření koncového bodu|Překročení limitu přenosové rychlosti (počet požadavků za sekundu)|

## <a name="next-steps"></a>Další kroky

* Dokumentace k [vytváření](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) a [Endpoint](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) REST API
