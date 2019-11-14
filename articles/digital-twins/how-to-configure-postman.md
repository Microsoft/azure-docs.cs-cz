---
title: Jak nakonfigurovat post-Azure digitální vlákna | Microsoft Docs
description: Naučte se konfigurovat a používat metodu post pro testování rozhraní API digitálních vláken Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 8967b61115d2e2e644dea93cb236f8a7cdfcfcbd
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072290"
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

Nakonfigurujte aplikaci Azure Active Directory tak, aby používala tok implicitního udělení OAuth 2,0.

1. Otevřete podokno **oprávnění rozhraní API** pro registraci vaší aplikace. Vyberte tlačítko **Přidat oprávnění** . V podokně **oprávnění API pro vyžádání** vyberte **rozhraní API moje organizace používá** kartu a pak vyhledejte:
    
    1. `Azure Digital Twins`. Vyberte rozhraní API **digitálních vláken Azure** .

        [rozhraní API pro vyhledávání ![nebo digitální vlákna Azure](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png)](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Případně vyhledejte `Azure Smart Spaces Service`. Vyberte rozhraní API **služby Azure Smart Spaces** .

        [rozhraní API pro hledání ![pro inteligentní prostory Azure](../../includes/media/digital-twins-permissions/aad-app-search-api.png)](../../includes/media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Název a ID rozhraní API služby Azure AD, které se zobrazí, závisí na vašem tenantovi:
    > * Testovací klient a účty zákazníka by měli hledat `Azure Digital Twins`.
    > * Jiné účty Microsoft by měly hledat `Azure Smart Spaces Service`.

1. Vybrané rozhraní API se zobrazí jako **digitální vlákna Azure** ve stejném PODOKNĚ **oprávnění API pro žádosti** . Vyberte rozevírací seznam **načíst (1)** a potom zaškrtněte políčko **číst. zapsat** . Klikněte na tlačítko **Přidat oprávnění** .

    [![Přidání oprávnění rozhraní API](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. V závislosti na nastaveních vaší organizace možná budete muset provést další kroky, abyste tomuto rozhraní API udělili přístup správce. Pro další informace se obraťte na správce. Po schválení přístupu správce se sloupec **požadováno souhlasu správce** v podokně **oprávnění API** zobrazí jako pro vaše rozhraní API podobně jako u následujících:

    [schválení souhlasu správce ![](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png)](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

1. Nakonfigurujte druhý **identifikátor URI pro přesměrování** na `https://www.getpostman.com/oauth2/callback`.

    [![přidat identifikátor URI pro přesměrování post](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. Zkopírujte a zachovejte **ID aplikace** vaší aplikace Azure Active Directory. Používá se v následujících krocích.

   [ID aplikace ![Azure Active Directory](../../includes/media/digital-twins-permissions/aad-app-reg-app-id.png)](../../includes/media//digital-twins-permissions/aad-app-reg-app-id.png#lightbox)


## <a name="obtain-an-oauth-20-token"></a>Získání tokenu OAuth 2,0

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Nastavte a nakonfigurujte metodu post pro získání tokenu Azure Active Directory. Následně můžete pomocí získaného tokenu vytvořit ověřený požadavek HTTP na digitální vlákna Azure:

1. Pokud si chcete aplikaci stáhnout, navštivte [www.getpostman.com](https://www.getpostman.com/) .
1. Ověřte, jestli je vaše **autorizační adresa URL** správná. Měla by mít formát:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Název  | Nahradit hodnotou | Příklad |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Název vašeho tenanta nebo organizace | `microsoft` |

1. Vyberte kartu **autorizace** , vyberte **OAuth 2,0**a potom vyberte **získat nový přístupový token**.

    | Pole  | Hodnota |
    |---------|---------|
    | Typ udělení | `Implicit` |
    | Adresa URL zpětného volání | `https://www.getpostman.com/oauth2/callback` |
    | Adresa URL ověření | Použijte **autorizační adresu URL** z **kroku 2** . |
    | ID klienta | Použijte **ID aplikace** pro aplikaci Azure Active Directory, která se vytvořila nebo znovu použila z předchozí části. |
    | Rozsah | Ponechte prázdné |
    | Stav | Ponechte prázdné |
    | Ověřování klienta | `Send as Basic Auth header` |

1. Klient by teď měl vypadat takto:

    [Příklad ![klienta po straně](media/how-to-configure-postman/postman-oauth-token.png)](media/how-to-configure-postman/postman-oauth-token.png#lightbox)

1. Vyberte **token žádosti**.
  
1. Posuňte se dolů a vyberte **použít token**.

## <a name="make-a-multipart-post-request"></a>Vytvoření žádosti POST s více částmi

Po dokončení předchozích kroků nakonfigurujte metodu post, aby se ověřil ověřený požadavek HTTP na více než jedna z nich:

1. Na kartě **hlavičky** přidejte klíč HLAVIČKY požadavku HTTP **– typ obsahu** s hodnotou `multipart/mixed`.

   [![typ obsahu multipart/Mixed](media/how-to-configure-postman/content-type.png)](media/how-to-configure-postman/content-type.png#lightbox)

1. Serializace jiných než textových dat do souborů. Data JSON by se uložila jako soubor JSON.
1. Na kartě **tělo** vyberte `form-data`. 
1. Přidejte každý soubor tak, že mu přiřadíte název **klíče** a vyberete `File`.
1. Pak vyberte jednotlivé soubory pomocí tlačítka **zvolit soubor** .

   [Příklad ![klienta po straně](media/how-to-configure-postman/form-body.png)](media/how-to-configure-postman/form-body.png#lightbox)

   >[!NOTE]
   > * Klient po straně klienta nevyžaduje, aby bloky částí v částech měly ručně přiřazený **typ obsahu** nebo **Content-Disposition**.
   > * Pro každou část není nutné zadávat tato záhlaví.
   > * Pro celý požadavek musíte vybrat `multipart/mixed` nebo jiný vhodný **typ obsahu** .

1. Nakonec vyberte **Odeslat** a odešlete požadavek HTTP POST s více částmi. Stavový kód `200` nebo `201` označuje úspěšnou žádost. Zobrazí se také příslušná zpráva odpovědi.

## <a name="next-steps"></a>Další kroky

- Další informace o rozhraních API pro správu digitálních vláken a způsobu jejich použití najdete v článku [Jak používat rozhraní API pro správu digitálních vláken Azure](how-to-navigate-apis.md).

- K [Přidání objektů blob do entit Azure Digital](./how-to-add-blobs.md)requests použijte požadavky na více částí.

- Pokud se chcete dozvědět o ověřování pomocí rozhraní API pro správu, přečtěte si téma [ověřování pomocí rozhraní API](./security-authenticating-apis.md).
