---
title: Povolení automatického zřizování uživatelů pro aplikace s více klienty – Azure AD
description: Průvodce pro nezávislé výrobce softwaru pro povolení automatického zřizování
services: active-directory
documentationcenter: azure
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia
ms.collection: active-directory
ms.openlocfilehash: e7f60335b3e75d93f408dec3386ca0bdcd6a0388
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711450"
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

* [Služba zřizování Azure AD](user-provisioning.md) spravuje zřizování a rušení zřizování uživatelů ze služby Azure AD do vaší aplikace (odchozí zřizování) a z vaší aplikace do Azure AD (příchozí zřizování). Služba se připojuje k systému pro koncové body rozhraní API pro správu uživatelů mezi doménami (SCIM), které poskytuje vaše aplikace.

* Při použití [Microsoft Graph](https://docs.microsoft.com/graph/)vaše aplikace spravuje příchozí a odchozí zřizování uživatelů a skupin ze služby Azure AD do vaší aplikace pomocí dotazování rozhraní API Microsoft Graph.

* Pokud vaše aplikace používá pro federaci federačního rozhraní SAML, může být povoleno zřizování uživatelů v čase (SAML JIT) Security Assertion Markup Language. Pomocí informací o deklaracích odeslaných v tokenu SAML zřídí uživatele.

Pokud chcete zjistit, která možnost integrace se má použít pro vaši aplikaci, přečtěte si referenční tabulku vysoké úrovně a podívejte se na podrobnější informace o jednotlivých možnostech.

| Funkce, které jsou povolené nebo rozšířené pomocí automatického zřizování| Služba zřizování Azure AD (SCIM 2,0)| Rozhraní API pro Microsoft Graph (OData v 4.0)| JIT SAML |
|---|---|---|---|
| Správa uživatelů a skupin ve službě Azure AD| √| √| Pouze uživatel |
| Správa uživatelů a skupin synchronizovaných z místní služby Active Directory| √*| √*| Jenom uživatel * |
| Přístup k datům mimo uživatele a skupiny během zřizování přístupu k datům O365 (týmy, SharePoint, E-mail, kalendář, dokumenty atd.)| X+| √| × |
| Vytváření, čtení a aktualizace uživatelů na základě obchodních pravidel| √| √| √ |
| Odstranění uživatelů na základě obchodních pravidel| √| √| × |
| Správa automatického zřizování uživatelů pro všechny aplikace z Azure Portal| √| ×| √ |
| Podpora více zprostředkovatelů identity| √| ×| √ |
| Podpora účtů hostů (B2B)| √| √| √ |
| Podpora účtů mimo podnik (B2C)| ×| √| √ |

<sup>*</sup> – pro synchronizaci uživatelů ze služby AD do Azure AD se vyžaduje nastavení Azure AD Connect.  
<sup>+</sup >– použití SCIM pro zřizování vám nevylučuje integraci aplikace do Microsoft graphu pro jiné účely.

## <a name="azure-ad-provisioning-service-scim"></a>Služba zřizování Azure AD (SCIM)

Služba zřizování Azure AD používá [SCIM](https://aka.ms/SCIMOverview), což je standardní obor pro zřizování podporovaný mnoha zprostředkovateli identity (zprostředkovatelů identity) a také aplikacemi (například časová rezerva, g Suite, Dropbox). Doporučujeme použít službu zřizování Azure AD, pokud chcete kromě služby Azure AD podporovat zprostředkovatelů identity, protože libovolný IdP kompatibilní s SCIM se může připojit ke svému koncovému bodu SCIM. Vytvořením jednoduchého koncového bodu/User můžete povolit zřizování bez nutnosti udržovat vlastní synchronizační modul. 

Další informace o tom, jak se uživatelé služby zřizování Azure AD SCIM, najdete v těchto tématech: 

* [Další informace o standardu SCIM](https://aka.ms/SCIMOverview)

* [Automatické zřizování uživatelů a skupin z Azure Active Directory k aplikacím pomocí systému pro správu identit mezi doménami (SCIM)](use-scim-to-provision-users-and-groups.md)

* [Porozumění implementaci Azure AD SCIM](use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>Microsoft Graph pro zřizování

Při použití Microsoft Graph ke zřízení máte přístup ke všem uživatelům, kteří jsou k dispozici v grafu. Kromě podrobností o uživatelích a skupinách můžete také načítat Další informace, jako jsou role uživatelů, vedoucí a přímé sestavy, vlastněná a registrovaná zařízení a stovky dalších datových částí, které jsou k dispozici v [Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0). 

Více než 15 000 000 organizací a 90% Fortune 500 společnosti využívají Azure AD při přihlášení k odběru cloudových služeb Microsoftu, jako je Office 365, Microsoft Azure, Enterprise Mobility Suite nebo Microsoft 365. Pomocí Microsoft Graph můžete integrovat svou aplikaci s pracovními postupy pro správu, jako je například registrace zaměstnanců (a ukončení), údržba profilu a další. 

Další informace o použití Microsoft Graph pro zřizování:

* [Microsoft Graph domovskou stránku](https://developer.microsoft.com/graph)

* [Přehled Microsoft Graphu](https://docs.microsoft.com/graph/overview)

* [Přehled ověřování Microsoft Graph](https://docs.microsoft.com/graph/auth/)

* [Začínáme s Microsoft Graph](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>Používání JIT pro zřizování pomocí jazyka SAML

Pokud chcete uživatelům zřídit jenom při prvním přihlášení k vaší aplikaci a nemusíte automaticky zrušit zřízení uživatelů, SAML JIT je možnost. Vaše aplikace musí podporovat SAML 2,0 jako federační protokol pro použití JIT SAML.

Kompilátor SAML používá informace o deklaracích identity v tokenu SAML k vytvoření a aktualizaci informací o uživateli v aplikaci. Zákazníci můžou podle potřeby nakonfigurovat tyto požadované deklarace identity v aplikaci Azure AD. V některých případech je potřeba zřizování JIT povolit ze strany aplikace, aby zákazník mohl používat tuto funkci. Kompilátor SAML JIT je vhodný pro vytváření a aktualizaci uživatelů, ale nemůže uživatele v aplikaci odstranit ani deaktivovat.

## <a name="next-steps"></a>Další kroky

* [Povolení jednotného přihlašování pro aplikaci](isv-sso-content.md)

* [Odesláním seznamu aplikací](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) a partnera s Microsoftem můžete vytvořit dokumentaci na webu Microsoftu.

* [Připojte se k Microsoft Partner Network (zdarma) a vytvořte svůj přejít na trh](https://partner.microsoft.com/en-us/explore/commercial).
