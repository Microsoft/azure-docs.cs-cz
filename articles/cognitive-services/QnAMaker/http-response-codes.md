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
ms.date: 10/09/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 8a39e8ee783d999bf3851ef1ba564e8f24de7910
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726948"
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