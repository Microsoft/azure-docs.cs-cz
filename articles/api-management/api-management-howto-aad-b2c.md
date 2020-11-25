---
title: Autorizace vývojářských účtů pomocí Azure Active Directory B2C
titleSuffix: Azure API Management
description: Naučte se, jak autorizovat uživatele pomocí Azure Active Directory B2C v API Management.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 7b586edd7adce8bcea61419005a3ce8cfc814fb3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013534"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Autorizace vývojářských účtů pomocí Azure Active Directory B2C ve službě Azure API Management

## <a name="overview"></a>Přehled

Azure Active Directory B2C je cloudové řešení pro správu identit pro webové a mobilní aplikace, které čelí spotřebiteli. Můžete ji použít ke správě přístupu k portálu pro vývojáře. V této příručce se dozvíte, jak ve službě API Management Service, která se má integrovat s Azure Active Directory B2C, na konfiguraci. Informace o povolení přístupu k portálu pro vývojáře pomocí klasického Azure Active Directory najdete v tématu [ověření vývojářských účtů pomocí Azure Active Directory].

> [!NOTE]
> Chcete-li dokončit kroky v této příručce, je nutné nejprve mít klienta Azure Active Directory B2C, aby bylo možné vytvořit aplikaci v nástroji. Také musíte mít připravená registrace a přihlášení k zásadám. Další informace najdete v tématu [přehled Azure Active Directory B2C].

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autorizace vývojářských účtů pomocí Azure Active Directory B2C

