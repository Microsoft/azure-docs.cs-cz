---
title: Ukázka prostředí PowerShell – Export tajných kódů a certifikátů pro podnikové aplikace v Azure Active Directory tenanta
description: Příklad PowerShellu, který exportuje všechny tajné klíče a certifikáty pro zadané podnikové aplikace ve vašem tenantovi Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 536197ebc5df94447f3937773e0447e47961bd92
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378597"
---
# <a name="export-secrets-and-certificates-for-enterprise-apps"></a>Export tajných klíčů a certifikátů pro podnikové aplikace
Tento ukázkový skript PowerShellu exportuje všechny tajné klíče, certifikáty a vlastníky pro zadané podnikové aplikace z vašeho adresáře do souboru CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Tato ukázka vyžaduje [prostředí PowerShell AzureAD v2 pro modul Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) nebo [prostředí AzureAD v2 PowerShell pro verzi Preview modulu grafu](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-enterprise-apps-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified enterprise apps in your directory.")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Příkaz "Add-Member" zodpovídá za vytváření sloupců v souboru CSV.
Proměnnou "$Path" můžete upravit přímo v PowerShellu s cestou k souboru CSV, pokud chcete, aby export neobsahoval neinteraktivní.

| Příkaz | Poznámky |
|---|---|
| [Get-AzureADServicePrincipal](/powershell/module/azuread/Get-azureADServicePrincipal?view=azureadps-2.0&preserve-view=true) | Načte podnikovou aplikaci z vašeho adresáře. |
| [Get-AzureADServicePrincipalOwner](/powershell/module/azuread/Get-AzureADServicePrincipalOwner?view=azureadps-2.0&preserve-view=true) | Načte vlastníky podnikové aplikace z vašeho adresáře. |


## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure AD PowerShell najdete v tématu [Přehled modulu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Další příklady prostředí PowerShell pro správu aplikací najdete v tématu [Příklady Azure AD PowerShellu pro správu aplikací](../app-management-powershell-samples.md).
