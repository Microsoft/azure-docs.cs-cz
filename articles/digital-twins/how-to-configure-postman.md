---
title: Postup konfigurace nástroje Postman pro digitální dvojče Azure | Dokumentace Microsoftu
description: Postup konfigurace nástroje Postman pro digitální dvojče Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: adgera
ms.openlocfilehash: 49b073952b0923b940204b19680dcc9a1ffa44b5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259270"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Postup konfigurace nástroje Postman pro digitální dvojče Azure

Tento článek popisuje, jak nakonfigurovat klienta Postman REST k interakci s a testování Azure digitální dvojče rozhraní API pro správu. Konkrétně popisuje:

* jak nakonfigurovat aplikaci služby Azure Active Directory pro použití tok implicitní grant OAuth 2.0.
* Jak použít klienta Postman REST aby token vliv požadavků HTTP pro vaše rozhraní API pro správu.
* Jak použít nástroj Postman, aby vícedílné zprávy standardu požadavky POST pro vaše rozhraní API pro správu.

## <a name="postman-summary"></a>Souhrn postman

Začít používat Azure digitální dvojče pomocí klientský nástroj REST [Postman](https://www.getpostman.com/) Příprava místní testovací prostředí. Klient nástroje Postman pomáhá rychle vytvářet složité požadavky HTTP. Stáhnout tak, že přejdete v desktopové verzi klienta Postman [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) REST testuje nástroj, který vyhledá klíčových funkcích požadavek HTTP do užitečné ploše a na základě modul plug-in grafického uživatelského rozhraní. 

Prostřednictvím klienta Postman vývojáři řešení můžete zadat typ požadavku HTTP (*příspěvek*, *získat*, *aktualizace*, *oprava*a  *Odstranit*), koncový bod rozhraní API pro volání a použití protokolu SSL. Postman podporuje taky přidávání hlaviček požadavků HTTP, parametry, data formuláře a těla.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Konfigurace služby Azure Active Directory použít tok implicitní grant OAuth 2.0

Konfigurace aplikace Azure Active Directory pro používání služby flow implicitní grant OAuth 2.0.

1. Postupujte podle kroků v [v tomto rychlém startu](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) k vytvoření aplikace Azure AD typu Native. Nebo můžete znovu použít existující registraci nativní aplikace.

1. V části **požadovaná oprávnění**vyberte **přidat** a zadejte **digitální dvojče Azure** pod **přístup přes rozhraní API přidat**. Pokud se vám toto rozhraní API nepodaří najít, vyhledejte místo toho **Azure Smart Spaces**. Vyberte **udělit oprávnění > delegovaná oprávnění** a **provádí**.

    ![Registrace aplikace Azure Active Directory přidat rozhraní api](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

1. Klikněte na tlačítko **Manifest** otevřete manifest aplikace pro vaši aplikaci. Nastavte *oauth2AllowImplicitFlow* k `true`.

      ![Azure Active Directory implicitní tok][1]

1. Konfigurace **adresa URL pro odpověď** k `https://www.getpostman.com/oauth2/callback`.

      ![Adresa URL odpovědi Azure Active Directory][2]

1. Zkopírujte a udržovat **ID aplikace** aplikace Azure Active Directory. Používá se v následujících kroků.

## <a name="obtain-an-oauth-20-token"></a>Získat token OAuth 2.0

V dalším kroku nastavení a konfigurace nástroje Postman pro získání tokenu Azure Active Directory. Digitální dvojče Azure pomocí tokenu získaného proveďte později, ověřeného požadavku HTTP:

1. Přejděte na [www.getpostman.com](https://www.getpostman.com/) stažení aplikace.
1. Ověřte, že vaše **autorizace URL** je správná. Mělo by to trvat formátu:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Název  | Nahradit hodnotou | Příklad: |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Název tenanta vaší organizace | `microsoft` |

1. Vyberte **autorizace** kartu, vyberte možnost **OAuth 2.0**a pak vyberte **získat nový přístupový Token**.

    | Pole  | Hodnota |
    |---------|---------|
    | Typ udělení | `Implicit` |
    | Adresa URL pro zpětné volání | `https://www.getpostman.com/oauth2/callback` |
    | Ověřovací adresa URL | Použití **autorizace URL** z kroku 2 |
    | ID klienta | Použití **ID aplikace** pro aplikace Azure Active Directory, který byl vytvořen nebo k jinému účelu z předchozí části |
    | Rozsah | Ponechte prázdné |
    | Stav | Ponechte prázdné |
    | Ověřování klientů | `Send as Basic Auth header` |

1. Klient by měl nyní vypadat jako:

   ![Příklad klientského nástroje postman][3]

1. Vyberte **požádat o Token**.

    >[!TIP]
    >Pokud se zobrazí chybová zpráva "Nebylo možné dokončit OAuth 2", zkuste následující:
    > * Nástroj Postman, zavřete a znovu ho otevřete a akci opakujte.
  
1. Přejděte dolů a vyberte možnost **použijte Token**.

<div id="multi"></div>

## <a name="make-a-multipart-post-request"></a>Ujistěte se, požadavek POST s více částmi.

Po dokončení předchozích kroků konfigurace nástroje Postman aby ověřeného požadavku POST vícedílné zprávy standardu HTTP:

1. V části **záhlaví** kartu, přidejte klíčem hlavičku požadavku HTTP **Content-Type** s hodnotou `multipart/mixed`.

   ![Typ obsahu multipart/mixed][4]

1. Serializace dat bez textu do souborů. JSON data by mělo být uloženo jako soubor JSON.
1. V části **tělo** kartu, přidejte každý soubor přiřazením **klíč** název, vyberete `file` nebo `text`.
1. Vyberte každého souboru prostřednictvím **zvolit soubor** tlačítko.

   ![Příklad klientského nástroje postman][5]

   >[!NOTE]
   > * Postman klient nevyžaduje, že ručně přiřazených bloků dat s více částmi **Content-Type** nebo **Content-Disposition**.
   > * Nemusíte určit tyto hlavičky pro každou část.
   > * Musíte vybrat `multipart/mixed` nebo další odpovídající **Content-Type** pro celou žádost.

1. A konečně, klikněte na tlačítko **odeslat** k odeslání žádosti HTTP POST s více částmi.

## <a name="next-steps"></a>Další postup

- Další informace o rozhraní API pro správu digitálních Dvojčata a způsob jejich použití, přečtěte si [způsob použití rozhraní API pro správu Azure digitální dvojče](how-to-navigate-apis.md).

- Použít s více částmi. požadavky na [přidat objekty BLOB Azure digitální dvojče entity](./how-to-add-blobs.md).

- Další informace o ověřování pomocí rozhraní API pro správu, přečtěte si [ověřování pomocí rozhraní API](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-configure-postman/implicit-flow.png
[2]: media/how-to-configure-postman/reply-url.png
[3]: media/how-to-configure-postman/postman-oauth-token.png
[4]: media/how-to-configure-postman/content-type.png
[5]: media/how-to-configure-postman/form-body.png