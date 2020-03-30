---
title: Ukázka Prostředí PowerShell – přiřazení skupiny k aplikaci proxy aplikace
description: Příklad prostředí PowerShell, který přiřadí skupinu k aplikaci proxy aplikace Azure Active Directory (Azure AD).
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
ms.openlocfilehash: 540453e5de3eed97b1207ca16443b46dffd5f8e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483346"
---
# <a name="assign-a-group-to-a-specific-azure-ad-application-proxy-application"></a>Přiřazení skupiny ke konkrétní aplikaci proxy aplikace Azure AD

Tento příklad skriptu Prostředí PowerShell umožňuje přiřadit konkrétní skupinu k aplikaci proxy aplikace Azure Active Directory (Azure AD).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Tato ukázka vyžaduje [modul AzureAD V2 PowerShell pro graf](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) nebo verzi preview [modulu AzureAD V2 PowerShell pro graf](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-group-to-app.ps1 "Assign a group to a specific Azure AD Application Proxy application")]

## <a name="script-explanation"></a>Vysvětlení skriptu

| Příkaz | Poznámky |
|---|---|
| [New-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/New-azureadgroupapproleassignment?view=azureadps-2.0) | Přiřadí skupinu k roli aplikace. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure AD PowerShell najdete v [tématu Přehled modulu Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Další příklady PowerShellu pro proxy aplikace najdete [v tématu příklady Azure AD PowerShellu pro proxy aplikace Azure AD](../application-proxy-powershell-samples.md).