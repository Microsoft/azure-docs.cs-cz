---
title: Začínáme integrace služby Azure AD s aplikacemi | Dokumentace Microsoftu
description: Tento článek je příručka Začínáme pro integraci služby Azure Active Directory (AD) s místními aplikacemi a cloudovými aplikacemi.
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
ms.date: 07/16/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 97404421a48d15339fdecf23a951a64d975da577
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090332"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Integrace Azure Active Directory s aplikacemi získávání Příručka Začínáme
## <a name="overview"></a>Přehled
Toto téma je určeno k poskytují návod pro integrace aplikací s Azure Active Directory (AD). Každé z níže uvedených částech najdete obsahovat stručný popis podrobnější tématu, takže můžete identifikovat, které části Tato příručka Začínáme jsou pro vás relevantní.  Pomocí odkazů podrobněji analyzovat na každý předmět.

## <a name="before-you-begin-take-inventory"></a>Než začnete, udělejte inventáře
Než budete přecházet k integrace aplikací s Azure AD, je důležité vědět, kde jsou a kde chcete přejít.  Můžete uvažovat o integraci projektu aplikace Azure AD jsou určeny na následující otázky.

### <a name="application-inventory"></a>Inventář aplikací
* Kde jsou všechny vaše aplikace? Kdo je vlastníkem je?
* Jaký typ ověřování vaše aplikace vyžaduje?
* Kdo potřebuje přístup k aplikacím, které?
* Chcete nasadit nové aplikace?
  * Bude sestavení interní a nasadit virtuální počítač na instanci Azure compute?
  * Budete používat ten, který je k dispozici v galerii aplikací Azure?

### <a name="user-and-group-inventory"></a>Inventář uživatele a skupiny
* Kde jsou umístěny uživatelské účty?
  * Místní služby Active Directory
  * Azure AD
  * V databázi samostatné aplikace, které vlastníte
  * Neschválené aplikace
  * Všechny výš uvedené
* Jaká oprávnění a přiřazení rolí jednotliví uživatelé nyní mají? Je potřeba zkontrolovat přístup nebo jste si jisti, že přiřazení přístup a role uživatele jsou vhodné nyní?
* Jsou skupiny už navázalo ve vašem místním Active Directory?
  * Jak jsou uspořádány skupin?
  * Kdo jsou členové skupiny?
  * Jaká oprávnění a role přiřazení skupiny aktuálně mají?
* Budete potřebovat k vyčištění uživatele nebo skupiny databází před integrací?  (To je poměrně důležitá otázka. Uvolňování paměti v uvolňování paměti navýšení kapacity.)

### <a name="access-management-inventory"></a>Inventář správy přístupu
* Jak můžete nyní spravovat přístup uživatelů k aplikacím? Potřebuje, chcete-li změnit?  Jste za další možnosti pro správu přístupu, například s [RBAC](../../role-based-access-control/role-assignments-portal.md) například?
* Kdo potřebuje přístup k co?

Možná nemáte odpovědi na všechny tyto otázky týkající se ještě před zahájením, ale to je v pořádku.  Tato příručka vám umožňují získali odpovědi na tyto otázky a umožňují přijímat informovaná rozhodnutí.

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure a adresář služby Azure Active Directory.  Pokud ještě nemáte předplatné Azure, můžete vyzkoušet Azure zdarma po dobu 30 dnů. [Vyzkoušejte si to!](https://azure.microsoft.com/trial/get-started-active-directory/)

## <a name="application-integration-with-azure-ad"></a>Integrace aplikací s Azure AD
### <a name="finding-unsanctioned-cloud-applications-with-cloud-discovery"></a>Zjištění neschválené cloudové aplikace s Cloud Discovery
Jak je uvedeno výše, může být aplikace, které nebyly byla spravována vaší organizací až doteď.  Jako součást procesu inventáře je možné najít neschválené cloudové aplikace. Zobrazit [nastavení Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

### <a name="authentication-types"></a>Typy ověřování
Každý z vašich aplikací může mít požadavky na různé ověřování. S Azure AD podpisové certifikáty je možné s aplikacemi, které používají protokol SAML 2.0, WS-Federation nebo OpenID Connect protokoly, stejně jako heslo Single Sign On. Další informace o aplikaci typy ověřování pro použití se službou Azure AD najdete v části [Správa certifikátů pro federované jednotné přihlašování v Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) a [jednotného přihlašování na základě heslo](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Povolení jednotného přihlašování s Proxy aplikací Azure AD
S Microsoft Azure AD Application Proxy můžete poskytnout přístup k aplikacím umístěným uvnitř vaši privátní síť bezpečněji odkudkoli a na libovolném zařízení. Po instalaci konektoru proxy aplikace ve vašem prostředí, se lze snadno nastavit pomocí Azure AD.

### <a name="integrating-applications-with-azure-ad"></a>Integrace aplikací s Azure AD
Následující články popisují různé způsoby, jak integrovat se službou Azure AD aplikace a obsahuje základní pokyny.

* [Určení, které služba Active Directory použít](../fundamentals/active-directory-administer.md)
* [Používání aplikací v galerii aplikací Azure](what-is-single-sign-on.md)
* [Integrace seznam kurzy aplikací SaaS](../saas-apps/tutorial-list.md)

## <a name="managing-access-to-applications"></a>Správa přístupu k aplikacím
Následující články popisují způsoby, jak můžete spravovat přístup k aplikacím, jakmile bude se integrovat s Azure AD pomocí konektorů služby Azure AD a Azure AD.

* [Správa přístupu k aplikacím pomocí služby Azure AD](what-is-access-management.md)
* [Automatizace s využitím konektorů služby Azure AD](../active-directory-saas-app-provisioning.md)
* [Přiřazování uživatelů k aplikaci](../active-directory-applications-guiding-developers-assigning-users.md)
* [Přiřazování skupin k aplikaci](../active-directory-applications-guiding-developers-assigning-groups.md)
* [Sdílení účtů](../active-directory-sharing-accounts.md)

## <a name="integrating-custom-applications"></a>Integrace vlastních aplikací
Pokud vytváříte novou aplikaci a chcete, abychom vývojářům v využívající efektivitu Azure AD, najdete v článku [směrných vývojáři](../active-directory-applications-guiding-developers-for-lob-applications.md).

Pokud chcete přidat vlastní aplikaci do Galerie aplikací Azure, přečtěte si téma ["Přineste si vlastní aplikaci" s Azure AD samoobslužný SAML konfigurací](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="see-also"></a>Další informace najdete v tématech
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](../active-directory-apps-index.md)

