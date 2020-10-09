---
title: Kurz webové aplikace – instalace klientské aplikace
description: Tento kurz vás provede jednotlivými kroky registrace veřejné aplikace pro připravujení nasazení webové aplikace.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 483ca63abbabaabd3b25446c9267a1b0540f60dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87852969"
---
# <a name="client-application-registration"></a>Registrace klientské aplikace
V předchozím kurzu jste nasadili a nastavili rozhraní API Azure pro FHIR. Teď, když máte k dispozici rozhraní API Azure pro FHIR, zaregistrujeme veřejnou klientskou aplikaci. Můžete si přečíst kompletní [registraci veřejného klienta aplikace](register-public-azure-ad-client-app.md) průvodce, kde najdete další podrobnosti nebo řešení potíží, ale zjistili jsme hlavní kroky tohoto kurzu.

1. Přejít na Azure Active Directory
1. Vybrat **registraci aplikace**  -->  **Nová registrace**
1. Pojmenujte aplikaci a nastavte identifikátor URI přesměrování na https://www.getpostman.com/oauth2/callback

   :::image type="content" source="media/tutorial-web-app/register-public-app.png" alt-text="Snímek obrazovky s podoknem zaregistrovat aplikaci a Příkladem názvu aplikace a adresy URL pro přesměrování.":::

## <a name="client-application-settings"></a>Nastavení klientské aplikace

Po registraci klientské aplikace zkopírujte na stránce Přehled ID aplikace (klienta) a ID tenanta. Tyto dvě hodnoty budete potřebovat později při přístupu k klientovi.

:::image type="content" source="media/tutorial-web-app/client-id-tenant-id.png" alt-text="Snímek obrazovky s podoknem zaregistrovat aplikaci a Příkladem názvu aplikace a adresy URL pro přesměrování.":::

### <a name="connect-with-web-app"></a>Připojení k webové aplikaci

Pokud jste [napsali webovou aplikaci](tutorial-web-app-write-web-app.md) pro připojení k Azure API pro FHIR, musíte také nastavit správné možnosti ověřování. 

1. V nabídce vlevo v části **Spravovat**vyberte **ověřování**. 

1. Chcete-li přidat novou konfiguraci platformy, vyberte možnost **Web**.

1. Při vytváření webové aplikace ve čtvrté části tohoto kurzu nastavte identifikátor URI přesměrování v přípravě na. Uděláte to tak, `https://\<WEB-APP-NAME>.azurewebsites.net` že do seznamu identifikátorů URI pro přesměrování přidáte. Pokud při [psaní webové aplikace](tutorial-web-app-write-web-app.md)zvolíte jiný název, budete se muset vrátit a aktualizovat.

1. Zaškrtněte políčka **přístupového tokenu** a **tokenu ID** .

   :::image type="content" source="media/tutorial-web-app/web-app-authentication.png" alt-text="Snímek obrazovky s podoknem zaregistrovat aplikaci a Příkladem názvu aplikace a adresy URL pro přesměrování.":::

## <a name="add-api-permissions"></a>Přidání oprávnění rozhraní API

Teď, když máte nastavené správné ověření, nastavte oprávnění rozhraní API:

1. Vyberte **oprávnění rozhraní API** a klikněte na **Přidat oprávnění**.
1. V části **rozhraní API, které používá moje organizace**, vyhledejte rozhraní API pro zdravotní péče Azure.
1. Vyberte **user_impersonation** a klikněte na **Přidat oprávnění**.

:::image type="content" source="media/tutorial-web-app/api-permissions.png" alt-text="Snímek obrazovky s podoknem zaregistrovat aplikaci a Příkladem názvu aplikace a adresy URL pro přesměrování.":::

## <a name="next-steps"></a>Další kroky
Nyní máte veřejnou klientskou aplikaci. V dalším kurzu provedeme testování a získáme přístup k této aplikaci prostřednictvím post.

>[!div class="nextstepaction"]
>[Testovat klientskou aplikaci v nástroji post](tutorial-web-app-test-postman.md)
