---
title: Přidání aplikace galerie – Azure Active Directory | Dokumenty společnosti Microsoft
description: Přečtěte si, jak přidat aplikaci z galerie Azure AD do podnikových aplikací Azure.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0a826c499790c86a20ea7061075a6c3e66b3a4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73062588"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Přidání aplikace galerie do organizace Azure AD

Azure Active Directory (Azure AD) má galerii, která obsahuje tisíce předintegrovaných aplikací, které jsou povolené s enterprise jednotné přihlašování. Tento článek popisuje obecné kroky pro přidání aplikace z galerie do organizace Azure AD.

> [!IMPORTANT]
> Nejprve zkontrolujte aplikaci v [seznamu kurzů o tom, jak integrovat aplikace SaaS s Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). Pravděpodobně najdete podrobné pokyny pro přidání a konfiguraci aplikace galerie, kterou chcete přidat.

## <a name="add-a-gallery-application"></a>Přidání aplikace galerie

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce vašeho tenanta Azure AD, správce cloudové aplikace nebo správce aplikace.

1. Na [webu Azure Portal](https://portal.azure.com)na levém navigačním panelu vyberte **Azure Active Directory**.

1. V podokně **Služby Azure Active Directory** vyberte **podnikové aplikace**.

    ![Otevřené podnikové aplikace](media/add-gallery-app/open-enterprise-apps.png)


3. Pokud chcete do tenanta přidat aplikaci galerie, vyberte **Nová aplikace**.

    ![Vyberte Nová aplikace a přidejte do tenanta aplikaci galerie.](media/add-gallery-app/new-application.png)

 4. Přepněte na nové prostředí náhledu galerie: V banneru v horní části **stránky Přidat aplikaci**vyberte odkaz s textem **Kliknutím sem vyzkoušíte novou a vylepšenou galerii aplikací**.

5. Podokno **Procházet galerii Azure AD** otevře a zobrazí dlaždice pro cloudové platformy, místní aplikace a doporučené aplikace. Všimněte si, že aplikace uvedené v části **Doporučené aplikace** mají ikony označující, zda podporují federované jednotné přihlašování (SSO) a zřizování.

    ![Hledání aplikace podle názvu nebo kategorie](media/add-gallery-app/browse-gallery.png)

6. Vyhledejte v galerii aplikaci, kterou chcete přidat, nebo ji vyhledejte zadáním jejího názvu do vyhledávacího pole. Pak vyberte aplikaci z výsledků. (Nepovinné) Ve formuláři můžete upravit název aplikace tak, aby odpovídal potřebám vaší organizace.

    ![Ukazuje, jak přidat aplikaci z galerie](media/add-gallery-app/create-application.png)

7. Vyberte **Vytvořit**. Zobrazí se stránka Začínáme s možnostmi konfigurace aplikace pro vaši organizaci.

## <a name="configure-user-sign-in-properties"></a>Konfigurace vlastností přihlašování uživatelů

1. Výběrem **možnosti Vlastnosti** otevřete podokno vlastností pro úpravy.

    ![Podokno Upravit vlastnosti](media/add-gallery-app/edit-properties.png)

1. Nastavte následující možnosti, abyste určili, jak se uživatelé, kteří jsou přiřazeni nebo nepřiřazeni k aplikaci, mohou přihlásit k aplikaci a zda uživatel může aplikaci zobrazit na přístupovém panelu.

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

1. Chcete-li použít vlastní logo, vytvořte logo o rozměrech 215 x 215 pixelů a uložte ho ve formátu PNG. Pak přejděte na své logo a nahrajte ho.

    ![Změna loga](media/add-gallery-app/change-logo.png)

1. Až budete hotovi, vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

Teď, když jste přidali aplikaci do organizace Azure AD, [zvolte metodu jednotného přihlášení,](what-is-single-sign-on.md#choosing-a-single-sign-on-method) kterou chcete použít, a podívejte se na příslušný článek níže:

- [Konfigurace jednotného přihlašování pomocí SAML](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurace jednotného přihlašování hesla](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurace propojeného přihlašování](configure-linked-sign-on.md)

