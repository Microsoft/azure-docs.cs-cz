---
title: Začínáme s integrací Azure AD s aplikacemi
description: Tento článek je úvodní příručka pro integraci Azure Active Directory (AD) s místními aplikacemi a cloudových aplikací.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: db3d3623e175d582a2fe271d73aa452ca07b8e8d
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735062"
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
* Budete potřebovat vyčistit databáze uživatelů a skupin před integrací?  (Jedná se o poměrně důležitou otázku. Uvolnění paměti, uvolnění paměti.)

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

### <a name="authentication-types"></a>Typy ověřování
Každá z vašich aplikací může mít různé požadavky na ověřování. Pomocí služby Azure AD je možné podpisové certifikáty použít s aplikacemi, které používají protokoly SAML 2,0, WS-Federation nebo OpenID Connect, a také jednotné přihlašování pomocí hesla. Další informace o typech ověřování aplikací pro použití s Azure AD najdete v tématu [Správa certifikátů pro federované jednotné Sign-On v rámci Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) a [jednotného přihlašování založené na heslech](what-is-single-sign-on.md).

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

Poskytněte prosím svůj názor na plány nasazení prostřednictvím [průzkumu plánu nasazení](https://aka.ms/DeploymentPlanFeedback).