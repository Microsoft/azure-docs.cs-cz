---
title: Autorizace vývojářských účtů pomocí služby Azure Active Directory B2C
titleSuffix: Azure API Management
description: Zjistěte, jak autorizovat uživatele pomocí Azure Active Directory B2C ve správě rozhraní API.
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
ms.openlocfilehash: b8215cd852d54283bfc6bd47e77d7d63ee4e2582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475489"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Autorizace vývojářských účtů pomocí Azure Active Directory B2C ve službě Azure API Management

## <a name="overview"></a>Přehled

Azure Active Directory B2C je cloudové řešení pro správu identit pro webové a mobilní aplikace orientované na spotřebitele. Můžete ji použít ke správě přístupu k portálu pro vývojáře. Tato příručka vám ukáže konfiguraci, která je vyžadována ve službě API Management pro integraci s Azure Active Directory B2C. Informace o povolení přístupu k portálu pro vývojáře pomocí klasické služby Azure Active Directory najdete v tématu [Autorizace vývojářských účtů pomocí služby Azure Active Directory].

> [!NOTE]
> Chcete-li provést kroky v této příručce, musíte mít nejprve klienta Azure Active Directory B2C k vytvoření aplikace v aplikaci. Také musíte mít připravené zásady registrace a přihlášení. Další informace naleznete [v tématu Přehled služby Azure Active Directory B2C].

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autorizace vývojářských účtů pomocí služby Azure Active Directory B2C

