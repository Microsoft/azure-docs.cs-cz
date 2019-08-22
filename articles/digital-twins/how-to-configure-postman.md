---
title: Jak nakonfigurovat metodu post pro digitální vlákna Azure | Microsoft Docs
description: Jak nakonfigurovat metodu post pro digitální vlákna Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: v-adgera
ms.openlocfilehash: 66dbfd09ef07740c07ddb010b73e33e783340e5a
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873652"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Jak nakonfigurovat metodu post pro digitální vlákna Azure

Tento článek popisuje, jak nakonfigurovat klienta programu post REST pro interakci s a testováním rozhraní API pro správu digitálních vláken Azure. Konkrétně popisuje:

* Jak nakonfigurovat aplikaci Azure Active Directory pro použití procesu implicitního udělení žádosti OAuth 2,0.
* Jak používat klienta post REST k vytváření tokenů nesoucích požadavky HTTP na rozhraní API pro správu.
* Jak používat metodu POST k provádění požadavků POST na rozhraní API pro správu.

## <a name="postman-summary"></a>Souhrn příspěvku

Začněte využívat digitální vlákna Azure pomocí nástroje klienta REST, jako je například [post](https://www.getpostman.com/) , pro přípravu místního testovacího prostředí. Klient pro odesílání pomáhá rychle vytvářet komplexní požadavky HTTP. Stáhněte desktopovou verzi klienta po přechodu na [www.getpostman.com/apps](https://www.getpostman.com/apps).

[](https://www.getpostman.com/) Držitelem je nástroj pro testování REST, který vyhledává klíčové funkce požadavku HTTP do užitečného grafického uživatelského rozhraní a GUI založeného na modulu plug-in.

Prostřednictvím klienta pro řešení můžou vývojáři řešení určit druh požadavku HTTP (*post*, *Get*, *Update*, *patch*a *Delete*), koncového bodu rozhraní API pro volání a používání protokolu SSL. Post také podporuje přidávání hlaviček požadavků HTTP, parametrů, formulářů-dat a institucí.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Konfigurace Azure Active Directory pro použití procesu implicitního udělení žádosti OAuth 2,0

Nakonfigurujte aplikaci Azure Active Directory tak, aby používala tok implicitního udělení OAuth 2,0.

1. Pomocí kroků v [tomto rychlém](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) startu vytvořte aplikaci Azure AD typu Native. Nebo můžete znovu použít stávající registraci nativní aplikace.

1. V části **požadovaná oprávnění**vyberte **Přidat** a do pole **Přidat přístup k rozhraní API**zadejte **digitální vlákna Azure** . Pokud se vám toto rozhraní API nepodaří najít, vyhledejte místo toho **Azure Smart Spaces**. Pak vyberte **udělit oprávnění > delegovaná oprávnění** a **Hotovo**.

    [![Registrace aplikací Azure Active Directory přidání rozhraní API](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Vyberte **manifest** pro otevření manifestu aplikace pro vaši aplikaci. Nastavte *oauth2AllowImplicitFlow* na `true`.

    [![Azure Active Directory implicitní tok](media/how-to-configure-postman/implicit-flow.png)](media/how-to-configure-postman/implicit-flow.png#lightbox)

1. Nakonfigurujte **adresu URL odpovědi** na `https://www.getpostman.com/oauth2/callback`.

    [![Adresa URL odpovědi Azure Active Directory](media/how-to-configure-postman/reply-url.png)](media/how-to-configure-postman/reply-url.png#lightbox)

1. Zkopírujte a zachovejte **ID aplikace** vaší aplikace Azure Active Directory. Používá se v následujících krocích.

## <a name="obtain-an-oauth-20-token"></a>Získání tokenu OAuth 2,0

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Nastavte a nakonfigurujte metodu post pro získání tokenu Azure Active Directory. Následně můžete pomocí získaného tokenu vytvořit ověřený požadavek HTTP na digitální vlákna Azure:

1. Pokud si chcete aplikaci stáhnout, navštivte [www.getpostman.com](https://www.getpostman.com/) .
1. Ověřte, jestli je vaše **autorizační adresa URL** správná. Měla by mít formát:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Name  | Nahradit hodnotou | Příklad |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Název vašeho tenanta nebo organizace | `microsoft` |

1. Vyberte kartu **autorizace** , vyberte **OAuth 2,0**a potom vyberte **získat nový přístupový token**.

    | Pole  | Value |
    |---------|---------|
    | Typ udělení | `Implicit` |
    | Adresa URL pro zpětné volání | `https://www.getpostman.com/oauth2/callback` |
    | Adresa URL ověření | Použijte **autorizační adresu URL** z kroku 2. |
    | ID klienta | Použijte **ID aplikace** pro aplikaci Azure Active Directory, která se vytvořila nebo změnila účel z předchozí části. |
    | Scope | Ponechte prázdné |
    | State | Ponechte prázdné |
    | Ověřování klientů | `Send as Basic Auth header` |

1. Klient by teď měl vypadat takto:

    [![Příklad klienta pro vystavení](media/how-to-configure-postman/postman-oauth-token.png)](media/how-to-configure-postman/postman-oauth-token.png#lightbox)

1. Vyberte **token žádosti**.

    >[!TIP]
    >Pokud se zobrazí chybová zpráva "OAuth 2 nelze dokončit", vyzkoušejte následující:
    > * Zavřete okno post a znovu ho otevřete a zkuste to znovu.
  
1. Posuňte se dolů a vyberte **použít token**.

<div id="multi"></div>

## <a name="make-a-multipart-post-request"></a>Vytvoření žádosti POST s více částmi

Po dokončení předchozích kroků nakonfigurujte metodu post, aby se ověřil ověřený požadavek HTTP na více než jedna z nich:

1. Na kartě **záhlaví** přidejte klíč HLAVIČKY požadavku HTTP **– typ obsahu** s hodnotou `multipart/mixed`.

   [![Typ obsahu multipart/Mixed](media/how-to-configure-postman/content-type.png)](media/how-to-configure-postman/content-type.png#lightbox)

1. Serializace jiných než textových dat do souborů. Data JSON by se uložila jako soubor JSON.
1. Na kartě **tělo** přidejte jednotlivé soubory přiřazením názvu **klíče** , výběrem `file` nebo. `text`
1. Pak vyberte jednotlivé soubory pomocí tlačítka **zvolit soubor** .

   [![Příklad klienta pro vystavení](media/how-to-configure-postman/form-body.png)](media/how-to-configure-postman/form-body.png#lightbox)

   >[!NOTE]
   > * Klient po straně klienta nevyžaduje, aby bloky částí v částech měly ručně přiřazený **typ obsahu** nebo **Content-Disposition**.
   > * Pro každou část není nutné zadávat tato záhlaví.
   > * Pro celý požadavek `multipart/mixed` musíte vybrat nebo jiný vhodný **typ obsahu** .

1. Nakonec vyberte **Odeslat** a odešlete požadavek HTTP POST s více částmi.

## <a name="next-steps"></a>Další kroky

- Další informace o rozhraních API pro správu digitálních vláken a způsobu jejich použití najdete v článku [Jak používat rozhraní API pro správu digitálních vláken Azure](how-to-navigate-apis.md).

- K [Přidání objektů blob do entit Azure Digital](./how-to-add-blobs.md)requests použijte požadavky na více částí.

- Pokud se chcete dozvědět o ověřování pomocí rozhraní API pro správu, přečtěte si téma [ověřování pomocí rozhraní API](./security-authenticating-apis.md).
