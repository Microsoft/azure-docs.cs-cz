---
title: Jak nakonfigurovat Pošťáka – Azure Digital Twins | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat a použít Postman k testování rozhraní API Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: ffcfb4f6ec5f6c654d0b243af85034ab575e0d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297156"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Jak nakonfigurovat Postman pro Azure Digital Twins

Tento článek popisuje, jak nakonfigurovat klienta Postman REST pro interakci s a testování rozhraní API pro správu digitálních dvojčat Azure. Konkrétně popisuje:

* Jak nakonfigurovat aplikaci Azure Active Directory pro použití implicitního grantového toku OAuth 2.0.
* Jak použít klienta Postman REST k výrobě požadavků HTTP s tokeny pro vaše nastavení API pro správu.
* Jak používat Postman k vícedílné požadavky POST na vaše nastavení API pro správu.

## <a name="postman-summary"></a>Pošťák shrnutí

Můžete začít s Azure Digital Twins pomocí klientského nástroje REST, jako je [Postman,](https://www.getpostman.com/) k přípravě místního testovacího prostředí. Pošťákův klient pomáhá rychle vytvářet složité požadavky HTTP. Stáhněte si desktopovou verzi klienta Postman tak, že přejdete na [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Pošťák](https://www.getpostman.com/) je testovací nástroj REST, který vyhledá klíčové funkce požadavků HTTP do užitečného grafického uživatelského rozhraní založeného na stolních počítačích a pluginech.

Prostřednictvím klienta Postman mohou vývojáři řešení určit druh požadavku HTTP (*POST*, *GET*, *UPDATE*, *PATCH*a *DELETE*), koncový bod rozhraní API pro volání a použití protokolu TLS. Pošťák také podporuje přidávání hlavičky požadavků HTTP, parametrů, dat formuláře a těl.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Konfigurace služby Azure Active Directory tak, aby používala implicitní tok grantů OAuth 2.0

1. Podle pokynů na [úvodním panelu](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) vytvořte a nakonfigurujte aplikaci Služby Azure Active Directory. Případně můžete znovu použít existující registraci aplikace.

    [![Konfigurace nového identifikátoru URI přesměrování Pozora](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. Nyní přidejte identifikátor URI `https://www.getpostman.com/oauth2/callback` **přesměrování** do .

1. Zaškrtněte políčko **Implicitní udělení** > **tokenů přístupu,** aby bylo možné použít implicitní grantový tok OAuth 2.0. Vyberte **konfigurovat**a potom **uložit**.

1. Zkopírujte **ID klienta** aplikace Azure Active Directory.

## <a name="obtain-an-oauth-20-token"></a>Získání tokenu OAuth 2.0

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Nastavte a nakonfigurujte Postman a získejte token Služby Azure Active Directory. Potom vytvořte ověřený požadavek HTTP pro Azure Digital Twins pomocí získaného tokenu:

1. Ověřte **správnost adresy URL autorizace.** To by mělo mít formát:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Name (Název)  | Nahradit hodnotou | Příklad |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Název vašeho klienta nebo organizace. Místo alfanumerického **ID klienta** registrace aplikace Azure Active Directory použijte název vhodný pro člověka. | `microsoft` |

1. Přejděte na [www.getpostman.com](https://www.getpostman.com/) a stáhněte si aplikaci.

1. Chceme podat žádost GET. Vyberte kartu **Autorizace,** vyberte OAuth 2.0 a pak vyberte **Získat nový přístupový token**.

    | Pole  | Hodnota |
    |---------|---------|
    | Typ udělení | `Implicit` |
    | Adresa URL zpětného volání | `https://www.getpostman.com/oauth2/callback` |
    | Ověřovací adresa URL | Použití **adresy URL autorizace** z **kroku 1** |
    | ID klienta | Použití **ID aplikace** pro aplikaci Azure Active Directory, která byla vytvořena nebo znovu použita z předchozí části |
    | Rozsah | Ponechte prázdné |
    | Stav | Ponechte prázdné |
    | Ověření klienta | `Send as Basic Auth header` |

1. Klient by se nyní měl zobrazit jako:

    [![Příklad tokenu klienta Pošťáka](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Vyberte **Request Token** (Vyžádat token).
  
1. Posuňte se dolů a vyberte **Použít token**.

## <a name="make-a-multipart-post-request"></a>Vytvoření vícedílné žádosti POST

Po dokončení předchozích kroků nakonfigurujte Postman tak, aby ověřoval vícedílný požadavek POST protokolu HTTP:

1. Na kartě Záhlaví přidejte klíč **obsahu** obsahu `multipart/mixed` **hlavičky** požadavku HTTP s hodnotou .

   [![Určení typu obsahu vícedílné/smíšené](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. Serialize netextových dat do souborů. Data JSON budou uložena jako soubor JSON.
1. Na **Body** kartě Tělo `form-data`vyberte . 
1. Přidejte každý soubor **key** přiřazením názvu `File`klíče a výběrem .
1. Potom vyberte každý soubor pomocí tlačítka **Zvolit soubor.**

   [![Příklad těla klienta Pošťáka](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * Pošťákův klient nevyžaduje, aby vícedílné bloky měly ručně přiřazenou **položku Content-Type** nebo **Content-Disposition**.
   > * Není nutné zadat tyto hlavičky pro každou část.
   > * Pro celý `multipart/mixed` požadavek je nutné vybrat nebo jiný vhodný **typ obsahu.**

1. Nakonec vyberte **Odeslat,** chcete-li odeslat vícedílný požadavek HTTP POST. Stavový kód `200` `201` nebo označuje úspěšný požadavek. Příslušná zpráva odpovědi se zobrazí v rozhraní klienta.

1. Ověřte data požadavku HTTP POST voláním koncového bodu rozhraní API: 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>Další kroky

- Další informace o virtuálních i min. a správě digitálních dvojčat a o tom, jak je používat, najdete v tématu [Jak používat řešení API pro správu digitálních dvojčat Azure](how-to-navigate-apis.md).

- Pomocí vícedílných požadavků [můžete přidávat objekty BLOB do entit Azure Digital Twins](./how-to-add-blobs.md).

- Informace o ověřování pomocí api pro správu najdete v části [Ověření pomocí řešení api](./security-authenticating-apis.md).