1. Začněte tím, že se přihlásíte k [Azure Portal](https://portal.azure.com) a vyhledáte svou instanci API Management.

   > [!NOTE]
   > Pokud jste ještě nevytvořili instanci služby API Management, přečtěte si téma [vytvoření instance služby API Management][Create an API Management service instance] v [kurzu Začínáme se službou Azure API Management][Get started with Azure API Management].

1. V části **identity**. Klikněte na **+ Přidat** v horní části.

   Na pravé straně se zobrazí podokno **Přidat poskytovatele identity** . Vyberte **Azure Active Directory B2C**.
    
   ![Přidat AAD B2C jako zprostředkovatele identity][api-management-howto-add-b2c-identity-provider]

1. Zkopírujte **adresu URL pro přesměrování**.

   ![Adresa URL pro přesměrování zprostředkovatele identity AAD B2C][api-management-howto-copy-b2c-identity-provider-redirect-url]

1. Na nové kartě získáte přístup k tenantovi Azure Active Directory B2C v Azure Portal a otevřete okno **aplikace** .

   ![Zaregistrovat novou aplikaci 1][api-management-howto-aad-b2c-portal-menu]

1. Kliknutím na tlačítko **Přidat** vytvořte novou Azure Active Directory B2C aplikaci.

   ![Registrace nové aplikace 2][api-management-howto-aad-b2c-add-button]

1. V okně **Nová aplikace** zadejte název aplikace. V části **Webová aplikace/webové rozhraní API** vyberte **Ano** a v části **Povolení implicitního toku** vyberte **Ano** . Pak vložte **adresu URL pro přesměrování** zkopírovanou v kroku 3 do textového pole **Adresa URL odpovědi** .

   ![Registrace nové aplikace 3][api-management-howto-aad-b2c-app-details]

1. Pokud používáte nový portál pro vývojáře (ne starší portál pro vývojáře), zahrňte v deklaracích aplikací **křestní jméno**, **příjmení** a **ID objektu uživatele** .

    ![Deklarace identity aplikace](./media/api-management-howto-aad-b2c/api-management-application-claims.png)

1. Klikněte na tlačítko **Vytvořit**. Když je aplikace vytvořená, zobrazí se v okně **aplikace** . Kliknutím na název aplikace zobrazíte její podrobnosti.

   ![Registrace nové aplikace 4][api-management-howto-aad-b2c-app-created]

1. V okně **vlastnosti** zkopírujte **ID aplikace** do schránky.

   ![ID aplikace 1][api-management-howto-aad-b2c-app-id]

1. Přepněte zpátky do API Management **Přidat zprostředkovatele identity** a vložte ID do textového pole **ID klienta** .
    
1.  Přepněte zpátky na registraci aplikace B2C, klikněte na tlačítko **klíče** a pak klikněte na **vygenerovat klíč**. Kliknutím na **Uložit** uložte konfiguraci a zobrazte **klíč aplikace**. Zkopírujte klíč do schránky.

    ![Klíč aplikace 1][api-management-howto-aad-b2c-app-key]

1.  Přepněte zpátky do API Management **Přidat zprostředkovatele identity** a vložte ho do textového pole **tajný klíč klienta** .
    
1.  V části **přihlášení tenanta** zadejte název domény Azure Active Directory B2C tenanta.

1.  Pole **autorita** umožňuje řídit Azure AD B2C přihlašovací adresu URL, která se má použít. Nastavte hodnotu na **<your_b2c_tenant_name>. b2clogin.com**.

1. Zadejte **zásady registrace** a zásady **přihlášení** ze zásad klienta B2C. Volitelně můžete také zadat **zásady pro úpravu profilu** a **resetování hesel**.

1. Po zadání požadované konfigurace klikněte na **Uložit**.

    Po uložení změn budou moci vývojáři vytvářet nové účty a přihlašovat se k portálu pro vývojáře pomocí Azure Active Directory B2C.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>Portál pro vývojáře – přidání ověřování účtu Azure AD B2C

Na portálu pro vývojáře je možné přihlašovat pomocí AAD B2C pomocí **tlačítka pro přihlášení: widget OAuth** . Pomůcka už je součástí přihlašovací stránky výchozího obsahu portálu pro vývojáře.

I když se nový účet automaticky vytvoří, když se přihlásí nový uživatel pomocí AAD B2C, můžete zvážit přidání stejného widgetu do registrační stránky.

**Formulář pro registraci: widget OAuth** představuje formulář používaný k registraci pomocí protokolu OAuth.

> [!IMPORTANT]
> Aby se změny AAD projevily, je potřeba [znovu publikovat portál](api-management-howto-developer-portal-customize.md#publish) .

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>Starší portál pro vývojáře – jak se zaregistrovat pomocí Azure AD B2C

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Pokud se chcete přihlásit k účtu vývojáře pomocí Azure Active Directory B2C, otevřete nové okno prohlížeče a přejdete na portál pro vývojáře. Klikněte na tlačítko **zaregistrovat** se.

   ![Portál pro vývojáře 1][api-management-howto-aad-b2c-dev-portal]

2. Vyberte, chcete-li se zaregistrovat pomocí **Azure Active Directory B2C**.

   ![Portál pro vývojáře 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Budete přesměrováni na zásadu registrace, kterou jste nakonfigurovali v předchozí části. Pomocí e-mailové adresy nebo jednoho ze stávajících účtů sociálních sítí se můžete zaregistrovat.

   > [!NOTE]
   > Pokud je Azure Active Directory B2C jedinou možností, která je povolená na kartě **identity** na portálu vydavatele, budete přesměrováni přímo na zásadu registrace.

   ![Portál pro vývojáře][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Po dokončení registrace se budete přesměrováni zpět na portál pro vývojáře. Nyní jste přihlášeni k portálu pro vývojáře pro vaši instanci služby API Management.

    ![Registrace dokončena][api-management-registration-complete]

## <a name="next-steps"></a>Další kroky

*  [Přehled Azure Active Directory B2C]
*  [Azure Active Directory B2C: rozšiřitelná architektura zásad]
*  [Použití účet Microsoft jako poskytovatele identity v Azure Active Directory B2C]
*  [Použijte účet Google jako poskytovatele identity v Azure Active Directory B2C]
*  [Použití účtu LinkedIn jako poskytovatele identity v Azure Active Directory B2C]
*  [Použijte účet Facebook jako poskytovatele identity v Azure Active Directory B2C]



[api-management-howto-add-b2c-identity-provider]: ./media/api-management-howto-aad-b2c/api-management-add-b2c-identity-provider.PNG
[api-management-howto-copy-b2c-identity-provider-redirect-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-identity-provider-redirect-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

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
[Přehled Azure Active Directory B2C]: ../active-directory-b2c/overview.md
[Postup autorizace vývojářských účtů pomocí Azure Active Directory]: ./api-management-howto-aad.md
[Azure Active Directory B2C: rozšiřitelná architektura zásad]: ../active-directory-b2c/user-flow-overview.md
[Použití účet Microsoft jako poskytovatele identity v Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-microsoft-account.md
[Použijte účet Google jako poskytovatele identity v Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-google.md
[Použijte účet Facebook jako poskytovatele identity v Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-facebook.md
[Použití účtu LinkedIn jako poskytovatele identity v Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-linkedin.md

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
