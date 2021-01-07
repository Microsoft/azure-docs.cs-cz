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
ms.date: 01/04/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 4d6a7150c854ba89c3cd8eacd6b553c4b8e97343
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963345"
---
# <a name="configure-token-lifetime-policies-preview"></a>Konfigurace zásad životnosti tokenů (Preview)
Můžete zadat dobu života přístupu, SAML nebo ID tokenu vydaného platformou Microsoft identity. Životnost tokenů je možné nastavit u všech aplikací ve vaší organizaci, u aplikace pro více tenantů nebo pro konkrétní objekt služby ve vaší organizaci. Další informace najdete v tématu [konfigurovatelné životnosti tokenů](active-directory-configurable-token-lifetimes.md).

V této části se seznámíte se společným scénářem zásad, který vám může pomoci stanovit nová pravidla pro dobu života tokenu. V tomto příkladu se dozvíte, jak vytvořit zásadu, která vyžaduje, aby se uživatelé ve vaší webové aplikaci ověřovali častěji.

## <a name="get-started"></a>Začínáme
Začněte tím, že provedete následující kroky:

1. Stáhněte si nejnovější [verzi modulu Azure AD PowerShell Public Preview](https://www.powershellgallery.com/packages/AzureADPreview).
1. Spuštěním `Connect` příkazu se přihlaste ke svému účtu správce Azure AD. Spusťte tento příkaz pokaždé, když spustíte novou relaci.

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. Chcete-li zobrazit všechny zásady, které byly vytvořeny ve vaší organizaci, spusťte rutinu [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) .  Všechny výsledky s definovanými hodnotami vlastností, které se liší od výše uvedených výchozích hodnot, jsou v rozsahu vyřazení.

    ```powershell
    Get-AzureADPolicy -All
    ```

1. Pokud chcete zjistit, které aplikace a instanční objekty jsou propojené s konkrétní zásadou, kterou jste zjistili, spusťte následující rutinu [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) nahrazením **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** pomocí libovolných identifikátorů vašich zásad. Pak se můžete rozhodnout, jestli chcete nakonfigurovat četnost přihlášení podmíněného přístupu, nebo zachovat výchozí nastavení služby Azure AD.

    ```powershell
    Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
    ```

Pokud má váš tenant zásady, které definují vlastní hodnoty vlastností konfigurace a tokenu relace, společnost Microsoft doporučuje tyto zásady aktualizovat na hodnoty, které odpovídají výchozím hodnotám uvedeným výše. Pokud se neprovede žádné změny, služba Azure AD automaticky použije výchozí hodnoty.

## <a name="create-a-policy-for-web-sign-in"></a>Vytvoření zásady pro webové přihlašování

V tomto příkladu vytvoříte zásadu, která vyžaduje, aby se uživatelé ve vaší webové aplikaci ověřovali častěji. Tato zásada nastavuje dobu života tokenů přístupu a ID a maximální stáří tokenu relace Multi-Factor Service k instančnímu objektu vaší webové aplikace.

1. Vytvořte zásady životnosti tokenů.

    Tato zásada, pro webové přihlašování, nastavuje dobu života tokenu přístupu/ID a maximální stáří tokenu relace s jedním faktorem na dvě hodiny.

    1. Pokud chcete vytvořit zásadu, spusťte rutinu [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Pokud chcete zobrazit nové zásady a získat **objectID** zásad, spusťte rutinu [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Přiřaďte zásadu k instančnímu objektu. Také je nutné získat **objectID** objektu služby.

    1. Pomocí rutiny [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) můžete zobrazit všechny instanční objekty vaší organizace nebo jeden instanční objekt.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Pokud máte instanční objekt, spusťte rutinu [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-token-lifetime-policies-for-refresh-and-session-tokens"></a>Vytvoření zásad životnosti tokenů pro aktualizace a tokeny relací
> [!IMPORTANT]
> Od května 2020 nemohou noví klienti konfigurovat dobu platnosti tokenů aktualizace a relace.  Klienti se stávající konfigurací můžou upravovat zásady aktualizace a tokenu relace až do 30. ledna 2021.  Azure Active Directory přestane dodržovat stávající konfiguraci tokenu aktualizace a tokenu relace v zásadách po 30. lednu 2021. Po vyřazení stále můžete nakonfigurovat přístup, SAML a životnosti tokenů ID.
>
> Pokud potřebujete pokračovat v definování časového období, než se uživateli zobrazí výzva k opětovnému přihlášení, nakonfigurujte četnost přihlášení v podmíněném přístupu. Pokud chcete získat další informace o podmíněném přístupu, přečtěte si téma [Konfigurace správy relací ověřování pomocí podmíněného přístupu](/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).
>
> Pokud nechcete používat podmíněný přístup po datu vyřazení, vaše tokeny aktualizace a relace budou nastavené na [výchozí konfiguraci](active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties-after-the-retirement) v tomto datu a už nebudete moct měnit jejich životnost.

### <a name="manage-an-organizations-default-policy"></a>Správa výchozích zásad organizace
V tomto příkladu vytvoříte zásadu, která umožňuje uživatelům méně často se přihlašovat napříč celou organizací. Provedete to tak, že vytvoříte zásadu životnosti tokenů pro tokeny aktualizace s jedním faktorem, který se používá v rámci vaší organizace. Zásady se aplikují na všechny aplikace ve vaší organizaci a na všechny instanční objekty, u kterých ještě není nastavená žádná sada zásad.

1. Vytvořte zásady životnosti tokenů.

    1. Nastavte token pro obnovení s jedním faktorem na "dokud neodvolávání". Platnost tokenu nevyprší, dokud nebude přístup odvolán. Vytvořte následující definici zásady:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Pokud chcete vytvořit zásadu, spusťte rutinu [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Pokud chcete odebrat jakékoli prázdné znaky, spusťte rutinu [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Pokud chcete zobrazit novou zásadu a získat **objectID** této zásady, spusťte následující příkaz:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Aktualizujte zásady.

    Můžete se rozhodnout, že první zásada, kterou jste nastavili v tomto příkladu, není tak striktní, jak vaše služba vyžaduje. Pokud chcete nastavit vypršení platnosti tokenu jednorázového obnovení do dvou dnů, spusťte následující příkaz:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Vytvoření zásady pro nativní aplikaci, která volá webové rozhraní API
V tomto příkladu vytvoříte zásadu, která vyžaduje, aby se uživatelé neověřovali méně často. Tato zásada také prodlouží dobu, po kterou může uživatel být neaktivní, než se uživatel musí znovu ověřit. Zásady se aplikují na webové rozhraní API. Když nativní aplikace požaduje webové rozhraní API jako prostředek, použije se tato zásada.

1. Vytvořte zásady životnosti tokenů.

    1. Pokud chcete vytvořit striktní zásadu pro webové rozhraní API, spusťte rutinu [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Pokud chcete zobrazit nové zásady, spusťte následující příkaz:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Přiřaďte zásady k webovému rozhraní API. Také je nutné získat **identifikátor objectID** vaší aplikace. Pomocí rutiny [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) Najděte **identifikátor objectID** vaší aplikace nebo použijte [Azure Portal](https://portal.azure.com/).

    Získejte **objectID** vaší aplikace a přiřaďte zásady:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="manage-an-advanced-policy"></a>Správa pokročilých zásad
V tomto příkladu vytvoříte několik zásad, abyste se dozvěděli, jak systém priorit funguje. Naučíte se také, jak spravovat víc zásad, které se aplikují na několik objektů.

1. Vytvořte zásady životnosti tokenů.

    1. Pokud chcete vytvořit výchozí zásadu pro organizaci, která nastaví životnost tokenu jednoho faktoru na 30 dní, spusťte rutinu [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Pokud chcete zobrazit novou zásadu, spusťte rutinu [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Přiřaďte zásadu k instančnímu objektu.

    Nyní máte zásadu, která se vztahuje na celou organizaci. Je možné, že budete chtít zachovat tuto 30denní zásadu pro určitý instanční objekt, ale změnit výchozí zásadu organizace na horní limit "dokud neodvolán".

    1. Pokud chcete zobrazit všechny instanční objekty vaší organizace, použijte rutinu [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) .

    1. Pokud máte instanční objekt, spusťte rutinu [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Nastavte `IsOrganizationDefault` příznak na false:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Vytvořit nové výchozí zásady pro organizaci:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Teď máte k dispozici původní zásady propojené s vaším instančním objektem a nové zásady se nastaví jako výchozí zásada vaší organizace. Je důležité si uvědomit, že zásady použité u instančních objektů mají přednost před výchozími zásadami organizace.

## <a name="next-steps"></a>Další kroky
Seznamte se s [možnostmi správy relace ověřování](../conditional-access/howto-conditional-access-session-lifetime.md) v podmíněném přístupu Azure AD.
