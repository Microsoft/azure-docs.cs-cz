---
title: Kódy odpovědí protokolu HTTP rozhraní API
titleSuffix: Azure
description: Pochopit, jaké kódy odpovědí protokolu HTTP jsou vráceny z LUIS vytváření obsahu a koncový bod rozhraní API
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 8ea180131ce2f9e1288c3ffb928170be55a77de7
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53598446"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Běžné kódy odpovědí rozhraní API a jejich význam

[Vytváření](https://aka.ms/luis-authoring-apis) a [koncový bod](https://aka.ms/luis-endpoint-apis) rozhraní API vrací kódy odpovědí protokolu HTTP. Zprávy s odezvami zahrnout informace specifické pro požadavek, je obecné stavového kódu odpovědi HTTP. 

## <a name="common-status-codes"></a>Běžné kódy stavu
V následující tabulce jsou uvedeny některé z nejběžnějších kódy stavu odpovědi HTTP pro [vytváření](https://aka.ms/luis-authoring-apis) a [koncový bod](https://aka.ms/luis-endpoint-apis) rozhraní API:

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

## <a name="next-steps"></a>Další postup

* Rozhraní REST API [vytváření](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) a [koncový bod](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) dokumentace
