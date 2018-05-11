---
title: Začínáme integrace s aplikacemi Azure AD | Microsoft Docs
description: Tento článek je příručka Začínáme k integraci Azure Active Directory (AD) s místními aplikacemi a cloudových aplikací.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 523508bc2caeb4315caa6597662174aa0fdeb47b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Integrace služby Azure Active Directory s aplikacemi, získávání Příručka Začínáme
## <a name="overview"></a>Přehled
Toto téma je určeno tak, abyste získali plán pro integrace aplikací s Azure Active Directory (AD). Každý z níže uvedených částech obsahují stručné souhrnné informace o podrobnější téma, abyste mohli identifikovat, které části Tato příručka Začínáme se vás týkají.  Odkazy na podrobnější prohlídku podle jednotlivých subjektu.

## <a name="before-you-begin-take-inventory"></a>Než začnete, udělejte inventáře
Předtím, než můžete přejít v integrace aplikací s Azure AD, je důležité vědět, kde jsou a kde chcete přejít.  Tyto otázky, které jsou určeny k můžete vezměte v úvahu projektu integrace aplikace Azure AD.

### <a name="application-inventory"></a>Inventář aplikací
* Kde jsou všechny aplikace? Kdo je vlastníkem je?
* Jaký typ ověřování, vaše aplikace vyžadují?
* Potřebuje přístup k aplikacím, které?
* Opravdu chcete nasadit novou aplikaci?
  * Bude sestavení interních a nasadit ji v instanci Azure výpočetní?
  * Použijete jeden, který je k dispozici v galerii aplikací Azure?

### <a name="user-and-group-inventory"></a>Soupis uživatelů a skupin
* Kde jsou umístěny uživatelské účty?
  * Místní služby Active Directory
  * Azure AD
  * V databázi samostatné aplikace, která vlastníte
  * V neschválené aplikace
  * Všechny výše uvedené
* Jaké oprávnění a přiřazení rolí jednotlivých aktuálně mají uživatelé? Potřebujete zkontrolovat jejich přístup nebo jste si jisti, že vaše přiřazení přístupu a roli uživatele jsou vhodné nyní?
* Jsou skupiny už vytvořené v místní Active Directory?
  * Jak jsou uspořádány skupin?
  * Kteří jsou členy skupiny?
  * Jaké oprávnění nebo roli přiřazení skupiny aktuálně mají?
* Budete potřebovat k vyčištění uživatele nebo skupiny databází před integrací?  (Toto je poměrně důležité otázku. Uvolňování paměti v paměti se.)

### <a name="access-management-inventory"></a>Správa inventáře přístup
* Jak je aktuálně spravovat přístup uživatelů k aplikacím? Potřebuje, chcete-li změnit?  Můžete udělat jiné způsoby správy přístupu, například s [RBAC](../../role-based-access-control/role-assignments-portal.md) například?
* Kdo potřebuje přístup k co?

Možná nemáte odpovědi na všechny tyto otázky předem, ale to nevadí.  Tento průvodce vám může pomoct odpovědět na některé z těchto dotazů a některé informovanému rozhodnutí.

## <a name="prerequisites"></a>Požadované součásti
* Předplatné Azure a adresář služby Azure Active Directory.  Pokud nemáte předplatné Azure, můžete vyzkoušet Azure zdarma po dobu 30 dnů. [Vyzkoušejte si to!](https://azure.microsoft.com/trial/get-started-active-directory/)

## <a name="application-integration-with-azure-ad"></a>Integrace aplikací s Azure AD
### <a name="finding-unsanctioned-cloud-applications-with-cloud-app-discovery"></a>Vyhledání aplikace neschválená cloudových aplikací s Cloud App Discovery
Jak je uvedeno nahoře, může být aplikace, které nebyly byla spravovaná vaší organizací až doteď.  Jako součást procesu inventáře je možné najít nedovolené cloudové aplikace. V tématu [hledání nedovolené cloudové aplikace s Cloud App Discovery](cloud-app-discovery.md).

### <a name="authentication-types"></a>Typy ověřování
Každý z vašich aplikací mohou mít různá ověřovací požadavky. S Azure AD podpisové certifikáty lze použít s aplikací, které používají SAML 2.0, WS-Federation, nebo OpenID Connect protokoly, stejně jako heslo jednotné přihlašování. Další informace o aplikaci typy ověřování pro použití s Azure AD najdete v části [Správa certifikátů pro federované jednotné přihlašování v Azure Active Directory](../active-directory-sso-certs.md) a [jednotného přihlašování na založené na heslech](../active-directory-appssoaccess-whatis.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Povolení přihlášení SSO se Proxy aplikace Azure AD
S Microsoft Azure AD Application Proxy může poskytnout přístup k aplikacím umístěným uvnitř vaší privátní sítě bezpečně, z libovolného místa a na jakémkoli zařízení. Po instalaci konektoru proxy aplikace v rámci vašeho prostředí, můžete snadno nastavit s Azure AD.

### <a name="integrating-applications-with-azure-ad"></a>Integrace aplikací s Azure AD
Následující články popisují různé způsoby aplikace integraci s Azure AD a obsahují některé pokyny.

* [Zjišťování služby Active Directory, používat](../active-directory-administer.md)
* [Použití aplikace v galerii aplikací Azure](../active-directory-appssoaccess-whatis.md)
* [Integrace seznam kurzy aplikací SaaS](../active-directory-saas-tutorial-list.md)

## <a name="managing-access-to-applications"></a>Správa přístupu k aplikacím
Následující články popisují způsoby, jak můžete spravovat přístup k aplikacím, jakmile byly integrovány s Azure AD pomocí konektorů Azure AD a Azure AD.

* [Správa přístupu k aplikacím pomocí služby Azure AD](../active-directory-managing-access-to-apps.md)
* [Automatizace pomocí konektorů Azure AD](../active-directory-saas-app-provisioning.md)
* [Přiřazování uživatelů k aplikaci](../active-directory-applications-guiding-developers-assigning-users.md)
* [Přiřazování skupin k aplikaci](../active-directory-applications-guiding-developers-assigning-groups.md)
* [Sdílení účtů](../active-directory-sharing-accounts.md)

## <a name="integrating-custom-applications"></a>Integrace vlastních aplikací
Pokud vytváříte novou aplikaci a chcete pomoci vývojářům využívat možnosti Azure AD, najdete v části [směrných vývojáři](../active-directory-applications-guiding-developers-for-lob-applications.md).

Pokud chcete přidat vlastní aplikace galerii aplikací Azure, najdete v části ["Přineste vlastní aplikace" s konfigurací Azure AD samoobslužného SAML](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="see-also"></a>Další informace najdete v tématech
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](../active-directory-apps-index.md)

