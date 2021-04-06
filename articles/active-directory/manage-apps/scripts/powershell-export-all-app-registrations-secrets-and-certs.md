---
title: Ukázka prostředí PowerShell – Export tajných kódů a certifikátů pro registraci aplikací v klientovi Azure Active Directory.
description: Příklad PowerShellu pro export všech tajných klíčů a certifikátů pro zadané registrace aplikací ve vašem tenantovi Azure Active Directory.
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
ms.openlocfilehash: d0de96d0d8a5edc6fbacc25dcbcb868073e57183
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556549"
---
# <a name="export-secrets-and-certificates-for-app-registrations"></a>Export tajných klíčů a certifikátů pro registrace aplikací

Tento ukázkový skript PowerShellu exportuje všechny tajné klíče a certifikáty pro zadané registrace aplikací z vašeho adresáře do souboru CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Tato ukázka vyžaduje [prostředí PowerShell AzureAD v2 pro modul Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) nebo [prostředí AzureAD v2 PowerShell pro verzi Preview modulu grafu](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-app-registrations-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified app registrations in your directory.")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Příkaz "Add-Member" zodpovídá za vytváření sloupců v souboru CSV.
Proměnnou "$Path" můžete upravit přímo v PowerShellu s cestou k souboru CSV, pokud chcete, aby export neobsahoval neinteraktivní.

| Příkaz | Poznámky |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Načte aplikaci z adresáře. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner) | Načte vlastníky aplikace z vašeho adresáře. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure AD PowerShell najdete v tématu [Přehled modulu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Další příklady prostředí PowerShell pro správu aplikací najdete v tématu [Příklady Azure AD PowerShellu pro správu aplikací](../app-management-powershell-samples.md).
