---
title: Autorizovat vývojářským účtům pomocí Azure Active Directory B2C - Azure API Management | Microsoft Docs
description: Zjistěte, jak mají autorizovat uživatelé pomocí Azure Active Directory B2C ve službě API Management.
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
ms.openlocfilehash: 1ef0d6cc34b19b9bc8fe8d0b88f73a2657848747
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
> [!WARNING]
> Integrace se službou Azure Active Directory B2C je k dispozici v [Developer, Standard a Premium](https://azure.microsoft.com/en-us/pricing/details/api-management/) pouze úrovně.

# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Jak se mají autorizovat vývojářským účtům pomocí Azure Active Directory B2C ve službě Azure API Management
## <a name="overview"></a>Přehled
Azure Active Directory B2C je cloudové řešení správy identity pro určených webové a mobilní aplikace. Můžete ho použít ke správě přístupu na portálu pro vývojáře. Tento průvodce vám ukáže konfigurací, která je vyžadována ve službě API Management k integraci s Azure Active Directory B2C. Informace o povolení přístup k portálu pro vývojáře pomocí klasického Azure Active Directory najdete v tématu [autorizace vývojářským účtům pomocí služby Azure Active Directory].

> [!NOTE]
> Pokud chcete provést kroky v této příručce, musí mít první klienta služby Azure Active Directory B2C vytvořit aplikaci v. Také je potřeba mít registrace a přihlášení zásady, které jsou připravené. Další informace najdete v tématu [přehled Azure Active Directory B2C].

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autorizovat vývojářským účtům pomocí Azure Active Directory B2C

1. Chcete-li začít, klikněte na tlačítko **portál vydavatele** na portálu Azure pro služby API Management. Tím přejdete na portál vydavatele služby API Management.

   ![Portál vydavatele][api-management-management-console]

   > [!NOTE]
   > Pokud jste instanci služby API Management ještě nevytvořili, přečtěte si téma [vytvoření instance API Management] [ Create an API Management service instance] v [Začínáme s Azure API Management kurzu] [Get started with Azure API Management].

2. Na **API Management** nabídky, klikněte na tlačítko **zabezpečení**. Na **identity** , zvolte **Azure Active Directory B2C**.

  ![Externí identity 1][api-management-howto-aad-b2c-security-tab]

3. Poznamenejte si **adresy URL pro přesměrování** a přepnout na Azure Active Directory B2C na portálu Azure.

  ![Externí identity 2][api-management-howto-aad-b2c-security-tab-reply-url]

4. Klikněte **aplikace** tlačítko.

  ![Zaregistrujte novou aplikaci 1][api-management-howto-aad-b2c-portal-menu]

5. Klikněte **přidat** tlačítko vytvořte novou aplikaci Azure Active Directory B2C.

  ![Zaregistrujte novou aplikaci 2][api-management-howto-aad-b2c-add-button]

6. V **novou aplikaci** okno, zadejte název aplikace. Zvolte **Ano** pod **webové aplikaci nebo webové rozhraní API**a zvolte **Ano** pod **povolit implicitní tok**. Zkopírujte **adresy URL pro přesměrování** z **Azure Active Directory B2C** části **identity** v portálu vydavatele a vložte ji do **odpovědi Adresa URL** textové pole.

  ![Zaregistrujte novou aplikaci 3][api-management-howto-aad-b2c-app-details]

7. Klikněte na tlačítko **Vytvořit**. Při vytvoření aplikace se zobrazí v **aplikace** okno. Klikněte na název aplikace zobrazíte její podrobnosti.

  ![Zaregistrujte novou aplikaci 4][api-management-howto-aad-b2c-app-created]

8. Z **vlastnosti** zkopírujte **ID aplikace** do schránky.

  ![ID aplikace 1][api-management-howto-aad-b2c-app-id]

9. Přepněte zpět na portál vydavatele a ID do vložte **Id klienta** textové pole.

  ![ID aplikace 2][api-management-howto-aad-b2c-client-id]

10. Přepněte zpět na portálu Azure, klikněte na tlačítko **klíče** tlačítko a potom klikněte na **vygenerovat klíč**. Klikněte na tlačítko **Uložit** k uložení konfigurace a zobrazení **klíč aplikace**. Klíč zkopírujte do schránky.

  ![Aplikace klíč 1][api-management-howto-aad-b2c-app-key]

11. Přepněte zpět na portál vydavatele a vložte klíč do **tajný klíč klienta** textové pole.

  ![Klíč aplikace 2][api-management-howto-aad-b2c-client-secret]

12. Zadejte název domény klienta Azure Active Directory B2C v **povolené klienta**.

  ![Povolené klienta][api-management-howto-aad-b2c-allowed-tenant]

13. Zadejte **zásady registrace** a **přihlášení zásady**. Volitelně můžete zadat také **zásady úpravy profilu** a **zásady resetování hesel**.

  ![Zásady][api-management-howto-aad-b2c-policies]

  > [!NOTE]
  > Další informace o zásadách najdete v tématu [Azure Active Directory B2C: rozšiřitelný rámec zásad].

14. Po zadání požadované konfigurace, klikněte na tlačítko **Uložit**.

  Po uložení změn se vývojáři bude moct vytvářet nové účty a přihlaste se k portálu pro vývojáře pomocí Azure Active Directory B2C.

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>Zaregistrujte si účet pro vývojáře pomocí Azure Active Directory B2C

1. Zaregistrujte si účet pro vývojáře pomocí Azure Active Directory B2C, otevřete nové okno prohlížeče a přejděte na portál pro vývojáře. Klikněte **zaregistrovat** tlačítko.

   ![Portál pro vývojáře 1][api-management-howto-aad-b2c-dev-portal]

2. Zvolte se zaregistrovat **Azure Active Directory B2C**.

   ![Portál pro vývojáře 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Budete přesměrováni na zásady registrace, který jste nakonfigurovali v předchozím oddílu. Můžete přihlásit pomocí e-mailovou adresu nebo jeden z vaší účtů na sociálních.

   > [!NOTE]
   > Pokud Azure Active Directory B2C je jedinou možností, které je zapnutá **identity** karta na portálu vydavatele je budete přesměrováni na zásady registrace přímo.

   ![Portál pro vývojáře][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Po dokončení registrace budete přesměrováni zpět na portál pro vývojáře. Jste teď přihlášení na portál pro vývojáře pro instanci služby API Management.

    ![Registrace je dokončena.][api-management-registration-complete]

## <a name="next-steps"></a>Další postup

*  [přehled Azure Active Directory B2C]
*  [Azure Active Directory B2C: rozšiřitelný rámec zásad]
*  [Použít účet Microsoft jako poskytovatel identit v Azure Active Directory B2C]
*  [Použít účet Google jako poskytovatel identit v Azure Active Directory B2C]
*  [Použít účet LinkedIn jako poskytovatel identit v Azure Active Directory B2C]
*  [Použít účet Facebook jako poskytovatel identit v Azure Active Directory B2C]




[api-management-howto-aad-b2c-security-tab]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab.PNG
[api-management-howto-aad-b2c-security-tab-reply-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab-reply-url.PNG
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
[přehled Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[autorizace vývojářským účtům pomocí služby Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: rozšiřitelný rámec zásad]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Použít účet Microsoft jako poskytovatel identit v Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Použít účet Google jako poskytovatel identit v Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Použít účet Facebook jako poskytovatel identit v Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Použít účet LinkedIn jako poskytovatel identit v Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
