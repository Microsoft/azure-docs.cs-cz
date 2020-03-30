---
title: Přidání aplikace, která není v galerii – platforma identit Microsoftu | Dokumenty společnosti Microsoft
description: Přidejte do klienta Azure AD aplikaci, která není galerie.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd5a5f100dbe09c3b82f58183a118ee3bf455f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063607"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Přidání neuvedené (negalerie) aplikace do organizace Azure AD

Kromě možností v [galerii aplikací Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)máte možnost přidat **aplikaci bez galerie**. Můžete přidat libovolnou aplikaci, která již existuje ve vaší organizaci, nebo jakoukoli aplikaci jiného výrobce od dodavatele, který ještě není součástí galerie Azure AD. V závislosti na [licenční smlouvě](https://azure.microsoft.com/pricing/details/active-directory/)jsou k dispozici následující funkce:

- Samoobslužná integrace libovolné aplikace, která podporuje zprostředkovatele identity s [kontrolním výrazem zabezpečení (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) (spuštěná aktualizace SP nebo iniciována protokolem IDP)
- Samoobslužná integrace jakékoli webové aplikace, která má přihlašovací stránku založenou na HTML pomocí [uživatelského přihlašování založeného na heslech](what-is-single-sign-on.md#password-based-sso)
- Samoobslužné připojení aplikací, které používají [protokol SCIM systémpro správu identit mezi doménami (Zřizování uživatelů)](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Možnost přidání odkazů na libovolnou aplikaci ve [spouštěči aplikací Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) nebo [na přístupovém panelu Azure AD](what-is-single-sign-on.md#linked-sign-on)

Tento článek popisuje, jak přidat aplikaci bez galerie do **podnikových aplikací** na webu Azure Portal bez psaní kódu. Pokud místo toho hledáte pokyny pro vývojáře, jak integrovat vlastní aplikace s Azure AD, přečtěte si článek [Scénáře ověřování pro Azure AD](../develop/authentication-scenarios.md). Když vyvíjíte aplikaci, která k ověřování uživatelů používá moderní protokol, jako je [OpenId Connect/OAuth,](../develop/active-directory-v2-protocols.md) můžete ji zaregistrovat na platformě identit Microsoftu pomocí prostředí [pro registrace aplikací](../develop/quickstart-register-app.md) na webu Azure Portal.

## <a name="add-a-non-gallery-application"></a>Přidání aplikace bez galerie

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) pomocí svého účtu správce platformy identit Microsoftu.

2. Vyberte **možnost Podnikové aplikace** > **Nová aplikace**.

3. (Volitelné, ale doporučené) Do vyhledávacího pole Procházet galerii **Azure AD** zadejte zobrazovaný název aplikace. 

4. Vyberte **Vytvořit vlastní aplikaci**. Zobrazí se stránka **Vytvořit vlastní aplikaci.**

   ![Přidání aplikace](media/add-non-gallery-app/create-your-own-application.png)

5. Začněte psát zobrazovaný název nové aplikace. Pokud existují nějaké aplikace galerie s podobnými názvy, zobrazí se v seznamu výsledků hledání.

   > [!NOTE]
   > Doporučujeme používat galerijní verzi vaší aplikace, kdykoli je to možné. Pokud se aplikace, kterou chcete přidat, zobrazí ve výsledcích hledání, vyberte ji a zbytek tohoto postupu přeskočte.

6. V části Co chcete dělat s **Integrate any other application you don't find in the gallery** **vaší aplikací?** Tato možnost se obvykle používá pro aplikace SAML a WS-Fed.

   > [!NOTE]
   > Další dvě možnosti se používají v následujících scénářích:
   >* **Konfigurace proxy aplikace pro zabezpečený vzdálený přístup k místní aplikaci** otevře stránku konfigurace pro proxy aplikace Azure AD a konektory.
   >* **Zaregistrujte aplikaci, na které pracujete pro integraci s Azure AD** otevře stránku **registrace aplikací.** Tato možnost se obvykle používá pro aplikace OpenID Connect.

7. Vyberte **Vytvořit**. Otevře se stránka **Přehled** aplikace.

## <a name="configure-user-sign-in-properties"></a>Konfigurace vlastností přihlašování uživatelů

1. Výběrem **možnosti Vlastnosti** otevřete podokno vlastností pro úpravy.

    ![Podokno Upravit vlastnosti](media/add-non-gallery-app/edit-properties.png)

2. Nastavte následující možnosti, abyste určili, jak se uživatelé, kteří jsou přiřazeni nebo nepřiřazeni k aplikaci, mohou přihlásit k aplikaci a zda uživatel může aplikaci zobrazit na přístupovém panelu.

    - Možnost **Mohou se uživatelé přihlásit?** určuje, jestli se můžou přihlásit uživatelé přiřazení k aplikaci.
    - **Požadované přiřazení uživatele** určuje, zda se uživatelé, kteří nejsou přiřazeni k aplikaci, mohou přihlásit.
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
       | Povoleno pro uživatele k přihlášení? | Je přiřazení uživatelů povinné? | Uvidí ji uživatelé? | Mohou se nepřiřazení uživatelé přihlásit? | Zobrazí se aplikace nepřiřazeným uživatelům?* |
       | ano | ano | ano | ne  | ne   |
       | ano | ano | ne  | ne  | ne   |
       | ano | ne  | ano | ano | ne   |
       | ano | ne  | ne  | ano | ne   |
       | ne  | ano | ano | ne  | ne   |
       | ne  | ano | ne  | ne  | ne   |
       | ne  | ne  | ano | ne  | ne   |
       | ne  | ne  | ne  | ne  | ne   |

     * Zobrazí se aplikace uživateli na přístupovém panelu a ve spouštěči aplikací Office 365?

3. Chcete-li použít vlastní logo, vytvořte logo o rozměrech 215 x 215 pixelů a uložte ho ve formátu PNG. Pak přejděte na své logo a nahrajte ho.

    ![Změna loga](media/add-non-gallery-app/change-logo.png)

4. Až budete hotovi, vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

Teď, když jste přidali aplikaci do organizace Azure AD, [zvolte metodu jednotného přihlášení,](what-is-single-sign-on.md#choosing-a-single-sign-on-method) kterou chcete použít, a podívejte se na příslušný článek níže:

- [Konfigurace jednotného přihlašování pomocí SAML](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurace jednotného přihlašování hesla](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurace propojeného přihlašování](configure-linked-sign-on.md)
