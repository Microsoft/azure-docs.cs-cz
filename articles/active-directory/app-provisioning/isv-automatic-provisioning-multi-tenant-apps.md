---
title: Povolení automatického zřizování uživatelů pro víceklientské aplikace – Azure AD
description: Průvodce pro nezávislé dodavatele softwaru pro povolení automatického zřizování
services: active-directory
documentationcenter: azure
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia
ms.collection: active-directory
ms.openlocfilehash: 93e1d879f69a95fe7472ce530e0e9f38f3480f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522389"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Povolení automatického zřizování uživatelů pro víceklientské aplikace

Automatické zřizování uživatelů je proces automatizace vytváření, údržby a odstraňování identit uživatelů v cílových systémech, jako jsou aplikace softwaru jako služby.

## <a name="why-enable-automatic-user-provisioning"></a>Proč povolit automatické zřizování uživatelů?

Aplikace, které vyžadují, aby byl v aplikaci přítomen záznam uživatele před prvním přihlášením uživatele, vyžadují zřizování uživatele. Existují výhody pro vás jako poskytovatele služeb a výhody pro vaše zákazníky.

### <a name="benefits-to-you-as-the-service-provider"></a>Výhody pro vás jako poskytovatele služeb

* Zvyšte zabezpečení aplikace pomocí platformy microsoft identity.

* Snižte skutečné a vnímané úsilí zákazníků o přijetí vaší aplikace.

* Snižte náklady při integraci s více poskytovateli identit (IdPs) pro automatické zřizování uživatelů pomocí system for-domain identity management (SCIM) založené zřizování.

* Snižte náklady na podporu tím, že poskytnete rozšířené protokoly, které zákazníkům pomohou řešit problémy s zřizováním uživatelů.

* Zvyšte viditelnost vaší aplikace v [galerii aplikací Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps).

* Získejte seznam s prioritou na stránce Kurzy aplikací.

### <a name="benefits-to-your-customers"></a>Výhody pro vaše zákazníky

* Zvyšte zabezpečení automatickým odebráním přístupu k aplikaci pro uživatele, kteří změní role nebo ponechají organizaci vaší aplikaci.

* Zjednodušte správu uživatelů pro vaši aplikaci tím, že se vyhnete lidské chybě a opakované práci spojené s ručním zřizováním.

* Snižte náklady na hostování a údržbu vlastních zřizovacích řešení.

## <a name="choose-a-provisioning-method"></a>Výběr metody zřizování

Azure AD poskytuje několik integračních cest, které umožňují automatické zřizování uživatelů pro vaši aplikaci.

* [Služba zřizování Azure AD](../app-provisioning/user-provisioning.md) spravuje zřizování a zrušení zřizování uživatelů z Azure AD do vaší aplikace (odchozí zřizování) a z vaší aplikace do Azure AD (příchozí zřizování). Služba se připojuje ke koncovým bodům rozhraní API rozhraní API pro správu identit mezi doménami (SCIM), které poskytuje vaše aplikace.

* Při použití [Microsoft Graphu](https://docs.microsoft.com/graph/)spravuje vaše aplikace příchozí a odchozí zřizování uživatelů a skupin z Azure AD do vaší aplikace dotazem na rozhraní Microsoft Graph API.

* Zřizování uživatele assertion markup language Just in Time (SAML JIT) lze povolit, pokud vaše aplikace používá saml pro federaci. Používá informace o deklaracích odeslané v tokenu SAML pro zřizování uživatelů.

Chcete-li zjistit, kterou možnost integrace použít pro vaši aplikaci, naleznete v tabulce porovnání vysoké úrovně a pak se podívejte na podrobnější informace o jednotlivých možnostech.

| Funkce povolené nebo rozšířené pomocí automatického zřizování| Služba zřizování Azure AD (SCIM 2.0)| Rozhraní Microsoft Graph API (OData v4.0)| SAML JIT |
|---|---|---|---|
| Správa uživatelů a skupin ve službě Azure AD| √| √| Pouze uživatel |
| Správa uživatelů a skupin synchronizovaných z místní služby Active Directory| √*| √*| Pouze uživatel* |
| Přístup k datům mimo uživatele a skupiny během zřizování přístupu k datům O365 (týmy, SharePoint, e-mail, kalendář, dokumenty atd.)| X+| √| × |
| Vytváření, čtení a aktualizace uživatelů na základě obchodních pravidel| √| √| √ |
| Odstranění uživatelů na základě obchodních pravidel| √| √| × |
| Správa automatického zřizování uživatelů pro všechny aplikace z webu Azure Portal| √| ×| √ |
| Podpora více poskytovatelů identit| √| ×| √ |
| Podpora účtů hostů (B2B)| √| √| √ |
| Podpora účtů mimo podniky (B2C)| ×| √| √ |

<sup>*</sup>– Nastavení Azure AD Connect je potřeba k synchronizaci uživatelů ze služby AD do služby Azure AD.  
<sup>+</sup >– Použití SCIM pro zřizování nebrání integraci vaší aplikace s MIcrosoft Graph pro jiné účely.

## <a name="azure-ad-provisioning-service-scim"></a>Služba zřizování Azure AD (SCIM)

Zřizovací služby Azure AD používá [SCIM](https://aka.ms/SCIMOverview), oborový standard pro zřizování podporované mnoha zprostředkovateli identit (IdPs) a také aplikace (např. Doporučujeme použít službu zřizování Azure AD, pokud chcete podporovat idps kromě Azure AD, jako všechny SCIM kompatibilní IdP můžete připojit k koncovému bodu SCIM. Vytvoření jednoduché /User koncový bod, můžete povolit zřizování bez nutnosti udržovat vlastní synchronizační modul. 

Další informace o tom, jak uživatelé služby Azure AD Provisioning Service SCIM, najdete v tématu: 

* [Další informace o standardu SCIM](https://aka.ms/SCIMOverview)

* [Použití systému pro správu identit mezi doménami (SCIM) k automatickému zřizování uživatelů a skupin z Azure Active Directory do aplikací](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Principy implementace Azure AD SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>Microsoft Graph pro zřizování

Při použití Microsoft Graph pro zřizování, máte přístup ke všem bohatým uživatelským datům dostupným v graphu. Kromě podrobností o uživatelích a skupinách můžete také načíst další informace, jako jsou role uživatele, manažerské a přímé sestavy, vlastněná a registrovaná zařízení a stovky dalších datových částí dostupných v [aplikaci Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0). 

Více než 15 milionů organizací a 90 % 500 společností s majetkem využívá Azure AD při přihlášení ke cloudovým službám Microsoftu, jako jsou Office 365, Microsoft Azure, Enterprise Mobility Suite nebo Microsoft 365. Pomocí Microsoft Graphu můžete aplikaci integrovat s pracovními postupy pro správu, jako je registrace zaměstnanců (a ukončení), údržba profilu a další. 

Další informace o používání Microsoft Graphu pro zřizování:

* [Domovská stránka aplikace Microsoft Graph](https://developer.microsoft.com/graph)

* [Přehled Microsoft Graphu](https://docs.microsoft.com/graph/overview)

* [Přehled auth aplikace Microsoft Graph](https://docs.microsoft.com/graph/auth/)

* [Začínáme s Microsoft Graphem](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>Použití SAML JIT pro zřizování

Pokud chcete zřídit uživatele pouze při prvním přihlášení k aplikaci a není nutné automaticky odřazování uživatelů, SAML JIT je možnost. Vaše aplikace musí podporovat SAML 2.0 jako federační protokol pro použití SAML JIT.

SAML JIT používá informace o deklaracích identity v tokenu SAML k vytvoření a aktualizaci informací o uživateli v aplikaci. Zákazníci mohou nakonfigurovat tyto požadované deklarace identity v aplikaci Azure AD podle potřeby. Někdy jit zřizování musí být povolena ze strany aplikace tak, aby zákazník můžete použít tuto funkci. SAML JIT je užitečné pro vytváření a aktualizaci uživatelů, ale nelze odstranit nebo deaktivovat uživatele v aplikaci.

## <a name="next-steps"></a>Další kroky

* [Povolení jednotného přihlášení pro vaši aplikaci](../manage-apps/isv-sso-content.md)

* [Odešlete výpis aplikace](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) a staňte se partnerem společnosti Microsoft a vytvořte dokumentaci na webu společnosti Microsoft.

* [Připojte se k programu Microsoft Partner Network (zdarma) a vytvořte si plán pro uvedení na trh](https://partner.microsoft.com/en-us/explore/commercial).
