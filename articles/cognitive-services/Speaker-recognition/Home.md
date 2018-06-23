---
title: Rozpoznávání mluvčího rozhraní API | Microsoft Docs
description: Pomocí pokročilých algoritmů pro ověření mluvčího a identifikaci mluvčího s rozhraním API pro rozpoznávání mluvčího v kognitivní služby.
services: cognitive-services
author: dwlin
manager: zhang
ms.service: cognitive-services
ms.component: speaker-recognition
ms.topic: article
ms.date: 03/20/2016
ms.author: dwlin
ms.openlocfilehash: 6d5e4e4bbe0cb5e57d2556f680ffcf8d16ee1818
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342626"
---
# <a name="speaker-recognition-api"></a>Rozhraní API pro rozpoznání mluvčího

Vítá vás rozhraní API pro rozpoznávání mluvčího společnosti Microsoft. Rozhraní API pro rozpoznávání mluvčího jsou rozhraní API založené na cloudu, které poskytuje nejmodernější algoritmy pro ověření mluvčího a identifikaci mluvčího. Rozpoznávání mluvčího je možné rozdělit do dvou kategorií: ověření mluvčího a identifikaci mluvčího.


## <a name="speaker-verification"></a>Ověření mluvčího


Hlas má jedinečných charakteristik, které můžete použít k identifikaci osoby, stejně jako otisk prstu.  Použití hlasové jako signál pro scénáře řízení a ověřování přístupu vyplývá jako nový nástroj inovativní – v podstatě nabídky úroveň v zabezpečení, která zjednodušuje možnosti ověřování pro zákazníky.

Rozhraní API pro ověření mluvčího můžete automaticky ověřte a ověření uživatelů pomocí jejich hlasové nebo řeči.

### <a name="enrollment"></a>Registrace

Registrace pro ověření mluvčího je závislém textu, což znamená, že mluvčí muset zvolit konkrétní heslo k použití během fáze registrace a ověření. 

V registraci mluvčího hlasu se zaznamenává oznámením konkrétní frázi, pak se extrahují celou řadu funkcí a je rozpoznány zvolené frázi. Společně extrahované funkce a zvolený fráze formuláře jedinečný hlasové podpis.

### <a name="verification"></a>Ověření
###
V ověření, vstupní hlas a frázi porovná s hlasové podpis registraci a frázi – Chcete-li ověřit, jestli jsou z stejná osoba, a pokud říkají správné heslo.

Další podrobnosti o ověření mluvčího rozhraní API naleznete [mluvčího - ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Identifikace mluvčího

Rozhraní API pro identifikaci mluvčího můžete automaticky identifikaci osoby hovořícího v zvukový soubor, danou skupinu potenciální mluvčí. Vstupní zvuk je spárován proti zadané skupiny řečníci, a v případě, že je nalezena shoda, je vrácen mluvčího identity.

Všechny mluvčí by měl projít proces registrace nejprve potřebujete hlasu zaregistrován v systému a mít hlasové tisku vytvořili.


### <a name="enrollment"></a>Registrace

Registrace pro identifikaci mluvčího je nezávislé textu, což znamená, že neexistují žádná omezení mluvčímu uvádí ve zvukovém souboru. Se zaznamenává mluvčího hlasu a celou řadu funkcí, které se extrahují k vytvoření podpisu jedinečný hlasu. 


### <a name="recognition"></a>Uznání

Zvuk neznámou mluvčího, společně s potenciální skupiny řečníci, je k dispozici při rozpoznávání. Vstupní hlasu se porovná proti všechny řečníci, aby bylo možné zjistit jejichž hlasu je, a pokud je nalezena shoda, vrátí se identity mluvčímu.


Další podrobnosti o identifikaci mluvčího rozhraní API naleznete [mluvčího - identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).
