---
title: Ukázka PowerShellu – přesunutí aplikací proxy aplikací do jiné skupiny
description: Příklad aplikačního proxy serveru aplikace Azure Active Directory (Azure AD), který se používá k přesunu všech aplikací aktuálně přiřazených ke skupině konektorů do jiné skupiny konektorů.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 9acfa6b8b27c092416e16d404ad3e0dc907e5e3a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550985"
---
# <a name="move-all-apps-assigned-to-a-connector-group-to-another-connector-group"></a>Přesunout všechny aplikace přiřazené ke skupině konektorů do jiné skupiny konektorů

Tento ukázkový skript PowerShellu přesune všechny aplikace proxy aplikací pro Azure Active Directory (Azure AD), které jsou aktuálně přiřazené ke skupině konektorů, do jiné skupiny konektorů.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Tato ukázka vyžaduje [prostředí PowerShell AzureAD v2 pro modul Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) nebo [prostředí AzureAD v2 PowerShell pro verzi Preview modulu grafu](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>Vysvětlení skriptu

| Příkaz | Poznámky |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Získá instanční objekt. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Načte aplikaci Azure AD. |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Načte seznam všech skupin konektorů, nebo je-li tento parametr zadán, zobrazí podrobnosti o zadané skupině konektorů. |
| [Set-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup) | Přiřadí danou skupinu konektorů k zadané aplikaci.|

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure AD PowerShell najdete v tématu [Přehled modulu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Další příklady PowerShellu pro proxy aplikací najdete v tématu [Azure AD PowerShell – příklady pro azure proxy aplikací služby AD](../application-proxy-powershell-samples.md).
