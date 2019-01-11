---
title: Postup konfigurace nástroje Postman pro digitální dvojče Azure | Dokumentace Microsoftu
description: Postup konfigurace nástroje Postman pro digitální dvojče Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: adgera
ms.openlocfilehash: 705d96225c699c6e9824dadbd7fa1272ba5ddb2d
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54213276"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Postup konfigurace nástroje Postman pro digitální dvojče Azure

Tento článek popisuje, jak nakonfigurovat klienta Postman REST k interakci s a testování Azure digitální dvojče rozhraní API pro správu. Konkrétně popisuje:

* jak nakonfigurovat aplikaci služby Azure Active Directory pro použití tok implicitní grant OAuth 2.0.
* jak nakonfigurovat klienta Postman REST aby token vliv požadavků HTTP pro vaše rozhraní API pro správu.

## <a name="postman-summary"></a>Souhrn postman

Začít používat Azure digitální dvojče pomocí klientský nástroj REST [Postman](https://www.getpostman.com/) Příprava místní testovací prostředí. Klient nástroje Postman pomáhá rychle vytvářet složité požadavky HTTP. Stáhnout tak, že přejdete v desktopové verzi klienta Postman [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) REST testuje nástroj, který vyhledá klíčových funkcích požadavek HTTP do užitečné ploše a na základě modul plug-in grafického uživatelského rozhraní. Prostřednictvím klienta Postman vývojáři řešení můžete zadat typ požadavku HTTP (*příspěvek*, *získat*, *aktualizace*, *oprava*a  *Odstranit*), koncový bod rozhraní API pro volání a použití protokolu SSL. Postman podporuje taky přidávání hlaviček požadavků HTTP, parametry, data formuláře a těla.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Konfigurace služby Azure Active Directory použít tok implicitní grant OAuth 2.0

Konfigurace aplikace Azure Active Directory pro používání služby flow implicitní grant OAuth 2.0.

1. Postupujte podle kroků v [v tomto rychlém startu](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) k vytvoření aplikace Azure AD typu Native. Nebo můžete znovu použít existující registraci nativní aplikace.

1. V části **požadovaná oprávnění**vyberte **přidat** a zadejte **digitální dvojče Azure** pod **přístup přes rozhraní API přidat**. Pokud se vám toto rozhraní API nepodaří najít, vyhledejte místo toho **Azure Smart Spaces**. Vyberte **udělit oprávnění > delegovaná oprávnění** a **provádí**.

    ![Registrace aplikace Azure Active Directory přidat rozhraní api](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

1. Klikněte na tlačítko **Manifest** otevřete manifest aplikace pro vaši aplikaci. Nastavte *oauth2AllowImplicitFlow* k `true`.

      ![Azure Active Directory implicitní tok][1]

1. Konfigurace **adresa URL pro odpověď** k `https://www.getpostman.com/oauth2/callback`.

      ![Adresa URL odpovědi Azure Active Directory][2]

1. Zkopírujte a udržovat **ID aplikace** aplikace Azure Active Directory. Používá se pod.

### <a name="configure-the-postman-client"></a>Konfigurace klienta nástroje Postman

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

1. Klient by teď měl vypadat podobně jako:

   ![Příklad klientského nástroje postman][3]

1. Vyberte **požádat o Token**.

    >[!NOTE]
    >Pokud se zobrazí chybová zpráva "Nebylo možné dokončit OAuth 2", zkuste následující:
    > * Nástroj Postman, zavřete a znovu ho otevřete a akci opakujte.
  
1. Přejděte dolů a vyberte možnost **použijte Token**.

## <a name="next-steps"></a>Další postup

- Další informace o rozhraní API pro správu digitálních Dvojčata a způsob jejich použití, přečtěte si [způsob použití rozhraní API pro správu Azure digitální dvojče](how-to-navigate-apis.md).

- Další informace o ověřování pomocí rozhraní API pro správu, přečtěte si [ověřování pomocí rozhraní API](./security-authenticating-apis.md). 



<!-- Images -->
[1]: media/how-to-configure-postman/implicit-flow.png
[2]: media/how-to-configure-postman/reply-url.png
[3]: media/how-to-configure-postman/postman-oauth-token.png
