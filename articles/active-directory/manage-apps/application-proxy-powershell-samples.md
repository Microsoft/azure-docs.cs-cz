---
title: Ukázky Prostředí PowerShell pro proxy aplikace Azure AD
description: Pomocí těchto ukázek Prostředí PowerShell pro proxy aplikace Azure AD získáte informace o aplikacích a konektorech proxy aplikací ve vašem adresáři, přiřadíte uživatelům a skupinám aplikace a získejte informace o certifikátu.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 476bdfdd2c0b81d86900cee18024cc261ee80a07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481258"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Příklady Azure AD PowerShellu pro proxy aplikace Azure AD

Následující tabulka obsahuje odkazy na příklady skriptů PowerShellu pro proxy aplikace Azure AD. Tyto ukázky vyžadují buď [Modul AzureAD V2 PowerShell pro graf](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) nebo [AzureAD V2 PowerShell pro graf verze náhledu modulu](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview), pokud není uvedeno jinak.


For more information about the cmdlets used in these samples, see [Application Proxy Application Management](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) and [Application Proxy Connector Management](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management).

| | |
|---|---|
|**Aplikace Proxy aplikace**||
| [Seznam základních informací pro všechny aplikace Proxy aplikace](scripts/powershell-get-all-app-proxy-apps-basic.md) | Uvádí základní informace (AppId, DisplayName, ObjId) o všech aplikacích Proxy aplikace ve vašem adresáři . |
| [Seznam rozšířených informací pro všechny aplikace Proxy aplikace](scripts/powershell-get-all-app-proxy-apps-extended.md) | Uvádí rozšířené informace (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) o všech aplikacích proxy aplikace v adresáři.  |
| [Vypsat všechny aplikace proxy aplikací podle skupiny konektorů](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Zobrazí seznam informací o všech aplikacích Proxy aplikace ve vašem adresáři a o skupinách konektorů, ke kterým jsou aplikace přiřazeny. |
| [Získání všech aplikací proxy aplikací se zásadami životnosti tokenu](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Zobrazí seznam všech aplikací proxy aplikací ve vašem adresáři se zásadami životnosti tokenu a jeho podrobnosti. Tato ukázka vyžaduje [verzi preview modulu AzureAD V2 PowerShell pro graf](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview). |
|**Skupiny konektorů**||
| [Získání všech skupin konektorů a konektorů v adresáři](scripts/powershell-get-all-connectors.md) | Zobrazí seznam všech skupin konektorů a konektorů v adresáři. |
| [Přesunutí všech aplikací přiřazených ke skupině konektorů do jiné skupiny konektorů](scripts/powershell-move-all-apps-to-connector-group.md) | Přesune všechny aplikace aktuálně přiřazené ke skupině konektorů do jiné skupiny konektorů. |
|**Přiřazeni uživatelé a skupina**||
| [Zobrazení uživatelů a skupin přiřazených k aplikaci Proxy aplikace](scripts/powershell-display-users-group-of-app.md) | Zobrazí seznam uživatelů a skupin přiřazených k určité aplikaci proxy aplikace. |
| [Přiřazení uživatele k aplikaci](scripts/powershell-assign-user-to-app.md) | Přiřadí konkrétního uživatele k aplikaci. |
| [Přiřazení skupiny k aplikaci](scripts/powershell-assign-group-to-app.md) | Přiřadí konkrétní skupinu k aplikaci. |
|**Konfigurace externí adresy URL**||
| [Získání všech aplikací proxy aplikací pomocí výchozích domén (.msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Zobrazí seznam všech aplikací proxy aplikací používajících výchozí domény (.msappproxy.net). |
| [Získat všechny aplikace Proxy aplikace pomocí publikování zástupných symbolů](scripts/powershell-get-all-wildcard-apps.md) | Zobrazí seznam všech aplikací proxy aplikací pomocí publikování se zástupnými kódy. |
|**Konfigurace vlastní domény**||
| [Získání všech aplikací Proxy aplikace pomocí vlastních domén a informací o certifikátech](scripts/powershell-get-all-custom-domains-and-certs.md) | Zobrazí seznam všech aplikací proxy aplikací, které používají vlastní domény, a informace o certifikátu přidružené k vlastním doménám. |
| [Publikování všech aplikací aplikace Proxy Azure AD bez nahrání certifikátu](scripts/powershell-get-all-custom-domain-no-cert.md) | Zobrazí seznam všech aplikací proxy aplikací, které používají vlastní domény, ale nemají nahraný platný certifikát TLS/SSL. |
| [Publikování všech aplikací aplikace Azure AD Proxy s identickým certifikátem](scripts/powershell-get-custom-domain-identical-cert.md) | Zobrazí seznam všech aplikací aplikace Proxy Azure AD publikované s identickým certifikátem. |
| [Publikování všech aplikací aplikace Azure AD Proxy s identickým certifikátem a jeho nahrazení](scripts/powershell-get-custom-domain-replace-cert.md) | Pro aplikace aplikace Azure AD Proxy, které jsou publikovány s identickým certifikátem, umožňuje nahradit certifikát hromadně. |
