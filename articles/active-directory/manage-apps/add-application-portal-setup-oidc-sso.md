---
title: 'Rychlý Start: nastavení jednotného přihlašování (SSO) založeného na OIDC pro aplikaci ve vašem tenantovi Azure Active Directory (Azure AD)'
description: Tento rychlý Start vás provede procesem nastavení jednotného přihlašování (SSO) založeného na OIDC pro aplikaci ve vašem tenantovi Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: e5695d5ded92e9bb41d39a082790cfbb456dbc38
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258350"
---
# <a name="quickstart-set-up-oidc-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Rychlý Start: nastavení jednotného přihlašování (SSO) založeného na OIDC pro aplikaci ve vašem tenantovi Azure Active Directory (Azure AD)

Začněte s zjednodušeným přihlášením uživatelů nastavením jednotného přihlašování (SSO) pro aplikaci, kterou jste přidali do svého tenanta Azure Active Directory (Azure AD). Když nastavíte jednotné přihlašování, uživatelé se budou moct přihlašovat k aplikaci pomocí svých přihlašovacích údajů Azure AD. Jednotné přihlašování je součástí bezplatné edice služby Azure AD.

Další informace o jednotném přihlašování založeném na OIDC najdete v tématu [Principy jednotného přihlašování](configure-oidc-single-sign-on.md)založeného na OIDC.

## <a name="prerequisites"></a>Předpoklady

K nastavení jednotného přihlašování pro aplikaci, kterou jste přidali do tenanta Azure AD, budete potřebovat:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z následujících rolí: globální správce, správce cloudové aplikace, správce aplikace nebo vlastník instančního objektu.
- Aplikace, která podporuje jednotné přihlašování a která už byla předem nakonfigurovaná a přidaná do galerie Azure AD. Většina aplikací může používat službu Azure AD pro jednotné přihlašování. Aplikace v galerii Azure AD jsou předem nakonfigurované. Pokud vaše aplikace není uvedená nebo se jedná o aplikaci vytvořenou jako aplikace, můžete ji dál používat s Azure AD. Projděte si kurzy a další dokumentaci v obsahu. Tento rychlý Start se zaměřuje na aplikace, které byly předem nakonfigurované pro jednotné přihlašování a které vývojáři aplikací přidají do galerie Azure AD.
- Volitelné: dokončování [zobrazení vašich aplikací](view-applications-portal.md)
- Volitelné: dokončení [Přidání aplikace](add-application-portal.md)
- Volitelné: dokončení [Konfigurace aplikace](add-application-portal-configure.md)
- Volitelné: dokončování [přiřazování uživatelů k aplikaci](add-application-portal-assign-users.md).

>[!IMPORTANT]
>K otestování kroků v tomto rychlém startu použijte neprodukční prostředí.

## <a name="enable-single-sign-on-for-an-app"></a>Povolení jednotného přihlašování pro aplikaci

Když přidáte aplikaci, která používá standard OIDC pro jednotné přihlašování, budete mít tlačítko pro instalaci. Když vyberete tlačítko, přejdete na web aplikace a dokončíte proces registrace aplikace. Proces přidávání aplikace je popsaný v části rychlý Start aplikace přidat dříve v této sérii. Pokud konfigurujete aplikaci, která již byla přidána, podívejte se do prvního rychlého startu. Provede vás zobrazením aplikací, které už ve vašem tenantovi máte. 

Nastavení jednotného přihlašování pro aplikaci:

1. V rychlém startu dřív v této sérii jste zjistili, jak přidat aplikaci, která bude používat vašeho tenanta Azure AD pro správu identit. Pokud vývojář aplikace pro implementaci jednotného přihlašování použil Standard OIDC, zobrazí se vám při přidávání aplikace tlačítko pro registraci. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png" alt-text="Snímek obrazovky se zobrazí možnost jednotného přihlašování a přihlašovací tlačítko." lightbox="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png":::


2. Vyberte možnost **zaregistrovat** se a budete přihlášeni na přihlašovací stránku vývojářů aplikací. Přihlaste se pomocí Azure Active Directory přihlašovací údaje pro přihlášení. 

   > [!IMPORTANT]
    > Pokud již máte předplatné aplikace, pak dojde k ověření podrobností uživatele a informace o tenantovi/adresáři. Pokud aplikace není schopna ověřit uživatele, bude přesměrována k registraci do služby Application Service nebo na chybovou stránku.

3. Po úspěšném ověření se zobrazí dialogové okno s výzvou k vyjádření souhlasu správce. Vyberte **souhlas jménem vaší organizace** a pak vyberte **přijmout**. Další informace o souhlasu uživatele a správce najdete v tématu [vysvětlení souhlasu uživatele a správce](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/consent.png" alt-text="Snímek obrazovky s informacemi o souhlasu aplikace" lightbox="media/add-application-portal-setup-oidc-sso/consent.png":::

4. Aplikace se přidá do vašeho tenanta a zobrazí se Domovská stránka aplikace.


> [!TIP]
> Správu aplikací můžete automatizovat pomocí Graph API, přečtěte si téma [Automatizace správy aplikací pomocí rozhraní Microsoft Graph API](/graph/application-saml-sso-configure-api).

Tady je video s dalšími podrobnostmi o přidání aplikace založené na OIDC do služby Azure AD.

Přidání aplikace založené na OIDC v Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s touto řadou rychlý Start hotovi, zvažte odstranění aplikace a vyčištění svého testovacího tenanta. Odstranění aplikace se zabývá posledním rychlým startem v této sérii, najdete v tématu [odstranění aplikace](delete-application-portal.md).

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku, kde se dozvíte, jak odstranit aplikaci.
> [!div class="nextstepaction"]
> [Odstranění aplikace](delete-application-portal.md)
