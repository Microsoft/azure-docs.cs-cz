---
title: Ukázka PowerShellu – export aplikací s tajnými klíči a certifikáty po uplynutí doby od data, které se Azure Active Directory tenant.
description: Příklad prostředí PowerShell, který vyexportuje všechny aplikace s tajnými klíči a certifikáty, jejichž platnost překračuje požadované datum pro zadané aplikace ve vašem tenantovi Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 692ab2cfd480fd15760c13c63922fe76d23058ea
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375385"
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
| [Invoke – WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1&preserve-view=true) | Odesílá požadavky HTTP a HTTPS na webovou stránku nebo webovou službu. Analyzuje odpověď a vrátí kolekce odkazů, obrázků a dalších významných prvků jazyka HTML. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure AD PowerShell najdete v tématu [Přehled modulu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Další příklady prostředí PowerShell pro správu aplikací najdete v tématu [Příklady Azure AD PowerShellu pro správu aplikací](../app-management-powershell-samples.md).
