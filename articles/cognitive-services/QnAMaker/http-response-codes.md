---
title: Kódy odpovědí protokolu HTTP rozhraní API – QnA Maker
titleSuffix: Azure Cognitive Services
description: Pochopit, jaké kódy odpovědí protokolu HTTP jsou vráceny z rozhraní API nástroje QnA Maker
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 10/09/2018
ms.author: tulasim
ms.openlocfilehash: 96782d32817e4989c02e0ed098d7772c80aa26c8
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079677"
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