---
title: Správa zásad pro vyjádření souhlasu s aplikacemi ve službě Azure AD
description: Naučte se spravovat předdefinované a vlastní zásady pro vyjádření souhlasu s aplikacemi, které určují, kdy je možné udělit souhlas.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 62a8b48d6b33a92b62bc4c3634794190585615b7
ms.sourcegitcommit: 983eb1131d59664c594dcb2829eb6d49c4af1560
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222016"
---
# <a name="manage-app-consent-policies"></a>Správa zásad souhlasu pro aplikace

Pomocí Azure AD PowerShellu můžete zobrazovat a spravovat zásady pro vyjádření souhlasu s aplikacemi.

Zásada pro vyjádření souhlasu aplikace se skládá z jedné nebo více sad podmínek include a nula nebo více sad podmínek "vyloučení". Pro událost, která se má považovat za zásadu souhlasu aplikace, se musí shodovat *aspoň* jedna sada podmínek include a nesmí odpovídat *žádné* sadě podmínek "vyloučení".

Každá sada podmínek se skládá z několika podmínek. Aby se událost shodovala s nastavenou podmínkou, musí být splněné *všechny* podmínky v sadě podmínek.

Zásady pro vyjádření souhlasu aplikace, kde ID začíná řetězcem "Microsoft-", jsou předdefinované zásady. Některé z těchto předdefinovaných zásad se používají ve stávajících integrovaných rolích adresáře. Například `microsoft-application-admin` zásada pro vyjádření souhlasu aplikace popisuje podmínky, za kterých mají role správce aplikace a správce cloudové aplikace povolený souhlas správců v rámci tenanta. Předdefinované zásady je možné použít v rolích vlastních adresářů a konfigurovat nastavení souhlasu uživatele, ale nelze je upravovat ani odstraňovat.

## <a name="pre-requisites"></a>Požadavky

1. Ujistěte se, že používáte modul [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) . Tento krok je důležitý, pokud jste nainstalovali modul [AzureAD](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) i modul [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) ).

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Připojte se k Azure AD PowerShellu.

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>Zobrazit seznam existujících zásad pro vyjádření souhlasu s aplikacemi

Je vhodné začít tím, že se seznámíte se stávajícími zásadami pro vyjádření souhlasu s aplikacemi ve vaší organizaci:

1. Vypsat všechny zásady pro vyjádření souhlasu s aplikacemi:

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. Zobrazit sady zásad, které obsahují podmínky:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. Zobrazit množiny podmínek vyloučení:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>Vytvoření vlastní zásady pro vyjádření souhlasu s aplikací

Pomocí těchto kroků můžete vytvořit vlastní zásadu pro vyjádření souhlasu s aplikací:

1. Vytvořte novou prázdnou zásadu pro vyjádření souhlasu s aplikací.

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. Přidejte sady podmínek includes.

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   Opakováním tohoto kroku přidejte další sady podmínek "include".

1. Volitelně můžete přidat sady podmínek "vyloučení".

   ```powershell
   # Retrieve the service principal for the Azure Management API
   $azureApi = Get-AzureADServicePrincipal -Filter "servicePrincipalNames/any(n:n eq 'https://management.azure.com/')"

   # Exclude delegated permissions for the Azure Management API
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "excludes" `
       -PermissionType "delegated" `
       -ResourceApplication $azureApi.AppId
   ```

   Opakováním tohoto kroku přidejte další sady podmínek "vyloučit".

Po vytvoření zásady pro vyjádření souhlasu s aplikací můžete této zásadě [udělit souhlas s uživatelem](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy) .

## <a name="delete-a-custom-app-consent-policy"></a>Odstraní vlastní zásadu pro vyjádření souhlasu s aplikací.

1. Níže vidíte, jak můžete odstranit vlastní zásadu pro vyjádření souhlasu s aplikací. **Tuto akci nejde vrátit zpátky.**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> Odstraněné zásady souhlasu aplikace nelze obnovit. Pokud vlastní zásadu pro vyjádření souhlasu s aplikací omylem odstraníte, bude nutné zásadu znovu vytvořit.

