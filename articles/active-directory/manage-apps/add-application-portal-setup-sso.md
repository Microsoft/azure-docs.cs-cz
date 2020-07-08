---
title: 'Rychlý Start: nastavení jednotného přihlašování (SSO) pro aplikaci ve vašem tenantovi Azure Active Directory (Azure AD)'
description: Tento rychlý Start vás provede procesem nastavení jednotného přihlašování (SSO) pro aplikaci ve vašem tenantovi Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: f90edf525a8deb35376d6637090e7b424b2fd658
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86038986"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Rychlý Start: nastavení jednotného přihlašování (SSO) pro aplikaci ve vašem tenantovi Azure Active Directory (Azure AD)

Začněte s zjednodušeným přihlášením uživatelů, a to nastavením jednotného přihlašování pro aplikaci, kterou jste přidali do svého tenanta Azure AD. Po nastavení jednotného přihlašování se uživatelé budou moct přihlašovat do aplikace pomocí svých přihlašovacích údajů Azure AD. Jednotné přihlašování je součástí bezplatné edice služby Azure AD.

## <a name="prerequisites"></a>Požadavky

K nastavení jednotného přihlašování pro aplikaci, kterou jste přidali do svého tenanta Azure AD, budete potřebovat:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z následujících rolí: globální správce, správce cloudové aplikace, správce aplikace nebo vlastník instančního objektu.
- Aplikace, která podporuje jednotné přihlašování a která je už předem nakonfigurovaná a přidaná do galerie Azure AD. Většina aplikací může používat službu Azure AD pro jednotné přihlašování. Aplikace v galerii Azure AD jsou předem nakonfigurované. Pokud vaše aplikace není v seznamu uvedená nebo se jedná o vlastní vyvinutou aplikaci, můžete ji dál používat s Azure AD. Projděte si kurzy a další dokumentaci v obsahu. Tento rychlý Start se zaměřuje na aplikace, které jsou předem nakonfigurované pro jednotné přihlašování a které vývojáři aplikací přidají do galerie Azure AD.
- (Volitelné: dokončování [zobrazení vašich aplikací](view-applications-portal.md))
- (Volitelné: dokončení [Přidání aplikace](add-application-portal.md))
- (Volitelné: dokončení [Konfigurace aplikace](add-application-portal-configure.md)).


>[!IMPORTANT]
>K otestování kroků v tomto rychlém startu doporučujeme použít jiné než produkční prostředí.


## <a name="enable-single-sign-on-for-an-app"></a>Povolení jednotného přihlašování pro aplikaci

Až dokončíte přidávání aplikace do tenanta služby Azure AD, hned se vám zobrazí stránka s přehledem. Pokud konfigurujete aplikaci, která již byla přidána, potom se podívejte na první rychlý Start a provede vás zobrazením aplikací přidaných do tenanta. 

Nastavení jednotného přihlašování pro aplikaci:

1. Na portálu Azure AD vyberte **podnikové aplikace** a pak vyhledejte a vyberte aplikaci, kterou chcete nastavit pro jednotné přihlašování.
2. V části Spravovat vyberte **jednotné přihlašování** a otevřete podokno vlastnosti pro úpravy.
    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Zobrazuje konfigurační stránku jednotného přihlašování na portálu Azure AD.":::
3. Vyberte SAML a otevřete stránku konfigurace jednotného přihlašování. V tomto příkladu je aplikace, kterou konfigurujeme pro jednotné přihlašování, GitHub. Po nastavení GitHubu se uživatelé budou moct přihlašovat k GitHubu pomocí svých přihlašovacích údajů z vašeho tenanta Azure AD.
    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Zobrazuje konfigurační stránku jednotného přihlašování na GitHubu.":::
4. Proces konfigurace aplikace pro použití Azure AD pro jednotné přihlašování založené na SAML se liší v závislosti na aplikaci. Všimněte si, že existuje odkaz na pokyny pro GitHub. Příručky k ostatním aplikacím najdete na adrese:https://docs.microsoft.com/azure/active-directory/saas-apps/
5. Postupujte podle pokynů průvodce pro nastavení jednotného přihlašování pro aplikaci. Mnoho aplikací má specifické požadavky na odběr pro funkce jednotného přihlašování. Například GitHub vyžaduje předplatné Enterprise.
    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Zobrazuje možnost jednotného přihlašování v podnikovém předplatném na stránce s cenami na webu GitHub.":::


## <a name="next-steps"></a>Další kroky

- [Odstranění aplikace](delete-application-portal.md)
