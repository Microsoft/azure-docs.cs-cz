---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: v-demjoh
ms.openlocfilehash: f28dd3e94db9d16645bf0d63841a17ee66defd7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776646"
---
Jednou ze základních funkcí služby Speech je schopnost rozpoznávat lidské rozpoznávání a překládat ho do jiných jazyků. V tomto rychlém startu se naučíte používat sadu Speech SDK ve vašich aplikacích a produktech k provádění vysoce kvalitního překladu řeči. Tento rychlý Start převádí řeč z mikrofonu na text v jiném jazyce.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../get-started.md).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>Nastavit zdrojový a cílový jazyk

Tento příkaz volá funkci Speech CLI k překladu řeči z mikrofonu z italštiny na francouzštinu.

```shell
 spx translate --microphone --source it-IT --target fr
```