---

### <a name="supported-conditions"></a>Podporované podmínky

Následující tabulka uvádí seznam podporovaných podmínek pro zásady pro vyjádření souhlasu s aplikacemi.

| Podmínka | Description|
|:---------------|:----------|
| PermissionClassification | [Klasifikace oprávnění](configure-permission-classifications.md) pro udělená oprávnění nebo "vše", aby odpovídala libovolné klasifikaci oprávnění (včetně oprávnění, která nejsou klasifikována). Výchozí hodnota je All (vše). |
| PermissionType | Typ oprávnění uděleného oprávnění Pro delegovaná oprávnění použijte "Application" pro oprávnění aplikací (např. role aplikace) nebo delegovaný. <br><br>**Poznámka**: hodnota "delegatedUserConsentable" označuje delegovaná oprávnění, která nenakonfiguroval Vydavatel rozhraní API, aby vyžadovala souhlas správce. Tato hodnota se dá použít v předdefinovaných zásadách udělení oprávnění, ale nedá se použít v zásadách udělení vlastních oprávnění. Povinná hodnota. |
| ResourceApplication | **AppID** aplikace prostředků (např. rozhraní API), pro kterou se uděluje oprávnění, nebo "any", aby odpovídala libovolné aplikaci prostředků nebo rozhraní API. Výchozí hodnota je Any. |
| Oprávnění | Seznam identifikátorů oprávnění pro konkrétní oprávnění, se kterými se shodují, nebo seznam s jednou hodnotou All, která se má shodovat s libovolnými oprávněními Výchozí je jediná hodnota all. <ul><li>Delegovaná ID oprávnění se dají najít ve vlastnosti **OAuth2Permissions** objektu ServicePrincipal rozhraní API.</li><li>ID oprávnění aplikace najdete ve vlastnosti **AppRoles** objektu ServicePrincipal rozhraní API.</li></ol> |
| ClientApplicationIds | Seznam hodnot **AppID** pro klientské aplikace, které se shodují s, nebo seznam s jednou hodnotou All, aby odpovídala všem klientským aplikacím. Výchozí je jediná hodnota all. |
| ClientApplicationTenantIds | Seznam Azure Active Directory ID klientů, ve kterých je klientská aplikace zaregistrovaná, nebo seznam s jednou hodnotou All, která se má shodovat s klientskými aplikacemi registrovanými v jakémkoli tenantovi. Výchozí je jediná hodnota all. |
| ClientApplicationPublisherIds | Seznam ID Microsoft Partner Network (MPN) pro [ověřené vydavatele](../develop/publisher-verification-overview.md) klientské aplikace nebo seznam s jednou hodnotou All (vše), který se bude shodovat s klientskými aplikacemi od libovolného vydavatele. Výchozí je jediná hodnota all. |
| ClientApplicationsFromVerifiedPublisherOnly | Nastavte, aby `$true` odpovídaly pouze klientským aplikacím [ověřeným vydavatelům](../develop/publisher-verification-overview.md). Nastavte `$false` , aby odpovídal všem klientským aplikacím, a to i v případě, že nemá ověřeného vydavatele. Výchozí je `$false`. |

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématech:

* [Konfigurovat nastavení souhlasu uživatele](configure-user-consent.md)
* [Konfigurace pracovního postupu pro vyjádření souhlasu správce](configure-admin-consent-workflow.md)
* [Naučte se spravovat souhlas s aplikacemi a hodnotit žádosti o souhlas.](manage-consent-requests.md)
* [Udělení souhlasu správce v rámci celého tenanta aplikaci](grant-admin-consent.md)
* [Oprávnění a souhlas na platformě Microsoft identity](../develop/v2-permissions-and-consent.md)

Pokud chcete získat nápovědu nebo najít odpovědi na své otázky:
* [Azure AD v Microsoft Q&A](https://docs.microsoft.com/answers/products/)