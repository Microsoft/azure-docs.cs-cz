---
title: Povolení automatického zřizování uživatelů pro aplikace s více klienty – Azure AD
description: Průvodce pro nezávislé výrobce softwaru pro povolení automatického zřizování
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/23/2019
ms.author: kenwith
ms.reviewer: zhchia
ms.openlocfilehash: 7bd0fc634109beb6cc674d89f56666e7035d33ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255691"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Povolení automatického zřizování uživatelů pro aplikaci s více klienty

Automatické zřizování uživatelů je proces automatizace vytváření, údržby a odebírání identit uživatelů v cílových systémech, jako jsou aplikace typu software jako služba.

## <a name="why-enable-automatic-user-provisioning"></a>Proč povolit Automatické zřizování uživatelů?

Aplikace, které vyžadují, aby se v aplikaci nachází záznam uživatele před prvním přihlášením uživatele vyžaduje zřízení uživatele. Pro vás jako poskytovatel služeb a výhody pro vaše zákazníky nabízíme výhody.

### <a name="benefits-to-you-as-the-service-provider"></a>Výhody jako poskytovatel služeb

* Zvyšte zabezpečení aplikace pomocí platformy Microsoft identity.

* Pro přijetí vaší aplikace omezte skutečná a zjištěná úsilí zákazníka.

* Snižte náklady v integraci s více zprostředkovateli identity (zprostředkovatelů identity) pro Automatické zřizování uživatelů pomocí systému pro zřizování založené na SCIM (pro správu identit mezi doménami).

* Snižte náklady na podporu poskytováním bohatých protokolů, které zákazníkům pomohou při řešení potíží se zřizováním uživatelů.

* Zvyšte viditelnost své aplikace v [galerii aplikací Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps).

* Seznamte se s určením priorit na stránce kurzy aplikací.

### <a name="benefits-to-your-customers"></a>Výhody pro vaše zákazníky

* Zvyšte zabezpečení tím, že automaticky odeberete přístup k aplikaci pro uživatele, kteří mění role nebo odejdou organizaci do vaší aplikace.

* Zjednodušte správu uživatelů pro svou aplikaci tím, že vyloučíte lidskou chybu a opakující se práci související s ručním zřizováním.

* Snižte náklady na hostování a údržbu řešení pro zřizování s vlastním vývojem.

## <a name="choose-a-provisioning-method"></a>Výběr metody zřizování

Azure AD poskytuje několik integračních cest, které umožňují Automatické zřizování uživatelů pro vaši aplikaci.

* [Služba zřizování Azure AD](../app-provisioning/user-provisioning.md) spravuje zřizování a rušení zřizování uživatelů ze služby Azure AD do vaší aplikace (odchozí zřizování) a z vaší aplikace do Azure AD (příchozí zřizování). Služba se připojuje k systému pro koncové body rozhraní API pro správu uživatelů mezi doménami (SCIM), které poskytuje vaše aplikace.

* Při použití [Microsoft Graph](/graph/)vaše aplikace spravuje příchozí a odchozí zřizování uživatelů a skupin ze služby Azure AD do vaší aplikace pomocí dotazování rozhraní API Microsoft Graph.

* Pokud vaše aplikace používá pro federaci federačního rozhraní SAML, může být povoleno zřizování uživatelů v čase (SAML JIT) Security Assertion Markup Language. Pomocí informací o deklaracích odeslaných v tokenu SAML zřídí uživatele.

Pokud chcete zjistit, která možnost integrace se má použít pro vaši aplikaci, přečtěte si referenční tabulku vysoké úrovně a podívejte se na podrobnější informace o jednotlivých možnostech.

| Funkce, které jsou povolené nebo rozšířené pomocí automatického zřizování| Služba zřizování Azure AD (SCIM 2,0)| Rozhraní API pro Microsoft Graph (OData v 4.0)| JIT SAML |
|---|---|---|---|
| Správa uživatelů a skupin ve službě Azure AD| √| √| Pouze uživatel |
| Správa uživatelů a skupin synchronizovaných z místní služby Active Directory| √*| √*| Jenom uživatel * |
| Přístup k datům mimo uživatele a skupiny během zřizování přístupu k Microsoft 365 dat (týmy, SharePoint, E-mail, kalendář, dokumenty atd.)| X +| √| × |
| Vytváření, čtení a aktualizace uživatelů na základě obchodních pravidel| √| √| √ |
| Odstranění uživatelů na základě obchodních pravidel| √| √| × |
| Správa automatického zřizování uživatelů pro všechny aplikace z Azure Portal| √| ×| √ |
| Podpora více zprostředkovatelů identity| √| ×| √ |
| Podpora účtů hostů (B2B)| √| √| √ |
| Podpora účtů mimo podnik (B2C)| ×| √| √ |

