---
title: Ukázka prostředí PowerShell – seznam všech skupin konektorů proxy aplikací
description: Příklad prostředí PowerShell, ve kterém jsou uvedeny všechny skupiny konektorů a konektorů proxy aplikací aplikace Azure Active Directory (Azure AD) ve vašem adresáři.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: aad28b7b6e44d2f0e9ef8bb5638f8e80010f1284
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654044"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>Získat všechny skupiny konektorů a konektory v adresáři

Tento ukázkový skript PowerShellu obsahuje seznam všech skupin a konektorů proxy aplikací aplikace Azure Active Directory (Azure AD) ve vašem adresáři.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Tato ukázka vyžaduje [prostředí PowerShell AzureAD v2 pro modul Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) nebo [prostředí AzureAD v2 PowerShell pro verzi Preview modulu grafu](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>Vysvětlení skriptu

| Příkaz | Poznámky |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Načte seznam všech skupin konektorů, nebo je-li tento parametr zadán, zobrazí podrobnosti o zadané skupině konektorů. |
| [Get-AzureADApplicationProxyConnectorGroupMembers](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers?view=azureadps-2.0) | Načte všechny konektory proxy aplikací přidružené ke každé skupině konektorů.|

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure AD PowerShell najdete v tématu [Přehled modulu Azure AD PowerShell](/powershell/azure/active-directory/overview?view=azureadps-2.0).

Další příklady PowerShellu pro proxy aplikací najdete v tématu [Azure AD PowerShell – příklady pro azure proxy aplikací služby AD](../application-proxy-powershell-samples.md).