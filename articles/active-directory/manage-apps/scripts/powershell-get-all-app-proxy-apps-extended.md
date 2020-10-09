---
title: Ukázka PowerShellu – seznam rozšířených informací pro aplikace proxy aplikací
description: Příklad PowerShellu, který obsahuje seznam všech aplikací proxy aplikace Azure Active Directory (Azure AD) spolu s ID aplikace (AppId), názvem (DisplayName), externí adresou URL (ExternalUrl), interní adresou URL (InternalUrl) a typem ověřování (ExternalAuthenticationType).
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
ms.openlocfilehash: 8994abc61e27a9d7c7097aacab92282f0c47c989
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88506885"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Načte všechny aplikace proxy aplikací a seznam rozšířených informací.

Tento ukázkový skript PowerShellu obsahuje informace o všech aplikacích proxy aplikací pro Azure Active Directory (Azure AD), včetně ID aplikace (AppId), názvu (DisplayName), externí adresy URL (ExternalUrl), interní URL (InternalUrl) a typu ověřování (ExternalAuthenticationType).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Tato ukázka vyžaduje [prostředí PowerShell AzureAD v2 pro modul Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) nebo [prostředí AzureAD v2 PowerShell pro verzi Preview modulu grafu](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Vysvětlení skriptu

| Příkaz | Poznámky |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Získá instanční objekt. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Načte aplikaci Azure AD. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Načte aplikaci nakonfigurovanou pro proxy aplikací v Azure AD. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure AD PowerShell najdete v tématu [Přehled modulu Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Další příklady PowerShellu pro proxy aplikací najdete v tématu [Azure AD PowerShell – příklady pro azure proxy aplikací služby AD](../application-proxy-powershell-samples.md).