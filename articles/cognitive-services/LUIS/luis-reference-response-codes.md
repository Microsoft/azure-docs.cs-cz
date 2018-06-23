---
title: Kódy odpovědí jazyk Principy (LEOŠ) rozhraní API HTTP - Azure | Microsoft Docs
titleSuffix: Azure
description: Pochopit, jaké kódy odpovědí protokolu HTTP jsou vráceny z LEOŠ vytváření a koncový bod rozhraní API
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 04/16/2018
ms.author: v-geberr
ms.openlocfilehash: 9c7381d9dc2ecf302c85c6b4f1f24b24a28d9ebe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342805"
---
# <a name="luis-api-http-response-codes"></a>Kódy odpovědí HTTP LEOŠ rozhraní API
[Vytváření](https://aka.ms/luis-authoring-apis) a [koncový bod](https://aka.ms/luis-endpoint-apis) rozhraní API návratové kódy odpovědí HTTP. Při odpovědí na zprávy obsahují informace, které jsou specifické pro požadavek, je obecné stavový kód HTTP odpovědi. 

## <a name="common-status-codes"></a>Běžné kódy stavu
Následující tabulka uvádí některé z nejběžnějších kódy stavu odpovědi HTTP pro [vytváření](https://aka.ms/luis-authoring-apis) a [koncový bod](https://aka.ms/luis-endpoint-apis) rozhraní API:

|Kód|Rozhraní API|Vysvětlení|
|:--|--|--|
|400|Vytváření, koncový bod|Parametry žádosti jsou nesprávné, což znamená, že jsou požadovaných parametrů chybí, je poškozený nebo je příliš velký|
|400|Vytváření, koncový bod|text žádosti je nesprávný, což znamená, že je ve formátu JSON chybí, je poškozený nebo je příliš velký|
|401|Vytváření obsahu|Místo vytváření klíč použít klíč předplatného koncový bod,|
|401|Vytváření, koncový bod|neplatný, poškozený nebo prázdný klíč|
|401|Vytváření, koncový bod| klíč neodpovídá oblast|
|401|Vytváření obsahu|nejste vlastník nebo spolupracovníka|
|401|Vytváření obsahu|Neplatné pořadí volání rozhraní API|
|403|Vytváření, koncový bod|celkové měsíční klíče kvóty byl překročen limit|
|409|Koncový bod|aplikace je stále načítá.|
|410|Koncový bod|aplikace musí být retrained a publikovat|
|414|Koncový bod|dotaz přesahuje maximální limit pro počet znaků|
|429|Vytváření, koncový bod|Překročení omezení četnosti (počet požadavků za sekundu)|