---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 0964872d03d6e321d25d51a18edbb4a6f0be8a4f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424718"
---
V tomto rychlém startu se dozvíte, jak převést řeč na text pomocí služby Speech a kudrlinkou.

Základní informace o konceptech řeči a textu najdete v článku [Přehled](../../../speech-to-text.md) .

## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-speech-to-text"></a>Převod řeči na text

Na příkazovém řádku spusťte následující příkaz. Do příkazu bude nutné vložit následující hodnoty.
- Klíč předplatného služby Speech.
- Vaše oblast služby Speech.
- Cesta ke vstupnímu zvukovému souboru. Můžete vygenerovat zvukové soubory pomocí převodu [textu na řeč](../../../get-started-text-to-speech.md).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

Měla by se zobrazit odpověď podobná následující.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

Další informace najdete v [referenčních informacích k REST API převodu řeči na text](../../../rest-speech-to-text.md).