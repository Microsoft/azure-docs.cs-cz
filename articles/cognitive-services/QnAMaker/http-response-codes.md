---
title: Kódy odpovědí protokolu HTTP rozhraní API – QnA Maker
titleSuffix: Azure Cognitive Services
description: Zjistěte, jaké kódy odpovědí protokolu HTTP jsou vráceny z rozhraní API nástroje QnA Maker. To vám pomůže vyřešit všechny chyby.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 2364c42da22c9a8871928192ea825828d7dafb60
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911100"
---
# <a name="qna-maker-api-http-response-codes"></a>Kódy odpovědí protokolu HTTP rozhraní API nástroje QnA Maker
[Správu](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) a predikcí rozhraní API vrací kódy odpovědí protokolu HTTP. Zprávy s odezvami zahrnout informace specifické pro požadavek, je obecné stavového kódu odpovědi HTTP. 

### <a name="management"></a>Správa

|Kód|Vysvětlení|
|:--|--|
|2xx|Úspěch|
|400|Parametry žádosti jsou nesprávné, což znamená, že požadované parametry jsou chybí, je poškozený nebo je příliš velký|
|400|tělo žádosti je nesprávná, což znamená, že je ve formátu JSON chybí, je poškozený nebo je příliš velký|
|401|Neplatný klíč|
|403|Je zakázané - nemáte správná oprávnění|
|404|KB neexistuje|