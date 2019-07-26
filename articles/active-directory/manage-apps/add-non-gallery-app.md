---
title: Přidat aplikaci mimo galerii – Microsoft Identity Platform | Microsoft Docs
description: Přidejte do svého tenanta služby Azure AD aplikaci mimo galerii.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 06/18/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: db8d8d6df16c5df7e29d8bb870c5d5eda6d8a2d3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477265"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Přidání aplikace, která není v seznamu (galerie), do vaší organizace Azure AD

Kromě možností v [galerii aplikací Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)máte možnost Přidat **aplikaci mimo galerii**. Můžete přidat libovolnou aplikaci, která již existuje ve vaší organizaci, nebo jakoukoli aplikaci třetí strany od dodavatele, který ještě není součástí Galerie Azure AD. V závislosti na vaší [licenční smlouvě](https://azure.microsoft.com/pricing/details/active-directory/)jsou k dispozici tyto možnosti:

- Samoobslužná integrace všech aplikací, které podporují zprostředkovatele identity [Security Assertion Markup Language (SAML) 2,0](https://wikipedia.org/wiki/SAML_2.0) (SP-inicied nebo IDP)
- Samoobslužná integrace jakékoli webové aplikace, která má přihlašovací stránku založenou na jazyce HTML pomocí [jednotného přihlašování založeného na heslech](what-is-single-sign-on.md#password-based-sso)
- Samoobslužné připojení aplikací, které používají [systém pro protokol SCIM (Domain Identity Management) pro zřizování uživatelů](use-scim-to-provision-users-and-groups.md)
- Možnost Přidat odkazy na libovolnou aplikaci ve [Spouštěči aplikací Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) nebo na [panelu přístup k Azure AD](what-is-single-sign-on.md#linked-sign-on)

Tento článek popisuje, jak přidat aplikaci mimo galerii do podnikových **aplikací** v Azure Portal bez psaní kódu. Pokud místo toho hledáte pokyny pro vývojáře, jak integrovat vlastní aplikace se službou Azure AD, přečtěte si téma [scénáře ověřování pro Azure AD](../develop/authentication-scenarios.md). Když vyvíjíte aplikaci, která používá moderní protokol, jako je [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) k ověřování uživatelů, můžete ji zaregistrovat na platformě Microsoft identity pomocí prostředí [Registrace aplikací](../develop/quickstart-register-app.md) v Azure Portal.

## <a name="add-a-non-gallery-application"></a>Přidat aplikaci mimo galerii

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) pomocí účtu správce Microsoft Identity Platform.
1. Vyberte možnost **podnikové aplikace** > **Nová aplikace**.
2. (Volitelné, ale doporučené) Do vyhledávacího pole **Přidat z Galerie** zadejte zobrazovaný název aplikace. Pokud se aplikace zobrazí ve výsledcích hledání, vyberte ji a přeskočte zbytek tohoto postupu.
3. Vyberte **aplikaci mimo galerii**. Zobrazí se stránka **Přidat vlastní aplikaci** .

   ![Přidat aplikaci](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. Zadejte zobrazovaný název pro novou aplikaci.
6. Vyberte **Přidat**. Otevře se  Stránka s přehledem aplikace.

## <a name="configure-user-sign-in-properties"></a>Konfigurace vlastností přihlašování uživatelů

1. Výběrem **vlastnosti** otevřete podokno vlastnosti pro úpravy.

    ![Upravit podokno vlastností](media/add-application-portal/edit-properties.png)

1. Nastavte následující možnosti, chcete-li určit, jak se uživatelé, kteří jsou přiřazeni nebo nepřiřazeni k aplikaci, mohou přihlásit k aplikaci a uživatel může aplikaci zobrazit na přístupovém panelu.

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

     \* Zobrazí se aplikace uživateli na přístupovém panelu a ve spouštěči aplikací Office 365?

1. Pokud chcete použít vlastní logo, vytvořte logo 215 215 pixelů a uložte ho ve formátu PNG. Pak přejděte k logu a nahrajte ho.

    ![Změna loga](media/add-application-portal/change-logo.png)

1. Až budete hotovi, vyberte **Uložit**.

## <a name="next-steps"></a>Další postup

Teď, když jste přidali aplikaci do vaší organizace Azure AD, [Vyberte metodu jednotného přihlašování](what-is-single-sign-on.md#choosing-a-single-sign-on-method) , kterou chcete použít, a podívejte se na příslušný článek níže:

- [Konfigurace jednotného přihlašování založeného na SAML](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurace jednotného přihlašování k heslům](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurace propojeného přihlašování](configure-linked-sign-on.md)
