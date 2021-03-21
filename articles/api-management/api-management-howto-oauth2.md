---
title: Autorizovat vývojářské účty pomocí OAuth 2,0 v API Management
titleSuffix: Azure API Management
description: Naučte se, jak autorizovat uživatele pomocí OAuth 2,0 v API Management. OAuth 2,0 zabezpečuje rozhraní API, aby uživatelé mohli přistupovat jenom k prostředkům, ke kterým mají oprávnění.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/14/2020
ms.author: apimpm
ms.openlocfilehash: fae4e349d46425c0c2b2b923d6a61e2e588708c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93077247"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Jak autorizovat vývojářské účty pomocí OAuth 2,0 v Azure API Management

Mnoho rozhraní API podporuje [OAuth 2,0](https://oauth.net/2/) k zabezpečení rozhraní API a zajišťují, že přístup k nim mají jenom platní uživatelé a mají přístup jenom k prostředkům, ke kterým mají oprávnění. Aby bylo možné používat interaktivní konzolu pro vývojáře Azure API Management s těmito rozhraními API, služba vám umožní nakonfigurovat instanci služby tak, aby fungovala s rozhraním API s povoleným protokolem OAuth 2,0.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Předpoklady

V této příručce se dozvíte, jak nakonfigurovat instanci služby API Management, aby používala autorizaci OAuth 2,0 pro vývojářské účty, ale neukazuje, jak nakonfigurovat poskytovatele OAuth 2,0. Konfigurace pro každého poskytovatele OAuth 2,0 se liší, ale postup je podobný a požadované informace, které se používají při konfiguraci OAuth 2,0 ve vaší instanci služby API Management, jsou stejné. V tomto tématu se dozvíte o příkladech použití Azure Active Directory jako poskytovatele OAuth 2,0.

> [!NOTE]
> Další informace o konfiguraci OAuth 2,0 pomocí Azure Active Directory najdete v ukázce [WebApp-GraphAPI-dotnet][WebApp-GraphAPI-DotNet] .

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-an-oauth-20-authorization-server-in-api-management"></a><a name="step1"> </a>Konfigurace autorizačního serveru OAuth 2,0 v API Management

> [!NOTE]
> Pokud jste ještě nevytvořili instanci služby API Management, přečtěte si téma [vytvoření instance služby API Management][Create an API Management service instance].

1. V nabídce na levé straně klikněte na kartu OAuth 2,0 a klikněte na **+ Přidat**.

    ![Nabídka OAuth 2,0](./media/api-management-howto-oauth2/oauth-01.png)

2. Do polí **název** a **Popis** zadejte název a volitelný popis.

    > [!NOTE]
    > Tato pole se používají k identifikaci autorizačního serveru OAuth 2,0 v rámci aktuální instance služby API Management a jejich hodnoty nepocházejí ze serveru OAuth 2,0.

3. Zadejte **adresu URL stránky pro registraci klienta**. Na této stránce můžou uživatelé vytvářet a spravovat svoje účty a liší se v závislosti na použitém poskytovateli OAuth 2,0. **Adresa URL stránky pro registraci klientů** odkazuje na stránku, kterou mohou uživatelé použít k vytvoření a konfiguraci vlastních účtů pro poskytovatele OAuth 2,0, kteří podporují správu uživatelů účtů – například `https://contoso.com/login` . Některé organizace tuto funkci nekonfigurují ani nepoužívají, i když je poskytovatel OAuth 2,0 podporuje. Pokud váš poskytovatel OAuth 2,0 nemá nakonfigurovanou správu uživatelských účtů, zadejte sem adresu URL zástupného symbolu, například adresu URL vaší společnosti, nebo adresu URL, jako je například `https://placeholder.contoso.com` .

    ![Nový server OAuth 2,0](./media/api-management-howto-oauth2/oauth-02.png)

4. V další části formuláře jsou **typy udělení autorizace**, **Adresa URL koncového bodu autorizace** a nastavení **metody autorizační žádosti** .

    Určete **typy udělení autorizace** kontrolou požadovaných typů. Ve výchozím nastavení je zadán **autorizační kód** .

    Zadejte **adresu URL koncového bodu autorizace**. V případě Azure Active Directory bude tato adresa URL podobná následující adrese URL, kde `<tenant_id>` se nahradí ID vašeho tenanta Azure AD.

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    **Metoda autorizační požadavek** určuje, jak se žádost o autorizaci pošle na server OAuth 2,0. Ve výchozím nastavení je vybraná možnost **získat** .

5. Pak je nutné zadat **adresu URL koncového bodu tokenu**, **metody ověřování klienta**, **metodu odeslání přístupového tokenu** a **výchozí obor** .

    ![Snímek obrazovky, na kterém se zobrazuje obrazovka Přidat službu OAuth2](./media/api-management-howto-oauth2/oauth-03.png)

    U Azure Active Directory serveru OAuth 2,0 bude **Adresa URL koncového bodu tokenu** mít následující formát, kde `<TenantID>`  má formát `yourapp.onmicrosoft.com` .

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    Výchozí nastavení pro **metody ověřování klienta** je **Basic** a  **Metoda odesílání přístupového tokenu** je **autorizační hlavičkou**. Tyto hodnoty jsou konfigurovány v této části formuláře společně s **výchozím oborem**.

6. Oddíl **přihlašovací údaje klienta** obsahuje **ID klienta** a **tajný klíč klienta**, které se získávají během procesu vytváření a konfigurace vašeho serveru OAuth 2,0. Jakmile zadáte **ID klienta** a **tajný klíč klienta** , vygeneruje se **redirect_uri** pro **autorizační kód** . Tento identifikátor URI se používá ke konfiguraci adresy URL odpovědi v konfiguraci serveru OAuth 2,0.

    Na novém portálu pro vývojáře má přípona identifikátor URI formu:

    - `/signin-oauth/code/callback/{authServerName}` pro tok udělení autorizačního kódu
    - `/signin-oauth/implicit/callback` pro implicitní tok udělení

    ![Snímek obrazovky s informacemi o tom, kde přidat pověření klienta pro novou službu OAuth2](./media/api-management-howto-oauth2/oauth-04.png)

    Pokud jsou **typy autorizačních** oprávnění nastavené na **heslo vlastníka prostředku**, použije se k zadání těchto přihlašovacích údajů oddíl **hesla vlastníka prostředku** . v opačném případě je můžete nechat prázdné.

    Po dokončení formuláře kliknutím na **vytvořit** uložte API Management konfiguraci autorizačního serveru OAuth 2,0. Po uložení konfigurace serveru můžete nakonfigurovat rozhraní API tak, aby používalo tuto konfiguraci, jak je znázorněno v následující části.

## <a name="configure-an-api-to-use-oauth-20-user-authorization"></a><a name="step2"> </a>Konfigurace rozhraní API pro použití autorizace uživatele OAuth 2,0

1. V nabídce **API Management** na levé straně klikněte na **rozhraní API** .

    ![Rozhraní API OAuth 2,0](./media/api-management-howto-oauth2/oauth-05.png)

2. Klikněte na název požadovaného rozhraní API a klikněte na **Nastavení**. Přejděte do části **zabezpečení** a zaškrtněte políčko **OAuth 2,0**.

    ![Nastavení OAuth 2,0](./media/api-management-howto-oauth2/oauth-06.png)

3. V rozevíracím seznamu vyberte požadovaný **autorizační Server** a klikněte na **Uložit**.

    ![Snímek obrazovky, který zvýrazní vybraný autorizační Server a tlačítko Uložit.](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="legacy-developer-portal---test-the-oauth-20-user-authorization"></a><a name="step3"> </a>Starší verze portálu pro vývojáře – testování autorizace uživatele OAuth 2,0

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Jakmile nakonfigurujete server OAuth 2,0 a nakonfigurujete své rozhraní API tak, aby používalo tento server, můžete ho otestovat tak, že na portálu pro vývojáře kliknete a zavoláte rozhraní API. V horní nabídce stránky s **přehledem** instance Azure API Management klikněte na **portál pro vývojáře (starší verze)** .

V horní nabídce klikněte na **rozhraní API** a vyberte **echo API**.

![Echo API][api-management-apis-echo-api]

> [!NOTE]
> Pokud máte nakonfigurované jenom jedno rozhraní API nebo váš účet vidí jenom jedno, můžete kliknutím na rozhraní API přejít přímo na operace tohoto rozhraní.

Vyberte operaci **získat prostředek** , klikněte na **otevřít konzolu** a potom z rozevíracího seznamu vyberte **autorizační kód** .

![Otevření konzoly][api-management-open-console]

Když je vybraný **autorizační kód** , zobrazí se automaticky otevírané okno s přihlašovacím formulářem zprostředkovatele OAuth 2,0. V tomto příkladu je přihlašovací formulář k dispozici pomocí Azure Active Directory.

> [!NOTE]
> Pokud jste automaticky otevíraná okna zakázali, budete vyzváni, abyste je povolili v prohlížeči. Jakmile je povolíte, znovu vyberte **autorizační kód** a zobrazí se formulář pro přihlášení.

![Přihlásit se][api-management-oauth2-signin]

Jakmile se přihlásíte, **hlavičky žádosti** se naplní `Authorization : Bearer` hlavičkou, která žádost autorizuje.

![Token hlavičky požadavku][api-management-request-header-token]

V tomto okamžiku můžete nakonfigurovat požadované hodnoty pro zbývající parametry a odeslat žádost.

## <a name="next-steps"></a>Další kroky

Další informace o použití OAuth 2,0 a API Management najdete v následujícím videu a doprovodném [článku](api-management-howto-protect-backend-with-aad.md).

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps
