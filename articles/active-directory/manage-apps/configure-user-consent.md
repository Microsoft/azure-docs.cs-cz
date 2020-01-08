---
title: Konfigurace způsobu souhlasu koncových uživatelů s aplikacemi pomocí Azure AD
description: Naučte se, jak spravovat, jak a kdy si uživatelé můžou udělit souhlas s aplikacemi, které budou mít přístup k datům vaší organizace.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd305d2943d1b12756171748f28d32300081d71
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443389"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Konfigurace způsobu souhlasu koncových uživatelů s aplikacemi

Aplikace se můžou integrovat s platformou Microsoft identity, aby se uživatelé mohli přihlašovat pomocí svého pracovního nebo školního účtu v Azure Active Directory (Azure AD) a přistupovat k datům vaší organizace, aby mohli poskytovat rozsáhlá prostředí založená na datech. Různá oprávnění umožňují aplikaci různé úrovně přístupu k datům vašich uživatelů a vaší organizace.

Ve výchozím nastavení můžou uživatelé vyjádřit souhlas s aplikacemi, které přistupují k datům vaší organizace, i když jenom pro některá oprávnění. Ve výchozím nastavení může uživatel například udělit souhlas s tím, že aplikaci umožní přístup ke své vlastní poštovní schránce nebo týmu, kteří vlastní, ale nemůžou vyjádřit souhlas s tím, že umožňuje přístup ke čtení a zápisu na všechny weby SharePoint ve vaší organizaci pomocí bezobslužného přístupu. I když uživatelům, kteří si můžou udělit souhlas sami, umožní uživatelům snadno získat užitečné aplikace, které se integrují s Microsoft 365, Azure a dalšími službami, může představovat riziko, pokud se nepoužije a pečlivě monitoruje.

Společnost Microsoft doporučuje zakázat budoucí operace souhlasu s uživatelem, aby se snížila plocha a zmírnila toto riziko. Pokud je souhlas uživatele zakázaný, bude se i nadále akceptovat předchozí granty souhlasu, ale všechny budoucí operace souhlasu musí udělat správce. Souhlas správce na úrovni tenanta můžou vyžádat uživatelé prostřednictvím [pracovního postupu integrovaných žádostí o souhlas správce](configure-admin-consent-workflow.md) nebo prostřednictvím vlastních procesů podpory. Další podrobnosti najdete v [pěti krocích k zabezpečení infrastruktury identity](../../security/fundamentals/steps-secure-identity.md) .

## <a name="configure-user-consent-to-applications"></a>Konfigurace souhlasu uživatele s aplikacemi
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Zakázání nebo povolení souhlasu uživatele z Azure Portal

Pomocí Azure Portal můžete zakázat nebo povolit uživatelům souhlas s aplikacemi, které přistupují k datům vaší organizace:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako [globální správce](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Vyberte **Azure Active Directory**, pak **podnikové aplikace**a pak **uživatelské nastavení**.
3. Povolení nebo zakázání souhlasu uživatele s ovládacím prvkem označený **Uživatelé můžou udělit souhlas aplikacím, které přistupují k firemním datům**jménem.
4. Volitelné Nakonfigurujte [pracovní postup žádosti o souhlas správce](configure-admin-consent-workflow.md) , abyste zajistili, že uživatelé, kteří nejsou oprávněni souhlasit s aplikací, můžou požádat o schválení.

> [!TIP]
> Pokud chcete uživatelům povolit, aby si vyžádali kontrolu aplikace, na kterou uživatel nemá povoleno vyjádřit souhlas (například kvůli tomu, že uživatel zakázal souhlas s uživatelem nebo protože aplikace požaduje oprávnění, která uživatel nemá povoleno udělit), zvažte možnost [Konfigurace pracovního postupu souhlasu správce](configure-admin-consent-workflow.md).

### <a name="disable-or-enable-user-consent-using-powershell"></a>Zakázání nebo povolení souhlasu uživatele pomocí PowerShellu

Můžete použít modul Azure AD PowerShell V1 ([MSOnline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)), chcete-li povolit nebo zakázat uživatelům souhlas s aplikacemi, které přistupují k datům vaší organizace.

1. Přihlaste se ke svojí organizaci spuštěním této rutiny:

    ```powershell
    Connect-MsolService
    ```

2. Spuštěním této rutiny ověřte, jestli je povolený souhlas uživatele:

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. Povolí nebo zakáže souhlas uživatele. Pokud například chcete zakázat souhlas s uživatelem, spusťte tuto rutinu:

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Konfigurace souhlasu vlastníka skupiny u aplikací, které přistupují k datům skupiny

> [!IMPORTANT]
> Následující informace jsou pro nadcházející funkci, která vlastníkům skupiny umožní udělit aplikacím přístup k datům jejich skupin. Když se tato funkce uvolní, bude ve výchozím nastavení povolená. I když tato funkce ještě není široce vydaná, můžete tyto pokyny použít k tomu, abyste funkci před vydáním vypnuli.

Vlastníci skupiny můžou autorizovat aplikace (například aplikace publikované dodavateli třetích stran), aby měli přístup k datům vaší organizace, které jsou přidružené ke skupině. Například vlastník týmu (, který je vlastníkem skupiny Office 365 pro tým), může aplikaci dovolit číst všechny týmy v týmu nebo zobrazit základní profil členů skupiny.

> [!NOTE]
> Nezávisle na tomto nastavení je vlastník skupiny vždycky povolený přidávat další uživatele nebo aplikace přímo jako vlastníci skupiny.

### <a name="configure-group-owner-consent-using-powershell"></a>Konfigurace souhlasu vlastníka skupiny pomocí PowerShellu

Můžete použít modul Azure AD PowerShell Preview ([AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)), chcete-li povolit nebo zakázat vlastníkům skupin souhlas s aplikacemi, které přistupují k datům vaší organizace pro skupiny, které vlastní.

1. Ujistěte se, že používáte modul [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) (Tento krok je důležitý, pokud jste nainstalovali modul [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) i modul [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) ).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Připojte se k Azure AD PowerShellu.

   ```powershell
   Connect-AzureAD
   ```

3. Načte aktuální hodnotu nastavení adresáře pro *nastavení zásad souhlasu* ve vašem tenantovi. K tomu je potřeba zkontrolovat, jestli se nastavení adresáře pro tuto funkci vytvořilo, a pokud ne, použijte hodnoty z odpovídajících šablon nastavení adresáře.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

4. Pochopení hodnot nastavení. K dispozici jsou dvě hodnoty nastavení, které definují, kteří uživatelé budou moci aplikaci umožnit přístup k datům svých skupin:

    | Nastavení       | Typ         | Popis  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Logická hodnota |  Příznak označující, zda mohou vlastníci skupiny udělit oprávnění pro konkrétní skupinu. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | Pokud je _EnableGroupSpecificConsent_ nastavené na hodnotu "true" a tato hodnota je nastavená na ID objektu skupiny, budou se členové skupiny, kterým se identifikovali, autorizovat, aby pro skupiny, které vlastní, udělila oprávnění pro konkrétní skupiny. |

5. Aktualizovat hodnoty nastavení pro požadovanou konfiguraci:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

6. Uložit nastavení.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="next-steps"></a>Další kroky

[Konfigurace pracovního postupu pro vyjádření souhlasu správce](configure-admin-consent-workflow.md)

[Udělení souhlasu správce na úrovni tenanta pro aplikaci](grant-admin-consent.md)

[Oprávnění a souhlas na platformě Microsoft identity](../develop/active-directory-v2-scopes.md)

[Azure AD v StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
