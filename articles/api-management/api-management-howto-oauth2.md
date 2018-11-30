---
title: Autorizace vývojářských účtů pomocí OAuth 2.0 ve službě Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak autorizovat uživatele, kteří používají OAuth 2.0 ve službě API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: c0e502c1627ebe9d63ca43cb46b982bb0639f3d7
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444718"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Autorizace vývojářských účtů pomocí OAuth 2.0 ve službě Azure API Management

Mnoho rozhraní API podporují [OAuth 2.0](http://oauth.net/2/) k zabezpečení rozhraní API a zajistěte, aby pouze oprávnění uživatelé mají přístup, a bude pouze přístup k prostředkům, ke kterým máte nárok. Aby bylo možné používat interaktivní konzole pro vývojáře Azure API Management pomocí těchto rozhraní API, služby umožňuje nakonfigurovat instanci služby pro práci s vaší OAuth 2.0 povolené rozhraní API.

## <a name="prerequisites"> </a>Požadované součásti

Tato příručka ukazuje, jak nakonfigurovat instanci služby API Management používat autorizace OAuth 2.0 pro účty pro vývojáře, ale nezobrazuje postup konfigurace poskytovatele rozhraní OAuth 2.0. Konfigurace pro každého zprostředkovatele OAuth 2.0 se liší, i když kroky jsou podobné a požadované části informace použité při konfiguraci OAuth 2.0 ve vaší instanci služby API Management jsou stejné. Toto téma ukazuje příklad použití Azure Active Directory jako zprostředkovatele OAuth 2.0.

> [!NOTE]
> Další informace o konfiguraci OAuth 2.0 pomocí Azure Active Directory, najdete v článku [WebApp-GraphAPI-DotNet] [ WebApp-GraphAPI-DotNet] vzorku.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>Konfigurace serveru autorizace OAuth 2.0 ve službě API Management

> [!NOTE]
> Pokud jste instanci služby API Management ještě nevytvořili, přečtěte si téma [vytvoření instance služby API Management][Create an API Management service instance].

1. Klikněte na kartu OAuth 2.0 v nabídce na levé straně a klikněte na **+ přidat**.

    ![Nabídka OAuth 2.0](./media/api-management-howto-oauth2/oauth-01.png)

2. Zadejte název a volitelný popis v **název** a **popis** pole.

    > [!NOTE]
    > Tato pole se používají k identifikaci serveru ověřování OAuth 2.0 v rámci aktuální instance služby API Management a jejich hodnoty nepochází ze serveru OAuth 2.0.

3. Zadejte **adresa URL stránky pro registraci klienta**. Tato stránka je, kde uživatelé můžou vytvářet a spravovat své účty a se liší v závislosti na zprostředkovateli OAuth 2.0 používat. **Adresa URL stránky pro registraci klienta** odkazuje na stránku, která uživatelům umožňuje vytvořit a nakonfigurovat své účty služby pro zprostředkovatele OAuth 2.0, které podporují správu uživatelů pro účty. Některé organizace nenakonfigurujete nebo tuto funkci použít i v případě, že ho zprostředkovatel OAuth 2.0 podporuje. Pokud zprostředkovateli OAuth 2.0 nemá správu uživatelů pro účty konfigurované, zadejte URL zástupný symbol tady například adresu URL vaší společnosti nebo adresy URL jako `https://placeholder.contoso.com`.

    ![Nový server OAuth 2.0](./media/api-management-howto-oauth2/oauth-02.png)

4. Obsahuje následující části formuláře **typy udělení autorizačního kódu**, **adresu URL koncového bodu autorizace**, a **metoda žádosti o autorizaci** nastavení.

    Zadejte **typy udělení autorizačního kódu** kontrolou požadované typy. **Autorizační kód** zadaný ve výchozím nastavení.

    Zadejte **adresu URL koncového bodu autorizace**. Pro Azure Active Directory, tato adresa URL bude vypadat podobně jako následující adresu URL, kde `<client_id>` nahradí za id klienta, který identifikuje vaši aplikaci na server OAuth 2.0.

    `https://login.microsoftonline.com/<client_id>/oauth2/authorize`

    **Metoda žádosti o autorizaci** Určuje, jak je odeslána žádost o autorizaci na server OAuth 2.0. Ve výchozím nastavení **získat** zaškrtnuto.

5. Potom **adresu URL koncového bodu Token**, **metody ověřování klientů**, **metoda posílání přístupového tokenu** a **výchozí obor** musí být zadat.

    ![Nový server OAuth 2.0](./media/api-management-howto-oauth2/oauth-03.png)

    Pro server Azure Active Directory OAuth 2.0 **adresu URL koncového bodu Token** bude mít následující formát, kde `<TenantID>` má formát `yourapp.onmicrosoft.com`.

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    Výchozí nastavení pro **metody ověřování klientů** je **základní**, a **metoda posílání přístupového tokenu** je **autorizační hlavičky**. Tyto hodnoty jsou nakonfigurované v této části formuláře, spolu s **výchozí obor**.

6. **Přihlašovací údaje pro klienta** oddíl obsahuje **ID klienta** a **tajný kód klienta**, které jsou získány během procesu vytváření a konfigurace serveru OAuth 2.0 . Jednou **ID klienta** a **tajný kód klienta** jsou určené **redirect_uri** pro **autorizační kód** je generován. Pomocí tohoto identifikátoru URI se používá ke konfiguraci příslušnou odpovědní adresu URL v konfiguraci serveru OAuth 2.0.

    ![Nový server OAuth 2.0](./media/api-management-howto-oauth2/oauth-04.png)

    Pokud **typy udělení autorizačního kódu** je nastavena na **heslo vlastníka prostředku**, **přihlašovacího hesla vlastníka prostředku** oddíl se používá k určení tyto přihlašovací údaje; jinak můžete jej můžete nechat prázdné.

    Po dokončení formuláře klikněte na tlačítko **vytvořit** uložte konfiguraci autorizační server API Management OAuth 2.0. Po uložení konfigurace serveru můžete nakonfigurovat rozhraní API pro použití této konfigurace, jak je znázorněno v následující části.

## <a name="step2"> </a>Konfigurace rozhraní API používat autorizaci uživatelů OAuth 2.0

1. Klikněte na tlačítko **rozhraní API** z **API Management** nabídky na levé straně.

    ![Rozhraní API OAuth 2.0](./media/api-management-howto-oauth2/oauth-05.png)

2. Klikněte na název požadovaného rozhraní API a klikněte na tlačítko **nastavení**. Přejděte **zabezpečení** části a potom zaškrtněte políčko u **OAuth 2.0**.

    ![Nastavení OAuth 2.0](./media/api-management-howto-oauth2/oauth-06.png)

3. Vyberte požadovaný **autorizační server** z rozevíracího seznamu a klikněte na **Uložit**.

    ![Nastavení OAuth 2.0](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="step3"> </a>Testování autorizaci uživatelů OAuth 2.0 na portálu pro vývojáře

Jakmile jste nakonfigurovali server autorizace OAuth 2.0 a nakonfigurovat server používat vaše rozhraní API, takže ji můžete otestovat tak, že přejdete na portál pro vývojáře a volání rozhraní API.  Klikněte na tlačítko **portál pro vývojáře** v horní nabídce z vaší instance Azure API Management **přehled** stránky.

![Portál pro vývojáře][api-management-developer-portal-menu]

Klikněte na tlačítko **rozhraní API** v horní nabídce a vyberte **rozhraní Echo API**.

![Echo API][api-management-apis-echo-api]

> [!NOTE]
> Pokud máte nakonfigurované jenom jedno rozhraní API nebo váš účet vidí jenom jedno, můžete kliknutím na rozhraní API přejít přímo na operace tohoto rozhraní.

Vyberte **GET Resource** operace, klikněte na tlačítko **otevřete konzoly**a pak vyberte **autorizační kód** z rozevíracího seznamu.

![Otevření konzoly][api-management-open-console]

Když **autorizační kód** je zaškrtnuto, automaticky otevírané okno se zobrazí ve formuláři přihlášení zprostředkovatele OAuth 2.0. V tomto příkladu přihlašovací formulář poskytuje Azure Active Directory.

> [!NOTE]
> Pokud máte automaticky otevíraná okna zakázané zobrazí výzva k povolení v prohlížeči. Povolíte-li je, vyberte **autorizační kód** znovu a zobrazí se přihlašovací formulář.

![Přihlášení][api-management-oauth2-signin]

Až se zaregistrujete, **hlavičky požadavku** se naplní `Authorization : Bearer` hlavičku, která ověří požadavek.

![Tokenu hlavičky žádosti][api-management-request-header-token]

V tomto okamžiku můžete nakonfigurovat požadované hodnoty pro zbývající parametry a odešlete žádost.

## <a name="next-steps"></a>Další postup

Další informace o použití OAuth 2.0 a API Management, najdete v následující video a doprovodných [článku](api-management-howto-protect-backend-with-aad.md).

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
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

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

