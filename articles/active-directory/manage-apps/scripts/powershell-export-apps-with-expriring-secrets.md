---
title: Ukázky PowerShellu – exportujte aplikace s vypršením tajných kódů a certifikátů v Azure Active Directory tenant.
description: Příklad PowerShellu, který vyexportuje všechny aplikace s platností tajných kódů a certifikátů pro zadané aplikace ve vašem tenantovi Azure Active Directory.
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
ms.openlocfilehash: 7f129e06904497b43eff8a3f0221fb57565ac112
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375402"
---
# <a name="export-apps-with-expiring-secrets-and-certificates"></a>Exportujte aplikace s platností tajných kódů a certifikátů.

Tento ukázkový skript PowerShellu exportuje do souboru CSV všechny registrace aplikací s vypršením platnosti tajných klíčů, certifikátů a jejich vlastníků pro zadané aplikace z vašeho adresáře.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Tato ukázka vyžaduje [prostředí PowerShell AzureAD v2 pro modul Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) nebo [prostředí AzureAD v2 PowerShell pro verzi Preview modulu grafu](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-expiring-secrets.ps1 "Exports all apps with expiring secrets and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Skript lze použít přímo bez jakýchkoli úprav. Správce bude požádán o datum vypršení platnosti a to, jestli chcete, aby viděli již prošlé tajné klíče nebo certifikáty.

Příkaz "Add-Member" zodpovídá za vytváření sloupců v souboru CSV.
Příkaz New-Object vytvoří objekt, který se použije pro sloupce v exportu souboru CSV.
Proměnnou "$Path" můžete upravit přímo v PowerShellu s cestou k souboru CSV, pokud chcete, aby export neobsahoval neinteraktivní.

| Příkaz | Poznámky |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Načte aplikaci z adresáře. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | Načte vlastníky aplikace z vašeho adresáře. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure AD PowerShell najdete v tématu [Přehled modulu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Další příklady prostředí PowerShell pro správu aplikací najdete v tématu [Příklady Azure AD PowerShellu pro správu aplikací](../app-management-powershell-samples.md).
