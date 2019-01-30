---
title: Co je rozhraní API pro rozpoznávání mluvčího?
titleSuffix: Azure Cognitive Services
description: Používejte pokročilé algoritmy rozhraní API pro rozpoznávání mluvčího ve službě Cognitive Services k ověření a identifikaci mluvčího.
services: cognitive-services
author: dwlin
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: dwlin
ms.openlocfilehash: 7267663bae71b793923bc10a3b52ab54c12168af
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216757"
---
# <a name="speaker-recognition-api"></a>Rozhraní API pro rozpoznání mluvčího

Vítají vás rozhraní API pro rozpoznávání mluvčího služby Azure Cognitive Services. Rozhraní API pro rozpoznávání mluvčího jsou cloudová rozhraní API, která poskytují nejpokročilejší algoritmy pro ověření a identifikaci mluvčího. Rozpoznávání mluvčího lze rozdělit do dvou kategorií: ověření mluvčího a identifikace mluvčího.


## <a name="speaker-verification"></a>Ověření mluvčího

Hlas má, stejně jako otisk prstu, jedinečné rysy, které je možné použít k identifikaci osoby.  Z použití hlasu jako signálu pro řízení přístupu a ověřování se stává nový inovativní nástroj – v podstatě nabízí vyšší úroveň zabezpečení, která zákazníkům zjednodušuje proces ověřování.

Rozhraní API pro ověření mluvčího mohou uživatele automaticky ověřit pomocí hlasu nebo řeči.

### <a name="enrollment"></a>Registrace

Registrace ověření mluvčího je závislá na textu. To znamená, že mluvčí si musí zvolit konkrétní frázi, která se použije jak při registraci, tak ve fázi ověřování.

Při registraci se nahraje hlas mluvčího, jak říká nějakou frázi, a potom se z této fráze extrahuje několik rysů, podle kterých se fráze rozpozná. Společně tyto extrahované rysy a zvolená fráze vytvoří jedinečný hlasový podpis.

### <a name="verification"></a>Ověření

Při ověřování se vstupní hlas a fráze porovná s hlasovým podpisem a frází registrace. Ověří se tak, zda jde o stejnou osobu a zda používá správnou frázi.

Další podrobnosti o ověření mluvčího, najdete v rozhraní API [mluvčího – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Identifikace mluvčího

Rozhraní API pro identifikaci mluvčího mohou ze skupiny potenciálních mluvčích automaticky identifikovat osobu mluvící na zvukovém souboru. Vstupní zvuk se spáruje s poskytnutou skupinou mluvčích, a pokud se zjistí shoda, bude výsledkem identita mluvčího.

Všichni mluvčí by měli nejprve projít registrací, aby se jejich hlas zaregistroval do systému a vytvořil se otisk hlasu.


### <a name="enrollment"></a>Registrace

Registrace identifikace mluvčího je na textu nezávislá, což znamená, že neexistují žádná omezení, co může mluvčí říct. Hlas mluvčího se nahraje a pak se z něj extrahuje několik rysů, které vytvoří jedinečný hlasový podpis.


### <a name="recognition"></a>Rozpoznání

Během rozpoznání je k dispozici zvuk neznámého mluvčího a potenciální skupina mluvčích. Vstupní hlas se porovná se všemi mluvčími, aby se rozpoznalo, komu hlas patří, a pokud se najde shoda, výsledkem bude identita mluvčího.

Další podrobnosti o identifikaci mluvčího, najdete v rozhraní API [mluvčího – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).
