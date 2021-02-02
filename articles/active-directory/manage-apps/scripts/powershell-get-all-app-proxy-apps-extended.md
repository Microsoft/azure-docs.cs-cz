---
title: Ukázka PowerShellu – seznam rozšířených informací pro aplikace proxy aplikací
description: Příklad PowerShellu, který obsahuje seznam všech aplikací proxy aplikace Azure Active Directory (Azure AD) spolu s ID aplikace (AppId), názvem (DisplayName), externí adresou URL (ExternalUrl), interní adresou URL (InternalUrl) a typem ověřování (ExternalAuthenticationType).
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
ms.openlocfilehash: ccd0c7be7fd0dd533028faa0dc2bbdad30d74c79
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258672"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Načte všechny aplikace proxy aplikací a seznam rozšířených informací.

Tento ukázkový skript PowerShellu obsahuje informace o všech aplikacích proxy aplikací pro Azure Active Directory (Azure AD), včetně ID aplikace (AppId), názvu (DisplayName), externí adresy URL (ExternalUrl), interní URL (InternalUrl), typu ověřování (ExternalAuthenticationType), režimu jednotného přihlašování a dalších nastavení.

Změna hodnoty proměnné $ssoMode umožňuje filtrovaný výstup podle režimu jednotného přihlašování. Další podrobnosti jsou popsány ve skriptu.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Tato ukázka vyžaduje [powershellový modul AzureAD v2 pro Graph](/powershell/azure/active-directory/install-adv2) (AzureAD).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Vysvětlení skriptu

| Příkaz | Poznámky |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Získá instanční objekt. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Načte aplikaci Azure AD. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Načte aplikaci nakonfigurovanou pro proxy aplikací v Azure AD. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure AD PowerShell najdete v tématu [Přehled modulu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Další příklady PowerShellu pro proxy aplikací najdete v tématu [Azure AD PowerShell – příklady pro azure proxy aplikací služby AD](../application-proxy-powershell-samples.md).
