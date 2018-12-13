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
ms.openlocfilehash: 478727a4d3628fbe6a385e3beb28201c228f9e2d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078227"
---
# <a name="luis-api-http-response-codes"></a>Kódy odpovědí protokolu HTTP rozhraní API LUIS
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