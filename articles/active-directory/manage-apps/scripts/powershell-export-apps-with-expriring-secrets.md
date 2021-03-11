---
title: Ukázky PowerShellu – exportujte aplikace s vypršením tajných kódů a certifikátů v Azure Active Directory tenant.
description: Příklad PowerShellu, který vyexportuje všechny aplikace s platností tajných kódů a certifikátů pro zadané aplikace ve vašem tenantovi Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: 7123c87d8883d9ac309797255152611fb9fb81d2
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584275"
---
# <a name="export-apps-with-expiring-secrets-and-certificates"></a>Exportujte aplikace s platností tajných kódů a certifikátů.

Tento ukázkový skript PowerShellu exportuje všechny aplikace s vypršenou platností tajných kódů a certifikátů pro zadané aplikace z vašeho adresáře v souboru CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Tato ukázka vyžaduje [prostředí PowerShell AzureAD v2 pro modul Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) nebo [prostředí AzureAD v2 PowerShell pro verzi Preview modulu grafu](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-expiring-secrets.ps1 "Exports all apps with expiring secrets and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Příkaz "Add-Member" zodpovídá za vytváření sloupců v souboru CSV.
Proměnnou "$Path" můžete upravit přímo v PowerShellu s cestou k souboru CSV, pokud chcete, aby export neobsahoval neinteraktivní.

| Příkaz | Poznámky |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Načte aplikaci z adresáře. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | Načte vlastníky aplikace z vašeho adresáře. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure AD PowerShell najdete v tématu [Přehled modulu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Další příklady prostředí PowerShell pro správu aplikací najdete v tématu [Příklady Azure AD PowerShellu pro správu aplikací](../app-management-powershell-samples.md).
