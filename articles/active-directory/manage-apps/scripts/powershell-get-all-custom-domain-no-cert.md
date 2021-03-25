---
title: Ukázka prostředí PowerShell – aplikace proxy aplikací bez certifikátu
description: Příklad PowerShellu, který obsahuje seznam všech aplikací proxy aplikací Azure Active Directory (Azure AD), které používají vlastní domény, ale nemají platný certifikát TLS/SSL.
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
ms.openlocfilehash: f4b661ee66d3fb045dfa4891f1f1c1643927167c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044035"
---
# <a name="get-all-application-proxy-apps-published-with-no-certificate-uploaded"></a>Načte všechny aplikace proxy aplikací publikované bez nahraného certifikátu.

Tento ukázkový skript PowerShellu obsahuje seznam všech aplikací proxy aplikace Azure Active Directory (Azure AD), které používají vlastní domény, ale nemají nahraný platný certifikát TLS/SSL.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Tato ukázka vyžaduje [prostředí PowerShell AzureAD v2 pro modul Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) nebo [prostředí AzureAD v2 PowerShell pro verzi Preview modulu grafu](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-custom-domain-no-cert.ps1 "Get all Azure AD Proxy application apps published with no certificate uploaded")]

## <a name="script-explanation"></a>Vysvětlení skriptu

| Příkaz | Poznámky |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Získá instanční objekt. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Načte aplikaci Azure AD. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Načte aplikaci nakonfigurovanou pro proxy aplikací v Azure AD. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure AD PowerShell najdete v tématu [Přehled modulu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Další příklady PowerShellu pro proxy aplikací najdete v tématu [Azure AD PowerShell – příklady pro azure proxy aplikací služby AD](../application-proxy-powershell-samples.md).
