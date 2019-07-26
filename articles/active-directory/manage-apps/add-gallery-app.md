---
title: Přidání aplikace Galerie – Azure Active Directory | Microsoft Docs
description: Naučte se, jak přidat aplikaci z Galerie Azure AD do podnikových aplikací Azure.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 06/18/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bdf275bbafa9c46cfc4577ac2843da0be74c7ef
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477270"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Přidání aplikace Galerie do vaší organizace Azure AD

Azure Active Directory (Azure AD) obsahuje galerii obsahující tisíce předem integrovaných aplikací, které jsou povolené pomocí podnikového jednotného přihlašování. Tento článek popisuje obecné kroky pro přidání aplikace z Galerie do vaší organizace Azure AD.

> [!IMPORTANT]
> Nejdřív v [seznamu kurzů, jak integrovat aplikace SaaS s Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/), vyhledejte svoji aplikaci. Pravděpodobně najdete podrobné pokyny pro přidání a konfiguraci aplikace Galerie, kterou chcete přidat.

## <a name="add-a-gallery-application"></a>Přidat aplikaci Galerie

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce vašeho tenanta Azure AD, správce cloudové aplikace nebo správce aplikace.

1. V [Azure Portal](https://portal.azure.com)v levém navigačním panelu vyberte možnost **Azure Active Directory**.

1. V podokně **Azure Active Directory** vyberte možnost **podnikové aplikace**.

    ![Otevřené podnikové aplikace](media/add-application-portal/open-enterprise-apps.png)

1. Vyberte **Nová aplikace**.

    ![Nová aplikace](media/add-application-portal/new-application.png)

1. V části **Přidat z Galerie**do vyhledávacího pole zadejte název aplikace, kterou chcete přidat. 

    ![Hledání podle názvu nebo kategorie](media/add-application-portal/categories.png)

1. Vyberte aplikaci z výsledků.

1. Volitelné Ve formuláři specifickém pro aplikaci můžete upravit název aplikace tak, aby odpovídala potřebám vaší organizace.

1. Vyberte **Přidat**. Otevře se  Stránka s přehledem aplikace.

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

