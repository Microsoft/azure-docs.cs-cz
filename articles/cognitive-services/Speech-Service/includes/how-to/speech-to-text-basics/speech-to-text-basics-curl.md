---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 1ec0281145609f9ae06ad07e1ad2cfd2b7f9aba8
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940796"
---
V tomto rychlém startu se dozvíte, jak převést řeč na text pomocí služby Speech a kudrlinkou.

Základní informace o konceptech řeči a textu najdete v článku [Přehled](../../../speech-to-text.md) .

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-text-to-speech"></a>Převod textu na řeč

Na příkazovém řádku spusťte následující příkaz. Do příkazu bude nutné vložit následující hodnoty.
- Klíč předplatného služby Speech.
- Vaše oblast služby Speech.
- Cesta ke vstupnímu zvukovému souboru. Můžete vygenerovat zvukové soubory pomocí převodu [textu na řeč](../../../get-started-text-to-speech.md).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

Měla by se zobrazit odpověď podobná následující.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

Další informace najdete v [referenčních informacích k REST API převodu řeči na text](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text).