1. Pokud chcete začít, přihlaste se na [portál Azure portal](https://portal.azure.com) a vyhledejte instanci api management.

   > [!NOTE]
   > Pokud jste ještě nevytvořili instanci služby Správa rozhraní API, přečtěte si informace [o vytvoření instance služby Správa rozhraní API][Create an API Management service instance] v kurzu [Začínáme s Azure API .][Get started with Azure API Management]

1. V části **Identity**. Klikněte na **+Přidat** nahoře.

   Vpravo se zobrazí podokno **Přidat zprostředkovatele identity.** Zvolte **Azure Active Directory B2C**.
    
   ![Přidání AAD B2C jako zprostředkovatele identity][api-management-howto-add-b2c-identity-provider]

1. Zkopírujte **adresu URL přesměrování**.

   ![Adresa URL přesměrování zprostředkovatele identity AAD B2C][api-management-howto-copy-b2c-identity-provider-redirect-url]

1. Na nové kartě přejděte k tenantovi Služby Azure Active Directory B2C na webu Azure Portal a otevřete okno **Aplikace.**

   ![Registrace nové aplikace 1][api-management-howto-aad-b2c-portal-menu]

1. Kliknutím na tlačítko **Přidat** vytvořte novou aplikaci Azure Active Directory B2C.

   ![Registrace nové aplikace 2][api-management-howto-aad-b2c-add-button]

1. V okně **Nová aplikace** zadejte název aplikace. V části **Web App/Web API**zvolte Ano a v **položce Povolit implicitní tok**zvolte **Ano** a v položce **Ano** . Pak vložte **adresu URL přesměrování** zkopírovanou v kroku 3 do textového pole Adresa URL pro **odpověď.**

   ![Registrace nové aplikace 3][api-management-howto-aad-b2c-app-details]

1. Pokud používáte nový portál pro vývojáře (nikoli starší portál pro vývojáře), zadejte do deklarací identity aplikace **křestní jméno**, **příjmení**a **ID objektu uživatele.**

    ![Deklarace identity aplikace](./media/api-management-howto-aad-b2c/api-management-application-claims.png)

1. Klikněte na tlačítko **Vytvořit**. Po vytvoření aplikace se zobrazí v okně **Aplikace.** Kliknutím na název aplikace zobrazíte její podrobnosti.

   ![Registrace nové aplikace 4][api-management-howto-aad-b2c-app-created]

1. Z okna **Vlastnosti** zkopírujte **ID aplikace** do schránky.

   ![ID aplikace 1][api-management-howto-aad-b2c-app-id]

1. Přepněte zpět do podokna Api Management **Add identity provider** a vložte ID do textového pole **ID klienta.**
    
1.  Přepněte zpět na registraci aplikace B2C, klikněte na tlačítko **Klíče** a potom klikněte na **Generovat klíč**. Klepnutím na tlačítko **Uložit** uložte konfiguraci a zobrazte **klíč aplikace**. Zkopírujte klíč do schránky.

    ![Klíč aplikace 1][api-management-howto-aad-b2c-app-key]

1.  Přepněte zpět do podokna Api Management **Add identity provider** a vložte klíč do textového pole **Klientský tajný klíč.**
    
1.  Zadejte název domény klienta Azure Active Directory B2C v **tenantovi Přihlášení**.

1.  Pole **Autorita** umožňuje řídit přihlašovací adresu URL Azure AD B2C, která se má použít. Nastavte hodnotu **na<your_b2c_tenant_name>.b2clogin.com**.

1. Zadejte **zásady registrace** a **zásady přihlášení** ze zásad klienta B2C. Volitelně můžete také zadat **zásady pro úpravy profilu** a **zásady pro obnovení hesla**.

1. Po zadání požadované konfigurace klepněte na tlačítko **Uložit**.

    Po uložení změn budou vývojáři moct vytvářet nové účty a přihlašovat se k portálu pro vývojáře pomocí služby Azure Active Directory B2C.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>Portál pro vývojáře – přidání ověřování účtu Azure AD B2C

Na portálu pro vývojáře je možné se přihlásit pomocí AAD B2C pomocí **tlačítka Přihlášení: Widget OAuth.** Widget je již součástí přihlašovací stránky výchozího obsahu portálu pro vývojáře.

Přestože nový účet bude automaticky vytvořen vždy, když se nový uživatel přihlásí pomocí AAD B2C, můžete zvážit přidání stejného widgetu na registrační stránku.

Widget **Registrace: OAuth** představuje formulář používaný pro registraci pomocí oauth.

> [!IMPORTANT]
> Změny ad se projeví [znovu publikovat na portálu.](api-management-howto-developer-portal-customize.md#publish)

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>Starší portál pro vývojáře – jak se zaregistrovat pomocí Azure AD B2C

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Pokud si chcete zaregistrovat vývojářský účet pomocí Služby Azure Active Directory B2C, otevřete nové okno prohlížeče a přejděte na portál pro vývojáře. Klikněte na tlačítko **Zaregistrovat se.**

   ![Vývojářský portál 1][api-management-howto-aad-b2c-dev-portal]

2. Zvolte registraci pomocí **služby Azure Active Directory B2C**.

   ![Vývojářský portál 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Budete přesměrováni na zásady registrace, které jste nakonfigurovali v předchozí části. Zvolte, zda se chcete zaregistrovat pomocí své e-mailové adresy nebo některého ze stávajících účtů na sociálních sítích.

   > [!NOTE]
   > Pokud je Služba Azure Active Directory B2C jedinou možností, která je povolena na kartě **Identity** na portálu pro vydavatele, budete přesměrováni přímo na zásady registrace.

   ![Portál pro vývojáře][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Po dokončení registrace budete přesměrováni zpět na portál pro vývojáře. Teď jste přihlášení k portálu pro vývojáře pro instanci služby Api Management.

    ![Registrace dokončena][api-management-registration-complete]

## <a name="next-steps"></a>Další kroky

*  [Přehled služby Azure Active Directory B2C]
*  [Azure Active Directory B2C: Rozšiřitelný rámec zásad]
*  [Použití účtu Microsoft jako poskytovatele identity ve službě Azure Active Directory B2C]
*  [Použití účtu Google jako poskytovatele identity ve službě Azure Active Directory B2C]
*  [Použití účtu LinkedIn jako poskytovatele identity ve službě Azure Active Directory B2C]
*  [Použití účtu Na Facebooku jako poskytovatele identity ve službě Azure Active Directory B2C]



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
[Přehled služby Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Jak autorizovat vývojářské účty pomocí Služby Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: Rozšiřitelný rámec zásad]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Použití účtu Microsoft jako poskytovatele identity ve službě Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Použití účtu Google jako poskytovatele identity ve službě Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Použití účtu Na Facebooku jako poskytovatele identity ve službě Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Použití účtu LinkedIn jako poskytovatele identity ve službě Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
