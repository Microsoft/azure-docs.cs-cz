---
title: Autorizace vývojářských účtů pomocí OAuth 2.0 ve správě rozhraní API
titleSuffix: Azure API Management
description: Zjistěte, jak autorizovat uživatele pomocí OAuth 2.0 ve správě rozhraní API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 90c890925378c30ce5688d2713990b4b2cdd20c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430679"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Jak autorizovat vývojářské účty pomocí OAuth 2.0 ve správě Rozhraní API Azure

Mnoho rozhraní API podporuje [OAuth 2.0](https://oauth.net/2/) k zabezpečení rozhraní API a ujistěte se, že pouze platní uživatelé mají přístup a mají přístup pouze k prostředkům, na které mají nárok. Aby bylo možné používat interaktivní konzolu pro vývojáře Azure API Management s těmito rozhraními API, služba umožňuje nakonfigurovat instanci služby tak, aby fungovala s rozhraním API s povolenou oAuth 2.0.

> [!IMPORTANT]
> Autorizace OAuth 2.0 ještě není k dispozici v interaktivní konzole nového portálu pro vývojáře.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Požadavky

Tato příručka ukazuje, jak nakonfigurovat instanci služby API Management tak, aby používala autorizaci OAuth 2.0 pro vývojářské účty, ale neukazuje, jak nakonfigurovat zprostředkovatele OAuth 2.0. Konfigurace pro každého zprostředkovatele OAuth 2.0 se liší, i když kroky jsou podobné a požadované informace použité při konfiguraci OAuth 2.0 v instanci služby api management jsou stejné. Toto téma ukazuje příklady pomocí služby Azure Active Directory jako zprostředkovatele OAuth 2.0.

> [!NOTE]
> Další informace o konfiguraci OAuth 2.0 pomocí služby Azure Active Directory najdete v tématu [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet] ukázka.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-an-oauth-20-authorization-server-in-api-management"></a><a name="step1"> </a>Konfigurace autorizačního serveru OAuth 2.0 ve správě rozhraní API

> [!NOTE]
> Pokud jste ještě nevytvořili instanci služby Api Management, přečtěte si informace [o vytvoření instance služby Správa rozhraní API][Create an API Management service instance].

1. Klikněte na kartu OAuth 2.0 v nabídce vlevo a klikněte na **+Přidat**.

    ![Nabídka OAuth 2.0](./media/api-management-howto-oauth2/oauth-01.png)

2. Do polí **Název** a **popis** zadejte název a volitelný popis.

    > [!NOTE]
    > Tato pole slouží k identifikaci autorizačního serveru OAuth 2.0 v rámci aktuální instance služby Api Management a jejich hodnoty nepocházejí ze serveru OAuth 2.0.

3. Zadejte **adresu URL registrační stránky klienta**. Tato stránka je místo, kde uživatelé mohou vytvářet a spravovat své účty a liší se v závislosti na použitém zprostředkovateli OAuth 2.0. **Adresa URL registrační stránky klienta** odkazuje na stránku, kterou mohou uživatelé použít k vytvoření a konfiguraci vlastních účtů pro poskytovatele OAuth 2.0, kteří podporují správu uživatelských účtů. Některé organizace tuto funkci nekonfigurují ani nepoužívají, i když ji podporuje zprostředkovatel OAuth 2.0. Pokud váš poskytovatel OAuth 2.0 nemá nakonfigurovánou správu uživatelů účtů, zadejte zde zástupnou adresu `https://placeholder.contoso.com`URL, například adresu URL vaší společnosti nebo adresu URL, například .

    ![OAuth 2.0 nový server](./media/api-management-howto-oauth2/oauth-02.png)

4. Další část formuláře obsahuje **typy udělení autorizace**, **adresu URL koncového bodu autorizace**a nastavení **metody žádosti o autorizaci.**

    Zadejte **typy udělení autorizace** kontrolou požadovaných typů. Ve výchozím nastavení je určen **autorizační kód.**

    Zadejte **adresu URL koncového bodu autorizace**. Pro Azure Active Directory bude tato adresa URL `<tenant_id>` podobná následující adrese URL, kde se nahradí ID vašeho klienta Azure AD.

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    **Metoda žádosti o autorizaci** určuje způsob odeslání žádosti o autorizaci na server OAuth 2.0. Ve výchozím nastavení je vybrána možnost **GET.**

5. Potom je třeba zadat **adresu URL koncového bodu tokenu**, **metody ověřování klienta**, **metodu odesílání tokenů aplikace Access** a výchozí **obor.**

    ![OAuth 2.0 nový server](./media/api-management-howto-oauth2/oauth-03.png)

    Pro server Azure Active Directory OAuth 2.0 bude mít **adresa URL koncového bodu tokenu** následující formát, kde `<TenantID>` má formát . `yourapp.onmicrosoft.com`

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    Výchozí nastavení **metod ověřování klienta** je **Základní**a **metoda odesílání tokenů aplikace Access** je **Hlavička autorizace**. Tyto hodnoty jsou konfigurovány v této části formuláře spolu s **výchozím oborem**.

6. Část **Pověření klienta** obsahuje **ID klienta** a **tajný klíč klienta**, které jsou získány během procesu vytváření a konfigurace serveru OAuth 2.0. Jakmile je **zadáno ID klienta** a **tajný klíč klienta,** vygeneruje **se redirect_uri** **pro autorizační kód.** Tento identifikátor URI slouží ke konfiguraci adresy URL odpovědi v konfiguraci serveru OAuth 2.0.

    ![OAuth 2.0 nový server](./media/api-management-howto-oauth2/oauth-04.png)

    Pokud jsou **typy udělení autorizace** nastaveny na **heslo vlastníka prostředku**, použije se k určení těchto pověření oddíl pověření **vlastníka prostředku.** v opačném případě jej můžete nechat prázdné.

    Po dokončení formuláře klikněte na **Vytvořit** a uložte konfiguraci autorizačního serveru API Management OAuth 2.0. Po uložení konfigurace serveru můžete nakonfigurovat rozhraní API tak, aby tuto konfiguraci používala, jak je znázorněno v další části.

## <a name="configure-an-api-to-use-oauth-20-user-authorization"></a><a name="step2"> </a>Konfigurace rozhraní API pro použití autorizace uživatele OAuth 2.0

1. Klikněte na **rozhraní API** z nabídky **Správa rozhraní API** vlevo.

    ![OAuth 2.0 API](./media/api-management-howto-oauth2/oauth-05.png)

2. Klepněte na název požadovaného rozhraní API a klepněte na tlačítko **Nastavení**. Přejděte do části **Zabezpečení** a zaškrtněte políčko **OAuth 2.0**.

    ![Nastavení OAuth 2.0](./media/api-management-howto-oauth2/oauth-06.png)

3. V rozevíracím seznamu vyberte požadovaný **autorizační server** a klepněte na tlačítko **Uložit**.

    ![Nastavení OAuth 2.0](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="legacy-developer-portal---test-the-oauth-20-user-authorization"></a><a name="step3"> </a>Starší vývojářský portál – otestujte autorizaci uživatele OAuth 2.0

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Jakmile nakonfigurujete autorizační server OAuth 2.0 a nakonfigurujete rozhraní API tak, aby tento server používalo, můžete jej otestovat na portálu pro vývojáře a voláním rozhraní API. Klikněte na **portál pro vývojáře (starší verze)** v horní nabídce na stránce **Přehled** instance azure api správy.

V horní nabídce klepněte na **rozhraní API** a vyberte rozhraní **Echo API**.

![Echo API][api-management-apis-echo-api]

> [!NOTE]
> Pokud máte nakonfigurované jenom jedno rozhraní API nebo váš účet vidí jenom jedno, můžete kliknutím na rozhraní API přejít přímo na operace tohoto rozhraní.

Vyberte operaci **GET Resource,** klepněte na **příkaz Otevřít konzolu**a v rozevíracím seznamku vyberte **autorizační kód.**

![Otevření konzoly][api-management-open-console]

Pokud je vybrán **autorizační kód,** zobrazí se automaticky otevírané okno s přihlašovací formou zprostředkovatele OAuth 2.0. V tomto příkladu přihlašovací formulář poskytuje služba Azure Active Directory.

> [!NOTE]
> Pokud máte vypnutá automaticky otevíraná okna, budete vyzváni k jejich povolení prohlížečem. Po jejich povolení vyberte **autorizační kód** znovu a zobrazí se přihlašovací formulář.

![Přihlášení][api-management-oauth2-signin]

Po přihlášení jsou **hlavičky požadavku naplněny** hlavičkou, `Authorization : Bearer` která žádost autorizuje.

![Požadavek na token hlavičky][api-management-request-header-token]

V tomto okamžiku můžete nakonfigurovat požadované hodnoty pro zbývající parametry a odeslat požadavek.

## <a name="next-steps"></a>Další kroky

Další informace o použití oauth 2.0 a api management, naleznete v následujícím videu a doprovodný [článek](api-management-howto-protect-backend-with-aad.md).

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

