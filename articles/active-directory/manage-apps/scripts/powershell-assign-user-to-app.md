---
title: Ukázka prostředí PowerShell – přiřazení uživatele k aplikaci proxy aplikace
description: Příklad prostředí PowerShell, který přiřadí uživatele k aplikaci proxy aplikace Azure Active Directory (Azure AD).
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
ms.openlocfilehash: 8405afef07f5d3f52becc1acf184e9a36db8971c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548525"
---
# <a name="assign-a-user-to-a-specific-azure-ad-application-proxy-application"></a>Přiřazení uživatele ke konkrétní aplikaci Azure Proxy aplikací služby AD

Tento ukázkový skript PowerShellu vám umožní přiřadit uživatele ke konkrétní aplikaci Azure Proxy aplikací služby AD.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Tato ukázka vyžaduje [prostředí PowerShell AzureAD v2 pro modul Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) nebo [prostředí AzureAD v2 PowerShell pro verzi Preview modulu grafu](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-user-to-app.ps1 "Assign a user to an application")]

## <a name="script-explanation"></a>Vysvětlení skriptu

| Příkaz | Poznámky |
|---|---|
| [New-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/new-azureaduserapproleassignment) | Přiřadí uživatele k roli aplikace. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure AD PowerShell najdete v tématu [Přehled modulu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Další příklady PowerShellu pro proxy aplikací najdete v tématu [Azure AD PowerShell – příklady pro azure proxy aplikací služby AD](../application-proxy-powershell-samples.md).
