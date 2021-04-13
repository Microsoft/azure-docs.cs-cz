---
title: Nastavení životností tokenů
titleSuffix: Microsoft identity platform
description: Naučte se nastavit životnost pro tokeny vydané platformou Microsoft identity. Naučte se, jak spravovat výchozí zásady organizace, vytvořit zásadu pro webové přihlašování, vytvořit zásadu pro nativní aplikaci, která volá webové rozhraní API, a spravovat pokročilé zásady.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 66e9817c6d3bbcd199418b9afd78eda016c5f291
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363882"
---
# <a name="configure-token-lifetime-policies-preview"></a>Konfigurace zásad životnosti tokenů (Preview)
Můžete zadat dobu života přístupu, SAML nebo ID tokenu vydaného platformou Microsoft identity. Životnost tokenů je možné nastavit u všech aplikací ve vaší organizaci, u aplikace pro více tenantů nebo pro konkrétní objekt služby ve vaší organizaci. Další informace najdete v tématu [konfigurovatelné životnosti tokenů](active-directory-configurable-token-lifetimes.md).

V této části se seznámíte se společným scénářem zásad, který vám může pomoci stanovit nová pravidla pro dobu života tokenu. V tomto příkladu se dozvíte, jak vytvořit zásadu, která vyžaduje, aby se uživatelé ve vaší webové aplikaci ověřovali častěji.

## <a name="get-started"></a>Začínáme

Pokud chcete začít, Stáhněte si nejnovější [verzi modulu Azure AD PowerShell Public Preview](https://www.powershellgallery.com/packages/AzureADPreview).

Potom spusťte `Connect` příkaz pro přihlášení ke svému účtu správce Azure AD. Spusťte tento příkaz pokaždé, když spustíte novou relaci.

```powershell
Connect-AzureAD -Confirm
```

## <a name="create-a-policy-for-web-sign-in"></a>Vytvoření zásady pro webové přihlašování

V tomto příkladu vytvoříte zásadu, která vyžaduje, aby se uživatelé ve vaší webové aplikaci ověřovali častěji. Tato zásada nastaví dobu života tokenů přístupu nebo ID k instančnímu objektu vaší webové aplikace.

1. Vytvořte zásady životnosti tokenů.

    Tato zásada, pro webové přihlašování, nastavuje dobu života tokenu přístupu/ID na dvě hodiny.

    Pokud chcete vytvořit zásadu, spusťte rutinu [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

    ```powershell
    $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
    ```

    Pokud chcete zobrazit nové zásady a získat **objectID** zásad, spusťte rutinu [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

    ```powershell
    Get-AzureADPolicy -Id $policy.Id
    ```

1. Přiřaďte zásadu k instančnímu objektu. Také je nutné získat **objectID** objektu služby.

    Pomocí rutiny [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) můžete zobrazit všechny instanční objekty vaší organizace nebo jeden instanční objekt.

    ```powershell
    # Get ID of the service principal
    $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
    ```

    Pokud máte instanční objekt, spusťte rutinu [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :

    ```powershell
    # Assign policy to a service principal
    Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
    ```

## <a name="view-existing-policies-in-a-tenant"></a>Zobrazení existujících zásad v tenantovi

Chcete-li zobrazit všechny zásady, které byly vytvořeny ve vaší organizaci, spusťte rutinu [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) .  Všechny výsledky s definovanými hodnotami vlastností, které se liší od výše uvedených výchozích hodnot, jsou v rozsahu vyřazení.

```powershell
Get-AzureADPolicy -All $true
```

Pokud chcete zjistit, které aplikace a instanční objekty jsou propojené s konkrétní zásadou, kterou jste zjistili, spusťte následující rutinu [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) nahrazením **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** pomocí libovolných identifikátorů vašich zásad. Pak se můžete rozhodnout, jestli chcete nakonfigurovat četnost přihlášení podmíněného přístupu, nebo zachovat výchozí nastavení služby Azure AD.

```powershell
Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
```

Pokud má váš tenant zásady, které definují vlastní hodnoty vlastností konfigurace a tokenu relace, společnost Microsoft doporučuje tyto zásady aktualizovat na hodnoty, které odpovídají výchozím hodnotám uvedeným výše. Pokud se neprovede žádné změny, služba Azure AD automaticky použije výchozí hodnoty.

### <a name="troubleshooting"></a>Řešení potíží
Někteří uživatelé oznámili `Get-AzureADPolicy : The term 'Get-AzureADPolicy' is not recognized` chybu po spuštění `Get-AzureADPolicy` rutiny. Alternativním řešením je, že spuštěním následujícího postupu odinstalujete nebo znovu nainstalujete modul AzureAD a pak nainstalujete modul AzureADPreview:

```powershell
# Uninstall the AzureAD Module
UnInstall-Module AzureAD

# Re-install the AzureAD Module
Install-Module AzureAD

# Install the AzureAD Preview Module adding the -AllowClobber
Install-Module AzureADPreview -AllowClobber

Connect-AzureAD
Get-AzureADPolicy -All $true
```

## <a name="next-steps"></a>Další kroky
Seznamte se s [možnostmi správy relace ověřování](../conditional-access/howto-conditional-access-session-lifetime.md) v podmíněném přístupu Azure AD.
