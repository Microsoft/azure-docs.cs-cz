---
title: Jak nakonfigurovat post-Azure digitální vlákna | Microsoft Docs
description: Naučte se konfigurovat a používat metodu post pro testování rozhraní API pro digitální vlákna Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 377639d7a88478308709743ab842db71028686ed
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023306"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Jak nakonfigurovat metodu post pro digitální vlákna Azure

Tento článek popisuje, jak nakonfigurovat klienta programu post REST pro interakci s a testováním rozhraní API pro správu digitálních vláken Azure. Konkrétně popisuje:

* Jak nakonfigurovat aplikaci Azure Active Directory pro použití procesu implicitního udělení žádosti OAuth 2,0.
* Jak používat klienta post REST k vytváření tokenů nesoucích požadavky HTTP na rozhraní API pro správu.
* Jak používat metodu POST k provádění požadavků POST na rozhraní API pro správu.

## <a name="postman-summary"></a>Souhrn příspěvku

Začněte využívat digitální vlákna Azure pomocí nástroje klienta REST, jako je například [post](https://www.getpostman.com/) , pro přípravu místního testovacího prostředí. Klient pro odesílání pomáhá rychle vytvářet komplexní požadavky HTTP. Stáhněte desktopovou verzi klienta po přechodu na [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Držitelem je nástroj](https://www.getpostman.com/) pro testování REST, který vyhledává klíčové funkce požadavku HTTP do užitečného grafického uživatelského rozhraní a GUI založeného na modulu plug-in.

Prostřednictvím klienta pro řešení můžou vývojáři řešení určit druh požadavku HTTP (*post*, *Get*, *Update*, *patch*a *Delete*), koncového bodu rozhraní API pro volání a používání protokolu SSL. Post také podporuje přidávání hlaviček požadavků HTTP, parametrů, formulářů-dat a institucí.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Konfigurace Azure Active Directory pro použití procesu implicitního udělení žádosti OAuth 2,0

1. Pomocí kroků v [rychlém](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) startu vytvořte a nakonfigurujte aplikaci Azure Active Directory. Alternativně můžete znovu použít stávající registraci aplikace.

    [![nakonfigurovat nový identifikátor URI pro přesměrování po odeslání](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. Nyní přidejte **identifikátor URI pro přesměrování** do `https://www.getpostman.com/oauth2/callback`.

1. Zaškrtněte políčko **implicitní udělení** > **přístupové tokeny** , abyste umožnili použití toku implicitního udělení OAuth 2,0. Vyberte **Konfigurovat**a pak **Uložit**.

1. Zkopírujte **ID klienta** vaší aplikace Azure Active Directory.

## <a name="obtain-an-oauth-20-token"></a>Získání tokenu OAuth 2,0

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Nastavte a nakonfigurujte metodu post pro získání tokenu Azure Active Directory. Následně můžete pomocí získaného tokenu vytvořit ověřený požadavek HTTP na digitální vlákna Azure:

1. Ověřte, jestli je vaše **autorizační adresa URL** správná. Měla by mít formát:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Name (Název)  | Nahradit hodnotou | Příklad: |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Název vašeho tenanta nebo organizace. Místo alfanumerického **ID tenanta** vaší Azure Active Directory registrace aplikace použijte popisný název. | `microsoft` |

1. Pokud si chcete aplikaci stáhnout, navštivte [www.getpostman.com](https://www.getpostman.com/) .

1. Chceme vytvořit žádost o získání. Vyberte kartu **autorizace** , vyberte OAuth 2,0 a potom vyberte **získat nový přístupový token**.

    | Pole  | Hodnota |
    |---------|---------|
    | Typ udělení | `Implicit` |
    | Adresa URL zpětného volání | `https://www.getpostman.com/oauth2/callback` |
    | Adresa URL ověření | Použijte **autorizační adresu URL** z **kroku 1** . |
    | ID klienta | Použijte **ID aplikace** pro aplikaci Azure Active Directory, která se vytvořila nebo znovu použila z předchozí části. |
    | Rozsah | Ponechte prázdné |
    | Stav | Ponechte prázdné |
    | Ověřování klienta | `Send as Basic Auth header` |

1. Klient by teď měl vypadat takto:

    [Příklad tokenu klienta ![post](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Vyberte **token žádosti**.
  
1. Posuňte se dolů a vyberte **použít token**.

## <a name="make-a-multipart-post-request"></a>Vytvoření žádosti POST s více částmi

Po dokončení předchozích kroků nakonfigurujte metodu post, aby se ověřil ověřený požadavek HTTP na více než jedna z nich:

1. Na kartě **hlavičky** přidejte klíč HLAVIČKY požadavku HTTP **– typ obsahu** s hodnotou `multipart/mixed`.

   [![určení typu obsahu multipart/Mixed](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. Serializace jiných než textových dat do souborů. Data JSON by se uložila jako soubor JSON.
1. Na kartě **tělo** vyberte `form-data`. 
1. Přidejte každý soubor tak, že mu přiřadíte název **klíče** a vyberete `File`.
1. Pak vyberte jednotlivé soubory pomocí tlačítka **zvolit soubor** .

   [Příklad textu ![formulářového klienta](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * Klient po straně klienta nevyžaduje, aby bloky částí v částech měly ručně přiřazený **typ obsahu** nebo **Content-Disposition**.
   > * Pro každou část není nutné zadávat tato záhlaví.
   > * Pro celý požadavek musíte vybrat `multipart/mixed` nebo jiný vhodný **typ obsahu** .

1. Nakonec vyberte **Odeslat** a odešlete požadavek HTTP POST s více částmi. Stavový kód `200` nebo `201` označuje úspěšnou žádost. V klientském rozhraní se zobrazí odpovídající zpráva odpovědi.

1. Ověřte data požadavku HTTP POST voláním koncového bodu rozhraní API: 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>Další kroky

- Další informace o rozhraních API pro správu digitálních vláken a způsobu jejich použití najdete v článku [Jak používat rozhraní API pro správu digitálních vláken Azure](how-to-navigate-apis.md).

- K [Přidání objektů blob do entit Azure Digital](./how-to-add-blobs.md)requests použijte požadavky na více částí.

- Pokud se chcete dozvědět o ověřování pomocí rozhraní API pro správu, přečtěte si téma [ověřování pomocí rozhraní API](./security-authenticating-apis.md).