<sup>*</sup> – Azure AD Connect instalační program je nutný k synchronizaci uživatelů ze služby AD do Azure AD.  
<sup>+</sup >– Použití SCIM pro zřizování vám nevylučuje integraci aplikace s Microsoft Graph pro jiné účely.

## <a name="azure-ad-provisioning-service-scim"></a>Služba zřizování Azure AD (SCIM)

Služba zřizování Azure AD používá [SCIM](https://aka.ms/SCIMOverview), což je standardní obor pro zřizování podporovaný mnoha zprostředkovateli identity (zprostředkovatelů identity) a také aplikacemi (například časová rezerva, g Suite, Dropbox). Doporučujeme použít službu zřizování Azure AD, pokud chcete kromě služby Azure AD podporovat zprostředkovatelů identity, protože libovolný IdP kompatibilní s SCIM se může připojit ke svému koncovému bodu SCIM. Vytvořením jednoduchého koncového bodu/User můžete povolit zřizování bez nutnosti udržovat vlastní synchronizační modul. 

Další informace o tom, jak se uživatelé služby zřizování Azure AD SCIM, najdete v těchto tématech: 

* [Další informace o standardu SCIM](https://aka.ms/SCIMOverview)

* [Automatické zřizování uživatelů a skupin z Azure Active Directory k aplikacím pomocí systému pro správu identit mezi doménami (SCIM)](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Porozumění implementaci Azure AD SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>Microsoft Graph pro zřizování

Při použití Microsoft Graph ke zřízení máte přístup ke všem uživatelům, kteří jsou k dispozici v grafu. Kromě podrobností o uživatelích a skupinách můžete také načítat Další informace, jako jsou role uživatelů, vedoucí a přímé sestavy, vlastněná a registrovaná zařízení a stovky dalších datových částí, které jsou k dispozici v [Microsoft Graph](/graph/api/overview). 

Při přihlášení k odběru cloudových služeb Microsoftu, jako je Microsoft 365, Microsoft Azure nebo sada Enterprise mobility, se za víc než 15 000 000 organizací a 90% z Fortune 500 společnosti používá Azure AD. Pomocí Microsoft Graph můžete integrovat svou aplikaci s pracovními postupy pro správu, jako je například registrace zaměstnanců (a ukončení), údržba profilu a další. 

Další informace o použití Microsoft Graph pro zřizování:

* [Microsoft Graph domovskou stránku](https://developer.microsoft.com/graph)

* [Přehled Microsoft Graphu](/graph/overview)

* [Přehled ověřování Microsoft Graph](/graph/auth/)

* [Začínáme s Microsoft Graph](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>Používání JIT pro zřizování pomocí jazyka SAML

Pokud chcete uživatelům zřídit jenom při prvním přihlášení k vaší aplikaci a nemusíte automaticky zrušit zřízení uživatelů, SAML JIT je možnost. Vaše aplikace musí podporovat SAML 2,0 jako federační protokol pro použití JIT SAML.

Kompilátor SAML používá informace o deklaracích identity v tokenu SAML k vytvoření a aktualizaci informací o uživateli v aplikaci. Zákazníci můžou podle potřeby nakonfigurovat tyto požadované deklarace identity v aplikaci Azure AD. V některých případech je potřeba zřizování JIT povolit ze strany aplikace, aby zákazník mohl používat tuto funkci. Kompilátor SAML JIT je vhodný pro vytváření a aktualizaci uživatelů, ale nemůže uživatele v aplikaci odstranit ani deaktivovat.

## <a name="next-steps"></a>Další kroky

* [Povolení jednotného přihlašování pro aplikaci](../develop/v2-howto-app-gallery-listing.md)

* [Odesláním seznamu aplikací](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) a partnera s Microsoftem můžete vytvořit dokumentaci na webu Microsoftu.

* [Připojte se k Microsoft Partner Network (zdarma) a vytvořte svůj přejít na trh](https://partner.microsoft.com/explore/commercial).
