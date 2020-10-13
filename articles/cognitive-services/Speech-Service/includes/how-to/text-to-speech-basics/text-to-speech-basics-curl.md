---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 029cb1e59876eb01c609ef8b7ba0412ed35a2a1b
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940735"
---
V tomto rychlém startu se dozvíte, jak převést text na řeč pomocí služby Speech a kudrlinkou.

Základní informace o konceptech textu na řeč najdete v článku [Přehled](../../../text-to-speech.md) .

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-text-to-speech"></a>Převod textu na řeč

Na příkazovém řádku spusťte následující příkaz. Do příkazu bude nutné vložit následující hodnoty.
- Klíč předplatného služby Speech.
- Vaše oblast služby Speech.

Můžete si také změnit následující hodnoty.
- `X-Microsoft-OutputFormat`Hodnota hlavičky, která určuje formát výstupu zvuku. Seznam podporovaných formátů zvukových výstupů můžete najít v [Referenční příručce REST API převodu textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech#audio-outputs).
- Výstupní hlas. Pokud chcete získat seznam hlasů dostupných pro váš koncový bod řeči, přečtěte si další část.
- Výstupní soubor. V tomto příkladu nasměrujeme odpověď ze serveru do souboru s názvem `output.wav` .

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/text-to-speech.sh":::

## <a name="list-available-voices-for-your-speech-endpoint"></a>Výpis dostupných hlasů pro koncový bod řeči

Pokud chcete zobrazit seznam dostupných hlasů pro koncový bod řeči, spusťte následující příkaz.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="request":::

Měla by se zobrazit odpověď podobná následující.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="response":::
