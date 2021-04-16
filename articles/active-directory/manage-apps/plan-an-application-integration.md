---
title: Začínáme s integrací Azure Active Directory s aplikacemi
description: Tento článek je úvodní příručka pro integraci Azure Active Directory (AD) s místními aplikacemi a cloudových aplikací.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2021
ms.author: iangithinji
ms.reviewer: asteen
ms.openlocfilehash: 37916e5e356e7c5ad6e685ac0838363fe2579496
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374977"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Průvodce integrací Azure Active Directory s aplikacemi Začínáme

Toto téma shrnuje proces integrace aplikací s Azure Active Directory (AD). Všechny níže uvedené části obsahují stručný souhrn podrobnějšího přehledu, abyste mohli zjistit, které části této příručky Začínáme jsou pro vás důležité.

Podrobné plány nasazení si můžete stáhnout v části [Další kroky](#next-steps).

## <a name="take-inventory"></a>Přijmout inventář
Před integrací aplikací se službou Azure AD je důležité znát, kde jste a kde chcete přejít.  Následující otázky vám pomůžou představit svůj projekt integrace aplikací Azure AD.

### <a name="application-inventory"></a>Inventář aplikací
* Kde jsou všechny vaše aplikace? Kdo je vlastní?
* Jaký druh ověřování vaše aplikace vyžaduje?
* Kdo potřebuje přístup k jakým aplikacím?
* Chcete nasadit novou aplikaci?
  * Budete ho sestavovat interně a nasazovat ho do výpočetní instance Azure?
  * Použijete ten, který je k dispozici v galerii aplikací Azure?

### <a name="user-and-group-inventory"></a>Inventář uživatelů a skupin
* Kde se nachází vaše uživatelské účty?
  * Místní služby Active Directory
  * Azure AD
  * V samostatné aplikační databázi, kterou vlastníte
  * V neschválených aplikacích
  * Všechny výše uvedené možnosti
* Jaká oprávnění a přiřazení rolí mají jednotliví uživatelé aktuálně k dispozici? Potřebujete si projít svůj přístup nebo jste si jisti, že je teď k disvěřit přístup a přiřazení rolí uživatelů?
* Jsou již vytvořeny skupiny v místní službě Active Directory?
  * Jak jsou vaše skupiny uspořádány?
  * Kteří členy skupiny jsou?
  * Jaká oprávnění a přiřazení rolí dělají skupiny v současné době?
* Budete potřebovat vyčistit databáze uživatelů a skupin před integrací?  (Jedná se o důležitou otázku. Uvolnění paměti, uvolnění paměti.)

### <a name="access-management-inventory"></a>Inventář správy přístupu
* Jak aktuálně spravujete přístup uživatelů k aplikacím? Je potřeba změnit?  Měli jste za přístup k jiným způsobům správy přístupu, jako je například se službou [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) ?
* Kdo potřebuje přístup k čemu?

Možná nemáte odpovědi na všechny tyto otázky dopředu, ale to je v pořádku.  Tato příručka vám pomůže odpovědět na některé z těchto otázek a dělat nějaká kvalifikovaná rozhodnutí.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Vyhledání neschválených cloudových aplikací pomocí Cloud Discovery

Jak je uvedeno výše, můžou existovat aplikace, které nespravuje vaše organizace, až do této chvíle.  V rámci procesu inventarizace je možné najít neschválené cloudové aplikace. Viz [nastavení Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrace aplikací s Azure AD
Následující články popisují různé způsoby integrace aplikací s Azure AD a poskytují některé doprovodné materiály.

* [Určení služby Active Directory, která se má použít](../fundamentals/active-directory-whatis.md)
* [Používání aplikací v galerii aplikací Azure](what-is-single-sign-on.md)
* [Integrace seznamu kurzů pro aplikace SaaS](../saas-apps/tutorial-list.md)

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Možnosti pro aplikace, které nejsou uvedené v galerii Azure AD

Můžete přidat libovolnou aplikaci, která již existuje ve vaší organizaci, nebo jakoukoli aplikaci třetí strany od dodavatele, který ještě není součástí Galerie Azure AD. V závislosti na vaší [licenční smlouvě](https://azure.microsoft.com/pricing/details/active-directory/)jsou k dispozici tyto možnosti:

- Samoobslužná integrace všech aplikací, které podporují zprostředkovatele identity [Security Assertion Markup Language (SAML) 2,0](https://wikipedia.org/wiki/SAML_2.0) (SP-inicied nebo IDP)
- Samoobslužná integrace jakékoli webové aplikace, která má přihlašovací stránku založenou na jazyce HTML pomocí [jednotného přihlašování založeného na heslech](sso-options.md#password-based-sso)
- Samoobslužné připojení aplikací, které používají [systém pro protokol SCIM (Domain Identity Management) pro zřizování uživatelů](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Možnost Přidat odkazy na libovolnou aplikaci ve [Spouštěči aplikací Office 365](https://support.microsoft.com/office/meet-the-microsoft-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) nebo ve [složkách moje aplikace](https://myapplications.microsoft.com/)

Pokud hledáte pokyny pro vývojáře, jak integrovat vlastní aplikace do služby Azure AD, přečtěte si téma [scénáře ověřování pro Azure AD](../develop/authentication-vs-authorization.md). Když vyvíjíte aplikaci, která používá moderní protokol, jako je [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) k ověřování uživatelů, můžete ji zaregistrovat na platformě Microsoft identity pomocí prostředí [Registrace aplikací](../develop/quickstart-register-app.md) v Azure Portal.

### <a name="authentication-types"></a>Typy ověřování
Každá z vašich aplikací může mít různé požadavky na ověřování. Pomocí služby Azure AD je možné podpisové certifikáty použít s aplikacemi, které používají protokol SAML 2,0, WS-Federation nebo OpenID Connect a jednotné přihlašování k heslu. Další informace o typech ověřování aplikací najdete v tématu [Správa certifikátů pro federované jednotné Sign-On v Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) a [jednotné přihlašování založené na heslech](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Povolení jednotného přihlašování s Aplikace Azure AD proxy
Pomocí služby Microsoft Azure AD Application proxy můžete zajistit zabezpečený přístup k aplikacím, které se nacházejí v privátní síti, odkudkoli a na jakémkoli zařízení. Po instalaci konektoru proxy aplikací ve vašem prostředí je možné ho snadno nakonfigurovat pomocí Azure AD.

### <a name="integrating-custom-applications"></a>Integrace vlastních aplikací
Pokud chcete přidat vlastní aplikaci do Galerie aplikací Azure, přečtěte si téma [publikování aplikace v galerii aplikací Azure AD](../develop/v2-howto-app-gallery-listing.md).

## <a name="managing-access-to-applications"></a>Správa přístupu k aplikacím
Následující články popisují způsoby, kterými můžete spravovat přístup k aplikacím po integraci s Azure AD pomocí konektorů Azure AD a Azure AD.

* [Správa přístupu k aplikacím pomocí Azure AD](what-is-access-management.md)
* [Automatizace pomocí konektorů Azure AD](../app-provisioning/user-provisioning.md)
* [Přiřazování uživatelů k aplikaci](./assign-user-or-group-access-portal.md)
* [Přiřazování skupin k aplikaci](./assign-user-or-group-access-portal.md)
* [Sdílení účtů](../enterprise-users/users-sharing-accounts.md)

## <a name="next-steps"></a>Další kroky
Podrobné informace můžete stáhnout Azure Active Directory plány nasazení z [GitHubu](../fundamentals/active-directory-deployment-plans.md). Pro aplikace v galerii si můžete stáhnout plány nasazení pro jednotné přihlašování, podmíněný přístup a zřizování uživatelů prostřednictvím [Azure Portal](https://portal.azure.com).

Stažení plánu nasazení z Azure Portal:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vyberte možnost **podnikové aplikace** vybrat  |    |  **plán nasazení** aplikace.
