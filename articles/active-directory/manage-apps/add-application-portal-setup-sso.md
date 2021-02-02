---
title: 'Rychlý Start: nastavení jednotného přihlašování založeného na SAML (SSO) pro aplikaci ve vašem tenantovi Azure Active Directory (Azure AD)'
description: Tento rychlý Start vás provede procesem nastavení jednotného přihlašování založeného na SAML (SSO) pro aplikace ve vašem tenantovi Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 3278c9956dd5017572f063059250c97727656923
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99252368"
---
# <a name="quickstart-set-up-saml-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Rychlý Start: nastavení jednotného přihlašování založeného na SAML (SSO) pro aplikaci ve vašem tenantovi Azure Active Directory (Azure AD)

Začněte s zjednodušeným přihlášením uživatelů nastavením jednotného přihlašování (SSO) pro aplikaci, kterou jste přidali do svého tenanta Azure Active Directory (Azure AD). Když nastavíte jednotné přihlašování, uživatelé se budou moct přihlašovat k aplikaci pomocí svých přihlašovacích údajů Azure AD. Jednotné přihlašování je součástí bezplatné edice služby Azure AD.

## <a name="prerequisites"></a>Požadavky

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

Po dokončení přidávání aplikace do tenanta služby Azure AD se zobrazí stránka přehled. Pokud konfigurujete aplikaci, která již byla přidána, podívejte se do prvního rychlého startu. Provede vás zobrazením aplikací přidaných do tenanta. 

Nastavení jednotného přihlašování pro aplikaci:

1. Na portálu Azure AD vyberte **podnikové aplikace**. Pak vyhledejte a vyberte aplikaci, kterou chcete nastavit pro jednotné přihlašování.
1. V části **Spravovat** vyberte **jednotné přihlašování** . otevře se podokno **jednotného přihlašování** pro úpravy.

    > [!IMPORTANT]
    > Pokud aplikace používá standard OpenID Connect (OIDC) pro jednotné přihlašování, neuvidíte v navigaci možnost jednotného přihlašování. Informace o tom, jak je nastavit, najdete v rychlém startu pro jednotné přihlašování založené na OIDC.

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Snímek obrazovky zobrazující konfigurační stránku jednotného přihlašování na portálu Azure AD.":::

1. Vyberte **SAML** a otevřete stránku konfigurace jednotného přihlašování. V tomto příkladu je aplikace, kterou konfigurujeme pro jednotné přihlašování, GitHub. Po nastavení GitHubu se uživatelé můžou přihlásit k GitHubu pomocí svých přihlašovacích údajů z vašeho tenanta Azure AD.

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Snímek obrazovky se zobrazením konfigurační stránky jednotného přihlašování na GitHubu.":::

1. Proces konfigurace aplikace pro použití Azure AD pro jednotné přihlašování založené na SAML se liší v závislosti na aplikaci. K dispozici je odkaz na pokyny pro GitHub. Pokyny pro další aplikace najdete v tématu [kurzy pro integraci SaaSch aplikací s Azure Active Directory](/azure/active-directory/saas-apps/).
1. Postupujte podle pokynů průvodce pro nastavení jednotného přihlašování pro aplikaci. Mnoho aplikací má specifické požadavky na odběr pro funkce jednotného přihlašování. Například GitHub vyžaduje předplatné Enterprise.
    > [!TIP]
    > Další informace o možnostech konfigurace SAML najdete v tématu [Konfigurace jednotného přihlašování založeného na SAML](configure-saml-single-sign-on.md).

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Snímek obrazovky s možností jednotného přihlašování v podnikovém předplatném na stránce s cenami GitHubu.":::

> [!TIP]
> Správu aplikací můžete automatizovat pomocí Graph API, přečtěte si téma [Automatizace správy aplikací pomocí rozhraní Microsoft Graph API](/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s touto řadou rychlý Start hotovi, zvažte odstranění aplikace a vyčištění svého testovacího tenanta. Odstranění aplikace se zabývá posledním rychlým startem v této sérii, najdete v tématu [odstranění aplikace](delete-application-portal.md).

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku, kde se dozvíte, jak odstranit aplikaci.
> [!div class="nextstepaction"]
> [Odstranění aplikace](delete-application-portal.md)