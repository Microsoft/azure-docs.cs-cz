---
title: Ukázka prostředí PowerShell – seznam všech aplikací proxy aplikací se zásadami
description: Příklad Prostředí PowerShell, který obsahuje seznam všech aplikací proxy aplikací Azure Active Directory (Azure AD) ve vašem adresáři, které mají zásady celoživotního tokenu.
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
ms.openlocfilehash: c2ad7b769dc890917e5364fe57582acdfe16acac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482124"
---
# <a name="get-all-application-proxy-apps-with-a-token-lifetime-policy"></a>Získání všech aplikací proxy aplikací se zásadami životnosti tokenu

Tento příklad skriptu Prostředí PowerShell uvádí všechny aplikace proxy aplikací Azure Active Directory (Azure AD) ve vašem adresáři, které mají zásady životnosti tokenu a uvádí podrobnosti o zásadách.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Tato ukázka vyžaduje [verzi náhledu modulu AzureAD V2 PowerShell pro graf](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-with-policy.ps1 "Get all Application Proxy apps with a token lifetime policy")]

## <a name="script-explanation"></a>Vysvětlení skriptu

| Příkaz | Poznámky |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Získá instanční objekt. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Získá aplikaci Azure AD. |
|[Zásady získání AzureAD](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) | Získá zásady ve službě Azure AD. |
|[Zásady hlavní hospo-nastavení azuread](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview) | Získá zásady instančního objektu ve službě Azure AD. |


## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure AD PowerShell najdete v [tématu Přehled modulu Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Další příklady PowerShellu pro proxy aplikace najdete [v tématu příklady Azure AD PowerShellu pro proxy aplikace Azure AD](../application-proxy-powershell-samples.md).