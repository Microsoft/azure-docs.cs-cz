---
title: Ukázky PowerShellu pro Azure Proxy aplikací služby AD
description: Tyto ukázky PowerShellu pro Azure Proxy aplikací služby AD slouží k získání informací o aplikacích proxy aplikací a konektorech v adresáři, přiřazení uživatelů a skupin k aplikacím a získání informací o certifikátu.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: efe71fd77e9e4edb7fc7df4c633ae8f81177097b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657816"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Příklady Azure AD PowerShellu pro Azure Proxy aplikací služby AD

Následující tabulka obsahuje odkazy na příklady skriptu PowerShellu pro Azure Proxy aplikací služby AD. Pokud není uvedeno jinak, vyžadují tyto ukázky pro [modul grafu AzureAD v2 PowerShell pro graf](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) nebo [pro verzi Preview AzureAD v2 PowerShellu](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview).


Další informace o rutinách použitých v těchto ukázkách najdete v tématu Správa [aplikací proxy](/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) aplikací a [Správa konektoru proxy aplikací](/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management).

| Odkaz | Popis |
|---|---|
|**Aplikace proxy aplikací**||
| [Seznam základních informací pro všechny aplikace proxy aplikací](scripts/powershell-get-all-app-proxy-apps-basic.md) | Uvádí základní informace o všech aplikacích proxy aplikací ve vašem adresáři (AppId, DisplayName, ObjId). |
| [Zobrazit rozšířené informace pro všechny aplikace proxy aplikací](scripts/powershell-get-all-app-proxy-apps-extended.md) | Zobrazí seznam rozšířených informací (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) o všech aplikacích proxy aplikací ve vašem adresáři.  |
| [Vypsat všechny aplikace proxy aplikací podle skupiny konektorů](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Obsahuje seznam informací o všech aplikacích proxy aplikací ve vašem adresáři a skupin konektorů, ke kterým jsou aplikace přiřazené. |
| [Získat všechny aplikace proxy aplikací pomocí zásad životnosti tokenů](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Zobrazí všechny aplikace proxy aplikací ve vašem adresáři se zásadami životnosti tokenu a jeho podrobnostmi. Tato ukázka vyžaduje [prostředí PowerShell AzureAD v2 pro verzi Preview modulu graphu](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview). |
|**Skupiny konektorů**||
| [Získat všechny skupiny konektorů a konektory v adresáři](scripts/powershell-get-all-connectors.md) | Zobrazí seznam všech skupin konektorů a konektorů ve vašem adresáři. |
| [Přesunout všechny aplikace přiřazené ke skupině konektorů do jiné skupiny konektorů](scripts/powershell-move-all-apps-to-connector-group.md) | Přesune všechny aktuálně přiřazené aplikace do skupiny konektorů do jiné skupiny konektorů. |
|**Přiřazení uživatelé a skupiny**||
| [Zobrazit uživatele a skupiny přiřazené k aplikaci proxy aplikací](scripts/powershell-display-users-group-of-app.md) | Zobrazí seznam uživatelů a skupin přiřazených ke konkrétní aplikaci proxy aplikace. |
| [Přiřazení uživatele k aplikaci](scripts/powershell-assign-user-to-app.md) | Přiřadí konkrétního uživatele k aplikaci. |
| [Přiřazení skupiny k aplikaci](scripts/powershell-assign-group-to-app.md) | Přiřadí aplikaci konkrétní skupinu. |
|**Konfigurace externí adresy URL**||
| [Načíst všechny aplikace proxy aplikací pomocí výchozích domén (. msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Vypíše všechny aplikace proxy aplikací pomocí výchozích domén (. msappproxy.net). |
| [Získat všechny aplikace proxy aplikací pomocí publikování na základě zástupných znaků](scripts/powershell-get-all-wildcard-apps.md) | Vypíše všechny aplikace proxy aplikací pomocí publikování na základě zástupných znaků. |
|**Vlastní konfigurace domény**||
| [Získat všechny aplikace proxy aplikací pomocí vlastních domén a informací o certifikátu](scripts/powershell-get-all-custom-domains-and-certs.md) | Vypíše všechny aplikace proxy aplikací, které používají vlastní domény, a informace o certifikátech přidružených k vlastním doménám. |
| [Získání všech aplikací proxy aplikace Azure AD publikovaných bez nahraného certifikátu](scripts/powershell-get-all-custom-domain-no-cert.md) | Zobrazí seznam všech aplikačních proxy aplikací, které používají vlastní domény, ale nemá nahraný platný certifikát TLS/SSL. |
| [Získat všechny aplikace proxy aplikací Azure AD publikované se stejným certifikátem](scripts/powershell-get-custom-domain-identical-cert.md) | Zobrazí seznam všech aplikací proxy serveru Azure AD publikovaných se stejným certifikátem. |
| [Získat všechny aplikace proxy aplikací služby Azure AD publikované se stejným certifikátem a nahradit je](scripts/powershell-get-custom-domain-replace-cert.md) | Pro aplikace proxy serveru Azure AD, které jsou publikovány s totožným certifikátem, umožňuje nahradit certifikát hromadně. |