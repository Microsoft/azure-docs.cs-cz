---
title: Ukázka prostředí PowerShell – aplikace proxy aplikací se stejnými certifikáty
description: Příklad PowerShellu, který obsahuje seznam všech aplikací proxy aplikace Azure Active Directory (Azure AD), které jsou publikované se stejným certifikátem.
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
ms.openlocfilehash: 0debc48a5509d4c9e3b2b36642c86cd1f0da3ac4
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861558"
---
# <a name="get-all-azure-ad-proxy-application-apps-that-are-published-with-the-identical-certificate"></a>Získat všechny aplikace proxy serveru Azure AD, které jsou publikované se stejným certifikátem

Tento ukázkový skript PowerShellu obsahuje seznam všech aplikací proxy aplikace Azure Active Directory (Azure AD), které jsou publikované se stejným certifikátem.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Tato ukázka vyžaduje [prostředí PowerShell AzureAD v2 pro modul Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) nebo [prostředí AzureAD v2 PowerShell pro verzi Preview modulu grafu](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-identical-cert.ps1 "Get all Azure AD Proxy application apps published with the identical certificate")]

## <a name="script-explanation"></a>Vysvětlení skriptu

| Příkaz | Poznámky |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Získá instanční objekt. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Načte aplikaci Azure AD. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Načte aplikaci nakonfigurovanou pro proxy aplikací v Azure AD. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure AD PowerShell najdete v tématu [Přehled modulu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Další příklady PowerShellu pro proxy aplikací najdete v tématu [Azure AD PowerShell – příklady pro azure proxy aplikací služby AD](../application-proxy-powershell-samples.md).
