---
title: Ukázka PowerShellu – export aplikací s tajnými klíči a certifikáty po uplynutí doby od data, které se Azure Active Directory tenant.
description: Příklad prostředí PowerShell, který vyexportuje všechny aplikace s tajnými klíči a certifikáty, jejichž platnost překračuje požadované datum pro zadané aplikace ve vašem tenantovi Azure Active Directory.
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
ms.openlocfilehash: 9c0e5508830343561833785fbce31f547a8a7428
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149677"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>Exportujte aplikace s tajnými kódy a certifikáty po dobu mimo požadované datum.

Tento ukázkový skript PowerShellu exportuje tajná data všech aplikací a certifikáty, jejichž platnost vyprší po dobu, kdy zadané aplikace z vašeho adresáře v souboru CSV nejsou interaktivně.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript pracuje neinteraktivně. Správce používající ho bude muset změnit hodnoty v oddílu #PARAMETERS ke změně s vlastním ID aplikace, tajný klíč aplikace, název tenanta, období platnosti přihlašovacích údajů pro aplikace a cestu, kam se bude CSV exportovat.
Tento skript používá [tok Oauth Client_Credential](../../develop/v2-oauth2-client-creds-grant-flow.md) . funkce "refreshtoken kontextového tokenu" vytvoří přístupový token na základě hodnot parametrů změněných správcem.

Příkaz "Add-Member" zodpovídá za vytváření sloupců v souboru CSV.

| Příkaz | Poznámky |
|---|---|
| [Invoke – WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1) | Odesílá požadavky HTTP a HTTPS na webovou stránku nebo webovou službu. Analyzuje odpověď a vrátí kolekce odkazů, obrázků a dalších významných prvků jazyka HTML. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure AD PowerShell najdete v tématu [Přehled modulu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Další příklady prostředí PowerShell pro správu aplikací najdete v tématu [Příklady Azure AD PowerShellu pro správu aplikací](../app-management-powershell-samples.md).
