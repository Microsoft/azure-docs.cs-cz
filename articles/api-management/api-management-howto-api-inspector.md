---
title: Kurz – ladění rozhraní API v Azure API Management pomocí trasování požadavků
description: Postupujte podle kroků v tomto kurzu a Povolte trasování a kontrolu kroků zpracování požadavků v Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 531e346569b85ababc382f997fd7764a92b3d05f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542310"
---
# <a name="tutorial-debug-your-apis-using-request-tracing"></a>Kurz: ladění rozhraní API pomocí trasování požadavků

V tomto kurzu se dozvíte, jak kontrolovat (sledovat) zpracování žádostí ve službě Azure API Management, které vám pomůžou ladit a řešit potíže s rozhraním API. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Trasovat ukázkové volání
> * Kontrola kroků zpracování žádostí

:::image type="content" source="media/api-management-howto-api-inspector/api-inspector-001.png" alt-text="Nástroj pro inspekci rozhraní API":::

## <a name="prerequisites"></a>Předpoklady

+ Seznamte se s [terminologií služby Azure API Management](api-management-terminology.md).
+ Dokončete následující rychlý Start: [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
+ Dokončete následující kurz: [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).

## <a name="verify-allow-tracing-setting"></a>Ověřit nastavení povolení trasování 

Musí být povolené nastavení **Povolit trasování** pro předplatné používané pro vaše rozhraní API. Pokud používáte integrované předplatné s přístupem All-Access, je ve výchozím nastavení povoleno. Pokud chcete na portálu ověřit, přejděte na instanci API Management a vyberte **předplatná**.

   :::image type="content" source="media/api-management-howto-api-inspector/allow-tracing.png" alt-text="Povolení trasování pro odběr":::

## <a name="trace-a-call"></a>Trasování volání

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a přejděte k instanci API Management.
1. Vyberte **Rozhraní API**.
1. V seznamu rozhraní API vyberte  **ukázková konferenční konference API** .
1. Vyberte kartu **Test**.
1. Vyberte operaci **GetSpeakers**.
1. Potvrďte, že Hlavička požadavku HTTP zahrnuje **OCP-APIM-trace: true** a platnou hodnotu pro **OCP-APIM-Subscription-Key**. Pokud není, vyberte **+ Přidat hlavičku** a přidejte záhlaví.
1. Vyberte **Odeslat** pro volání rozhraní API.

  :::image type="content" source="media/api-management-howto-api-inspector/06-debug-your-apis-01-trace-call.png" alt-text="Konfigurace trasování rozhraní API":::

> [!TIP]
> Pokud se **OCP-APIM-Subscription-Key** v požadavku HTTP nevyplní automaticky, můžete ho načíst na portálu. Vyberte **předplatná** a otevřete místní nabídku (**...**) pro předplatného. Vyberte **Zobrazit/skrýt klíče**. V případě potřeby můžete také znovu vygenerovat klíče. Pak do záhlaví přidejte klíč.

## <a name="review-trace-information"></a>Kontrola informací o trasování

1. Po dokončení volání přejdete na kartu **trasování** v **odpovědi HTTP**.
1. Vyberte některý z následujících odkazů pro přechod na podrobné informace o trasování: **příchozí**, **back-end**, **odchozí**.

     :::image type="content" source="media/api-management-howto-api-inspector/response-trace.png" alt-text="Zkontrolovat trasování odpovědi":::

    * **Příchozí** – zobrazí původní požadavek API Management přijatý od volajícího a zásady použité pro požadavek. Pokud jste například přidali zásady v [kurzu: Transformujte a Chraňte své rozhraní API](transform-api.md), zobrazí se tady.

    * **Back-end** – zobrazuje požadavky API Management odeslané do back-endu rozhraní API a odpověď, kterou přijal.

    * **Odchozí** – zobrazí zásady použité pro odpověď před odesláním zpět volajícímu.

    > [!TIP]
    > Každý krok také ukazuje uplynulý čas od přijetí požadavku službou API Management.

1. Na kartě **zpráva** zobrazuje záhlaví **OCP-APIM-Trace-Location** umístění dat trasování uložených ve službě Azure Blob Storage. V případě potřeby pro načtení trasování přejít do tohoto umístění.

     :::image type="content" source="media/api-management-howto-api-inspector/response-message.png" alt-text="Umístění trasování v Azure Storage":::
## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Trasovat ukázkové volání
> * Kontrola kroků zpracování žádostí

Přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Použití revizí](api-management-get-started-revise-api.md)
