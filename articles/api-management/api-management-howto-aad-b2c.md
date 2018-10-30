---
title: Autorizace vývojářských účtů pomocí Azure Active Directory B2C – Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak k autorizaci uživatelů pomocí Azure Active Directory B2C ve službě API Management.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: fbba1d9b4bdf1536ed596e9a78e53116fe824027
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232915"
---
> [!WARNING]
> Integrace se službou Azure Active Directory B2C je k dispozici v [Developer, Standard a Premium](https://azure.microsoft.com/pricing/details/api-management/) pouze úrovní.

# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Autorizace vývojářských účtů pomocí Azure Active Directory B2C ve službě Azure API Management
## <a name="overview"></a>Přehled
Azure Active Directory B2C je cloudové řešení správy identit pro zákaznické webové a mobilní aplikace. Můžete ho použít ke správě přístupu na portálu pro vývojáře. Tento průvodce vám ukáže konfigurace, které je nutné ve službě API Management můžete integrovat s Azure Active Directory B2C. Informace o povolení přístup k portálu pro vývojáře pomocí klasické Azure Active Directory najdete v tématu [autorizace vývojářských účtů pomocí Azure Active Directory].

> [!NOTE]
> K dokončení kroků v tomto průvodci, musíte nejprve mít tenanta služby Azure Active Directory B2C k vytvoření aplikace v. Potřebujete také registrace a přihlášení zásady, které jsou připravené. Další informace najdete v tématu [Přehled služby Azure Active Directory B2C].

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autorizace vývojářských účtů pomocí Azure Active Directory B2C

1. Abyste mohli začít, přihlaste se k [webu Azure portal](https://portal.azure.com) a vyhledejte vaší instance služby API Management.

   > [!NOTE]
   > Pokud jste instanci služby API Management ještě nevytvořili, přečtěte si téma [vytvoření instance služby API Management] [ Create an API Management service instance] v [Začínáme s Azure API Management kurzu] [Get started with Azure API Management].

2. V části **zabezpečení**vyberte **identit**. Klikněte na tlačítko **+ přidat** v horní části.

   **Přidat zprostředkovatele identity** otevře se podokno na pravé straně. Zvolte **Azure Active Directory B2C**.
    
   ![Přidat jako zprostředkovatele identity AAD B2C][api-management-howto-add-b2c-identity-provider]

3. Kopírovat **přesměrování URL adresy**.

  ![Adresa URL přesměrování zprostředkovatele identity AAD B2C][api-management-howto-copy-b2c-identity-provider-redirect-url]

4. Na nové kartě přístup k vašemu tenantovi Azure Active Directory B2C v webu Azure portal a otevřete **aplikací** okno.

  ![Registrace nové aplikace 1][api-management-howto-aad-b2c-portal-menu]

5. Klikněte na tlačítko **přidat** tlačítko Vytvořit novou aplikaci Azure Active Directory B2C.

  ![Registrace nové aplikace 2][api-management-howto-aad-b2c-add-button]

6. V **novou aplikaci** okně zadejte název aplikace. Zvolte **Ano** pod **webová aplikace/webové rozhraní API**a zvolte **Ano** pod **povolit implicitní tok**. Vložte **adresy URL pro přesměrování** zkopírovali v kroku 3 do **adresy URL odpovědi** textového pole.

  ![Registrace nové aplikace 3][api-management-howto-aad-b2c-app-details]

7. Klikněte na tlačítko **Vytvořit**. Když se aplikace, zobrazí se v **aplikací** okno. Klikněte na název aplikace zobrazíte její podrobnosti.

  ![Registrace nové aplikace 4][api-management-howto-aad-b2c-app-created]

8. Z **vlastnosti** okno, kopie **ID aplikace** do schránky.

  ![ID aplikace 1][api-management-howto-aad-b2c-app-id]

9. Přepněte zpět do API managementu **přidat zprostředkovatele identity** podokně a vložte ID do **Id klienta** textového pole.

  ![ID aplikace 2][api-management-howto-aad-b2c-client-id]

10. Vrátit zpět k registraci aplikace B2C, klikněte na tlačítko **klíče** tlačítko a pak klikněte na tlačítko **vygenerovat klíč**. Klikněte na tlačítko **Uložit** uložte konfiguraci a zobrazení **klíče aplikace**. Klíč zkopírujte do schránky.

  ![Klíč aplikace 1][api-management-howto-aad-b2c-app-key]

11. Přepněte zpět do API managementu **přidat zprostředkovatele identity** podokně a vložte klíč do **tajný kód klienta** textového pole.

  ![Klíč aplikace 2][api-management-howto-aad-b2c-client-secret]

12. Zadejte název domény tenanta Azure Active Directory B2C v **povolený Tenant**.

  ![Povoleného tenanta][api-management-howto-aad-b2c-allowed-tenant]

13. Zadejte **zásady registrace** a **Signin zásady** ze zásad Tenanta B2C. Volitelně můžete zadat taky **zásady úprav profilu** a **zásady resetování hesel**.

  ![Zásady][api-management-howto-aad-b2c-policies]

  > [!NOTE]
  > Další informace o zásadách najdete v tématu [Azure Active Directory B2C: rozhraní rozšiřitelných zásad].

14. Po zadání požadované konfigurace, klikněte na tlačítko **Uložit**.

  Po uložení změn vývojáři budou moct vytvářet nové účty a přihlaste se k portálu pro vývojáře pomocí Azure Active Directory B2C.

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>Zaregistrujte si vývojářský účet pomocí Azure Active Directory B2C

1. Zaregistrujte si vývojářský účet pomocí Azure Active Directory B2C, otevřete nové okno prohlížeče a přejděte na portál pro vývojáře. Klikněte na tlačítko **zaregistrovat** tlačítko.

   ![Portál pro vývojáře 1][api-management-howto-aad-b2c-dev-portal]

2. Rozhodnete se zaregistrovat pomocí **Azure Active Directory B2C**.

   ![Portál pro vývojáře 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Budete přesměrováni na zásady registrace, který jste nakonfigurovali v předchozím oddílu. Zvolte možnost registrace pomocí e-mailovou adresu nebo jednu z vašich účtů na sociálních sítích.

   > [!NOTE]
   > Pokud je jedinou možností, který je povolen v Azure Active Directory B2C **identit** kartu v portálu pro vydavatele, budete přesměrováni do zásad registrace přímo.

   ![Portál pro vývojáře][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Po dokončení registrace budete přesměrováni zpět na portál pro vývojáře. Jste teď přihlášení k portálu pro vývojáře pro instanci služby API Management.

    ![Registrace je dokončena.][api-management-registration-complete]

## <a name="next-steps"></a>Další postup

*  [Přehled služby Azure Active Directory B2C]
*  [Azure Active Directory B2C: Rozhraní rozšiřitelných zásad]
*  [Použít účet Microsoft jako zprostředkovatele identity v Azure Active Directory B2C]
*  [Použít účet Google jako zprostředkovatele identity v Azure Active Directory B2C]
*  [Použít účet LinkedIn jako zprostředkovatele identity v Azure Active Directory B2C]
*  [Pomocí účtu sítě Facebook jako zprostředkovatele identity v Azure Active Directory B2C]



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

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
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

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[Přehled služby Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Autorizace vývojářských účtů pomocí Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: Rozhraní rozšiřitelných zásad]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Použít účet Microsoft jako zprostředkovatele identity v Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Použít účet Google jako zprostředkovatele identity v Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Pomocí účtu sítě Facebook jako zprostředkovatele identity v Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Použít účet LinkedIn jako zprostředkovatele identity v Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
