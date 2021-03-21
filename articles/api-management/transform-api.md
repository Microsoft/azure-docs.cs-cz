---
title: Kurz – transformace a ochrana rozhraní API v Azure API Management | Microsoft Docs
description: V tomto kurzu se dozvíte, jak chránit rozhraní API v API Management s využitím zásad transformace a omezování (omezení rychlosti).
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/28/2020
ms.author: apimpm
ms.openlocfilehash: 979bdaa1e0dac4f45a321abda2a208f46983f9cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010227"
---
# <a name="tutorial-transform-and-protect-your-api"></a>Kurz: transformace a ochrana rozhraní API

V tomto kurzu se dozvíte, jak transformovat rozhraní API tak, aby neodhalilo informace o privátním back-endu. Můžete například chtít skrýt informace o technologických zásobnících, které běží na back-endu. Můžete také chtít skrýt původní adresy URL, které se zobrazí v těle odpovědi HTTP rozhraní API, a přesměrovat je na bránu APIM.

V tomto kurzu se také dozvíte, jak snadné je přidat ochranu pro back-end rozhraní API konfigurací omezení přenosové rychlosti pomocí Azure API Management. Můžete například chtít omezit rychlost volání rozhraní API, takže vývojáři tyto rozhraní API nepoužijí. Další informace najdete v tématu [zásady API Management](api-management-policies.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> -   Odebrat hlavičky odpovědi transformováním rozhraní API
> -   Nahradit původní adresy URL v těle odpovědi rozhraní API adresami URL brány služby APIM
> -   Ochrana rozhraní API přidáním zásady omezení četnosti (omezování)
> -   Otestovat transformace

:::image type="content" source="media/transform-api/api-management-management-console.png" alt-text="Zásady na portálu":::

## <a name="prerequisites"></a>Předpoklady

-   Seznamte se s [terminologií služby Azure API Management](api-management-terminology.md).
-   Seznamte se s [konceptem zásad ve službě Azure API Management](api-management-howto-policies.md).
-   Dokončete následující rychlý Start: [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
-   Projděte si také následující kurz: Navíc kurzu: [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Odebrat hlavičky odpovědi transformováním rozhraní API

V této části se dozvíte, jak skrýt hlavičky HTTP, které nechcete zobrazovat uživatelům. Tento příklad ukazuje, jak odstranit následující hlavičky v odpovědi HTTP:

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>Test původní odpovědi

Původní odpověď zobrazíte následovně:

1. V instanci služby API Management vyberte možnost **rozhraní API**.
1. V seznamu rozhraní API vyberte **ukázková konferenční konference API** .
1. V horní části obrazovky vyberte kartu **test** .
1. Vyberte operaci **Getspeakers** a vyberte **Odeslat**.

Původní odpověď by měla vypadat nějak takto:

:::image type="content" source="media/transform-api/original-response.png" alt-text="Původní odpověď rozhraní API":::

Jak vidíte, odpověď obsahuje hlavičky **x-ASPNET-Version** a **x-Power-by** .

### <a name="set-the-transformation-policy"></a>Nastavení zásady transformací

1. Vyberte **ukázková konferenční rozhraní API** pro  >    >  **všechny operace**.
4. V části **odchozí zpracování** vyberte ikonu Editor kódu ( **</>** ).

   :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png" alt-text="Přejít na odchozí zásady" border="false":::

1. Umístěte kurzor do elementu **&lt; Outbound &gt;** a v pravém horním rohu vyberte **Zobrazit fragmenty** .
1. V pravém okně v části **zásady transformace** vyberte možnost **nastavit hlavičku HTTP** dvakrát (pro vložení dvou fragmentů zásad).

   :::image type="content" source="media/transform-api/transform-api.png" alt-text="Nastavit zásady hlaviček protokolu HTTP":::

1. Upravte **\<outbound>** kód tak, aby vypadal takto:

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   :::image type="content" source="media/transform-api/set-policy.png" alt-text="Set HTTP header"::: (Nastavit hlavičku HTTP)

1. Vyberte **Uložit**.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Nahradit původní adresy URL v těle odpovědi rozhraní API adresami URL brány služby APIM

V této části se dozvíte, jak skrýt původní adresy URL, které se zobrazí v těle odpovědi HTTP rozhraní API, a přesměrovat je na bránu APIM.

### <a name="test-the-original-response"></a>Test původní odpovědi

Původní odpověď zobrazíte následovně:

1. Vyberte **Test rozhraní API pro ukázkovou konferenci**  >  .
1. Vyberte operaci **Getspeakers** a vyberte **Odeslat**.

    Jak vidíte, odpověď obsahuje původní adresy URL back-endu:

    :::image type="content" source="media/transform-api/original-response2.png" alt-text="Původní adresy URL v odpovědi":::


### <a name="set-the-transformation-policy"></a>Nastavení zásady transformací

1.  Vyberte **ukázková konferenční rozhraní API**  >  **všechny operace**  >  **Návrh**.
1.  V části **odchozí zpracování** vyberte ikonu Editor kódu ( **</>** ).
1.  Umístěte kurzor do elementu **&lt; Outbound &gt;** a v pravém horním rohu vyberte **Zobrazit fragmenty** .
1.  V pravém okně v části **zásady transformace** vyberte **adresy URL masky v obsahu**. 
1.  Vyberte **Uložit**.

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Ochránit rozhraní API přidáním zásady omezování četnosti (omezení využití sítě)

Tato část ukazuje, jak přidat ochranu back-endovému rozhraní API nakonfigurováním omezování četnosti. Můžete například chtít omezit rychlost volání rozhraní API tak, aby vývojáři nepoužívali rozhraní API. V tomto příkladu je limit pro každé ID předplatného nastavený na 3 hovory za 15 sekund. Po 15 sekundách může vývojář zkusit volat rozhraní API znovu.

1.  Vyberte **ukázková konferenční rozhraní API**  >  **všechny operace**  >  **Návrh**.
1.  V části **příchozí zpracování** vyberte ikonu Editor kódu ( **</>** ).
1.  Umístěte kurzor dovnitř elementu **&lt; vstupu &gt;** a vyberte **Zobrazit fragmenty kódu** v pravém horním rohu.

    :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png" alt-text="Nastavení příchozí zásady" border="false":::

1.  V pravém okně v části **zásady omezení přístupu** vyberte **+ omezit četnost volání na klíč**.
1.  Upravte kód **sazby-limit-by-Key** (v **\<inbound\>** elementu) na následující kód:

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>Otestovat transformace

V tomto okamžiku, pokud se podíváte na kód v editoru kódu, vaše zásady vypadají takto:

   ```
   <policies>
      <inbound>
        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
        <base />
      </inbound>
      <backend>
        <base />
      </backend>
      <outbound>
        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
        <redirect-content-urls />
        <base />
      </outbound>
      <on-error>
        <base />
      </on-error>
   </policies>
   ```

Zbývající část článku testuje transformace zásad, které jste nastavili.

### <a name="test-the-stripped-response-headers"></a>Test odebraných hlaviček odpovědi

1. Vyberte **Test rozhraní API pro ukázkovou konferenci**  >  .
1. Vyberte operaci **Getspeakers** a vyberte **Odeslat**.

    Jak vidíte, hlavičky byly odstraněny:

    :::image type="content" source="media/transform-api/final-response1.png" alt-text="Vynechají hlavičky odpovědí":::

### <a name="test-the-replaced-url"></a>Test nahrazené adresy URL

1. Vyberte **Test rozhraní API pro ukázkovou konferenci**  >  .
1. Vyberte operaci **Getspeakers** a vyberte **Odeslat**.

    Jak vidíte, adresa URL byla nahrazena.

    :::image type="content" source="media/transform-api/final-response2.png" alt-text="Nahrazená adresa URL":::

### <a name="test-the-rate-limit-throttling"></a>Test omezování četnosti (omezení využití sítě)

1. Vyberte **Test rozhraní API pro ukázkovou konferenci**  >  .
1. Vyberte operaci **GetSpeakers**. V řádku vyberte **Odeslat** třikrát.

    Po odeslání žádosti třikrát obdržíte **429 příliš mnoho** odpovědí na požadavky.

    :::image type="content" source="media/transform-api/test-throttling.png" alt-text="Příliš mnoho žádostí":::

1. Počkejte 15 sekund nebo to a pak znovu vyberte **Odeslat** . Tentokrát by se už měla objevit odpověď **200 OK**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
>
> -   Odebrat hlavičky odpovědi transformováním rozhraní API
> -   Nahradit původní adresy URL v těle odpovědi rozhraní API adresami URL brány služby APIM
> -   Ochránit rozhraní API přidáním zásady omezování četnosti (omezení využití sítě)
> -   Otestovat transformace

Přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Monitorování rozhraní API](api-management-howto-use-azure-monitor.md)
