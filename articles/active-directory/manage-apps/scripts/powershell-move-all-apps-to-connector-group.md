---
title: Ukázka prostředí PowerShell – přesunutí aplikací proxy aplikací do jiné skupiny
description: Azure Active Directory (Azure AD) Application Proxy PowerShell příklad slouží k přesunutí všech aplikací aktuálně přiřazených ke skupině konektorů do jiné skupiny konektorů.
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
ms.openlocfilehash: f6dccdaa96dadb061b168bbdf6148ed4d759776c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75481981"
---
# <a name="move-all-apps-assigned-to-a-connector-group-to-another-connector-group"></a>Přesunutí všech aplikací přiřazených ke skupině konektorů do jiné skupiny konektorů

Tento příklad skriptu Prostředí PowerShell přesune všechny aplikace proxy aplikací Azure Active Directory (Azure AD), které jsou aktuálně přiřazeny skupině konektorů do jiné skupiny konektorů.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Tato ukázka vyžaduje [modul AzureAD V2 PowerShell pro graf](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) nebo verzi preview [modulu AzureAD V2 PowerShell pro graf](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>Vysvětlení skriptu

| Příkaz | Poznámky |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Získá instanční objekt. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Získá aplikaci Azure AD. |
| [Get-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Načte seznam všech skupin konektorů nebo pokud je zadán, podrobnosti o zadané skupiny spojnic. |
| [Set-AzureadApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup?view=azureadps-2.0) | Přiřadí danou skupinu konektorů určené aplikaci.|

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure AD PowerShell najdete v [tématu Přehled modulu Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Další příklady PowerShellu pro proxy aplikace najdete [v tématu příklady Azure AD PowerShellu pro proxy aplikace Azure AD](../application-proxy-powershell-samples.md).