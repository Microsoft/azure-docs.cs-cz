---
title: Přidat aplikaci mimo galerii – Microsoft Identity Platform | Microsoft Docs
description: Přidejte do svého tenanta služby Azure AD aplikaci mimo galerii.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9691ecb8d742952ba4364dcda2b62c3829f79d60
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760316"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Přidání aplikace, která není v seznamu (galerie), do vaší organizace Azure AD

Kromě možností v [galerii aplikací Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)máte možnost Přidat **aplikaci mimo galerii**. Můžete přidat libovolnou aplikaci, která již existuje ve vaší organizaci, nebo jakoukoli aplikaci třetí strany od dodavatele, který ještě není součástí Galerie Azure AD. V závislosti na vaší [licenční smlouvě](https://azure.microsoft.com/pricing/details/active-directory/)jsou k dispozici tyto možnosti:

- Samoobslužná integrace všech aplikací, které podporují zprostředkovatele identity [Security Assertion Markup Language (SAML) 2,0](https://wikipedia.org/wiki/SAML_2.0) (SP-inicied nebo IDP)
- Samoobslužná integrace jakékoli webové aplikace, která má přihlašovací stránku založenou na jazyce HTML pomocí [jednotného přihlašování založeného na heslech](what-is-single-sign-on.md#password-based-sso)
- Samoobslužné připojení aplikací, které používají [systém pro protokol SCIM (Domain Identity Management) pro zřizování uživatelů](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Možnost Přidat odkazy na libovolnou aplikaci ve [Spouštěči aplikací Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) nebo na [panelu přístup k Azure AD](what-is-single-sign-on.md#linked-sign-on)

Tento článek popisuje, jak přidat aplikaci mimo galerii do **podnikových aplikací** v Azure Portal bez psaní kódu. Pokud místo toho hledáte pokyny pro vývojáře, jak integrovat vlastní aplikace se službou Azure AD, přečtěte si téma [scénáře ověřování pro Azure AD](../develop/authentication-scenarios.md). Když vyvíjíte aplikaci, která používá moderní protokol, jako je [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) k ověřování uživatelů, můžete ji zaregistrovat na platformě Microsoft identity pomocí prostředí [Registrace aplikací](../develop/quickstart-register-app.md) v Azure Portal.

## <a name="add-a-non-gallery-application"></a>Přidat aplikaci mimo galerii

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) pomocí účtu správce Microsoft Identity Platform.

2. Vyberte možnost **podnikové aplikace**  >  **Nová aplikace**.

3. (Volitelné, ale doporučené) Do vyhledávacího pole **Procházet galerii Azure AD** zadejte zobrazovaný název aplikace. 

4. Vyberte **vytvořit vlastní aplikaci**. Zobrazí se stránka **vytvořit vlastní aplikaci** .

   ![Přidání aplikace](media/add-non-gallery-app/create-your-own-application.png)

5. Začněte psát zobrazované jméno pro novou aplikaci. Pokud existují aplikace Galerie s podobným názvem, zobrazí se v seznamu výsledků hledání.

   > [!NOTE]
   > Pokud je to možné, doporučujeme použít verzi Galerie vaší aplikace. Pokud se ve výsledcích hledání zobrazí aplikace, kterou chcete přidat, vyberte aplikaci a přeskočte zbytek tohoto postupu.

6. V části **co chcete s vaší aplikací dělat?** vyberte možnost **integrace jakékoli jiné aplikace, kterou v galerii nenajdete**. Tato možnost se obvykle používá pro aplikace SAML a WS-nakrmené.

   > [!NOTE]
   > Další dvě možnosti jsou používány v následujících scénářích:
   >* **Konfigurace proxy aplikací pro zabezpečený vzdálený přístup k místní aplikaci** otevře stránku konfigurace pro Azure proxy aplikací služby AD a konektory.
   >* **Zaregistrujte aplikaci, na které pracujete, abyste mohli integrovat s Azure AD** , otevřete stránku **Registrace aplikací** . Tato možnost se obvykle používá pro aplikace OpenID Connect.

7. Vyberte **Vytvořit**. Otevře se stránka s **přehledem** aplikace.

## <a name="configure-user-sign-in-properties"></a>Konfigurace vlastností přihlašování uživatelů

1. Výběrem **vlastnosti** otevřete podokno vlastnosti pro úpravy.

    ![Upravit podokno vlastností](media/add-non-gallery-app/edit-properties.png)

2. Nastavte následující možnosti, chcete-li určit, jak se uživatelé, kteří jsou přiřazeni nebo nepřiřazeni k aplikaci, mohou přihlásit k aplikaci a uživatel může aplikaci zobrazit na přístupovém panelu.

    - Možnost **Mohou se uživatelé přihlásit?** určuje, jestli se můžou přihlásit uživatelé přiřazení k aplikaci.
    - **Vyžadováno přiřazení uživatele** určuje, jestli se uživatelé, kteří nejsou přiřazeni k aplikaci, můžou přihlásit.
    - Možnost **Uvidí ji uživatelé?** určuje, jestli se uživatelům přiřazeným k aplikaci zobrazí aplikace na přístupovém panelu a ve spouštěči O365.

      Chování pro **přiřazené** uživatele:

       | Nastavení vlastností aplikace | | | Prostředí pro přiřazené uživatele | |
       |---|---|---|---|---|
       | Mohou se uživatelé přihlásit? | Je přiřazení uživatelů povinné? | Uvidí ji uživatelé? | Mohou se přiřazení uživatelé přihlásit? | Zobrazí se aplikace přiřazeným uživatelům?* |
       | ano | ano | ano | ano | ano  |
       | ano | ano | ne  | ano | ne   |
       | ano | ne  | ano | ano | ano  |
       | ano | ne  | ne  | ano | ne   |
       | ne  | ano | ano | ne  | ne   |
       | ne  | ano | ne  | ne  | ne   |
       | ne  | ne  | ano | ne  | ne   |
       | ne  | ne  | ne  | ne  | ne   |

      Chování pro **nepřiřazené** uživatele:

       | Nastavení vlastností aplikace | | | Prostředí pro nepřiřazené uživatele | |
       |---|---|---|---|---|
       | Povolit uživatelům přihlášení? | Je přiřazení uživatelů povinné? | Uvidí ji uživatelé? | Mohou se nepřiřazení uživatelé přihlásit? | Zobrazí se aplikace nepřiřazeným uživatelům?* |
       | ano | ano | ano | ne  | ne   |
       | ano | ano | ne  | ne  | ne   |
       | ano | ne  | ano | ano | ne   |
       | ano | ne  | ne  | ano | ne   |
       | ne  | ano | ano | ne  | ne   |
       | ne  | ano | ne  | ne  | ne   |
       | ne  | ne  | ano | ne  | ne   |
       | ne  | ne  | ne  | ne  | ne   |

     * Zobrazí se aplikace uživateli na přístupovém panelu a ve spouštěči aplikací Office 365?

3. Pokud chcete použít vlastní logo, vytvořte logo 215 215 pixelů a uložte ho ve formátu PNG. Pak přejděte k logu a nahrajte ho.

    ![Změna loga](media/add-non-gallery-app/change-logo.png)

4. Až budete hotovi, vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

Teď, když jste přidali aplikaci do vaší organizace Azure AD, [Vyberte metodu jednotného přihlašování](what-is-single-sign-on.md#choosing-a-single-sign-on-method) , kterou chcete použít, a podívejte se na příslušný článek níže:

- [Konfigurace jednotného přihlašování pomocí SAML](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurace jednotného přihlašování k heslům](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurace propojeného přihlašování](configure-linked-sign-on.md)
