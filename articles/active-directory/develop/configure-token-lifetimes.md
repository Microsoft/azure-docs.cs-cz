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
ms.date: 09/29/2020
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: f70a11165f6433e580fd857f2d5a620deb6640c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91604243"
---
# <a name="configure-token-lifetime-policies-preview"></a>Konfigurace zásad životnosti tokenů (Preview)
V Azure AD můžete vytvářet a spravovat životnosti tokenů pro aplikace, instanční objekty a vaši celkovou organizaci. Pokud se chcete dozvědět víc, přečtěte si [konfigurovatelné životnosti tokenů v platformě Microsoft Identity Platform](active-directory-configurable-token-lifetimes.md). 

> [!IMPORTANT]
> Po slyšení od zákazníků ve verzi Preview jsme implementovali [Možnosti správy relace ověřování](../conditional-access/howto-conditional-access-session-lifetime.md) ve službě Azure AD podmíněný přístup. Tuto novou funkci můžete použít ke konfiguraci životností tokenů aktualizace nastavením frekvence přihlášení. Po 30. května 2020 nebude moct žádný nový tenant použít konfigurovatelné zásady životnosti tokenů ke konfiguraci relace a obnovení tokenů. K vyřazení dojde během několika měsíců, což znamená, že přestanou dodržovat existující relaci a aktualizovat zásady tokenů. Po vyřazení můžete i po vyřazení nakonfigurovat životnosti přístupového tokenu.


V této části si projdeme několik běžných scénářů zásad, které vám pomůžou stanovit nová pravidla pro:

* Životnost tokenu
* Maximální neaktivní čas tokenu
* Maximální stáří tokenu

V příkladech se můžete dozvědět, jak:

* Správa výchozích zásad organizace
* Vytvoření zásady pro webové přihlašování
* Vytvoření zásady pro nativní aplikaci, která volá webové rozhraní API
* Správa pokročilých zásad

## <a name="prerequisites"></a>Požadavky
V následujících příkladech můžete vytvořit, aktualizovat, propojit a odstranit zásady pro aplikace, instanční objekty a celou organizaci. Pokud s Azure AD teprve začínáte, doporučujeme vám seznámit se s tím, [Jak získat tenanta Azure AD](quickstart-create-new-tenant.md) , než budete pokračovat v těchto příkladech.  

Začněte tím, že provedete následující kroky:

1. Stáhněte si nejnovější [verzi modulu Azure AD PowerShell Public Preview](https://www.powershellgallery.com/packages/AzureADPreview).
2. Spuštěním `Connect` příkazu se přihlaste ke svému účtu správce Azure AD. Spusťte tento příkaz pokaždé, když spustíte novou relaci.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Chcete-li zobrazit všechny zásady, které byly vytvořeny ve vaší organizaci, spusťte následující příkaz. Spusťte tento příkaz po většině operací v následujících scénářích. Spuštění příkazu vám také pomůže získat * * * * vašich zásad.

    ```powershell
    Get-AzureADPolicy
    ```

## <a name="manage-an-organizations-default-policy"></a>Správa výchozích zásad organizace
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

    1. Pokud chcete zobrazit novou zásadu a získat **objectID**této zásady, spusťte následující příkaz:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Aktualizujte zásady.

    Můžete se rozhodnout, že první zásada, kterou jste nastavili v tomto příkladu, není tak striktní, jak vaše služba vyžaduje. Pokud chcete nastavit vypršení platnosti tokenu jednorázového obnovení do dvou dnů, spusťte následující příkaz:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

## <a name="create-a-policy-for-web-sign-in"></a>Vytvoření zásady pro webové přihlašování

V tomto příkladu vytvoříte zásadu, která vyžaduje, aby se uživatelé ve vaší webové aplikaci ověřovali častěji. Tato zásada nastavuje dobu života tokenů přístupu a ID a maximální stáří tokenu relace Multi-Factor Service k instančnímu objektu vaší webové aplikace.

1. Vytvořte zásady životnosti tokenů.

    Tato zásada, pro webové přihlašování, nastavuje dobu života tokenu přístupu/ID a maximální stáří tokenu relace s jedním faktorem na dvě hodiny.

    1. Pokud chcete vytvořit zásadu, spusťte rutinu [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Pokud chcete zobrazit nové zásady a získat **objectID**zásad, spusťte rutinu [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

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

## <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Vytvoření zásady pro nativní aplikaci, která volá webové rozhraní API
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

1. Přiřaďte zásady k webovému rozhraní API. Také je nutné získat **identifikátor objectID** vaší aplikace. Pomocí rutiny [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) Najděte **identifikátor objectID**vaší aplikace nebo použijte [Azure Portal](https://portal.azure.com/).

    Získejte **objectID** vaší aplikace a přiřaďte zásady:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

## <a name="manage-an-advanced-policy"></a>Správa pokročilých zásad
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