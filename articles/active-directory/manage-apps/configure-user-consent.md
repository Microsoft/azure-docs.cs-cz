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
ms.openlocfilehash: 42337fe958a881ee263d16c866dda69f13fe09c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519612"
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
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Identifikátor GUID | Pokud je _EnableGroupSpecificConsent_ nastavené na hodnotu "true" a tato hodnota je nastavená na ID objektu skupiny, budou se členové skupiny, kterým se identifikovali, autorizovat, aby pro skupiny, které vlastní, udělila oprávnění pro konkrétní skupiny. |

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

## <a name="configure-risk-based-step-up-consent"></a>Konfigurace souhlasu pro krokování na základě rizika

Souhlasu krokování na základě rizika pomáhá omezit vystavení uživatelů škodlivým aplikacím, které provádějí [nedovolené žádosti o souhlas](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants). Pokud společnost Microsoft zjistí rizikové žádosti o souhlas koncového uživatele, bude místo toho vyžadovat "krokování" na základě souhlasu správce. Tato funkce je ve výchozím nastavení povolená, ale při povolení souhlasu koncového uživatele bude mít za následek změnu chování.

Při zjištění rizikové žádosti o souhlas se zobrazí výzva k zadání souhlasu s oznámením, že je požadováno schválení správcem. Pokud je povolen [pracovní postup žádosti o souhlas správce](configure-admin-consent-workflow.md) , může uživatel odeslat požadavek správci pro další kontrolu přímo z výzvy k zadání souhlasu. Pokud tato možnost není povolená, zobrazí se následující zpráva:

* **AADSTS90094:** &lt;clientAppDisplayName&gt; potřebuje oprávnění pro přístup k prostředkům ve vaší organizaci, které může udělit jenom správce. Please ask an admin to grant permission to this app before you can use it. (Test udělení souhlasu vyžaduje ve vaší organizaci pro přístup k prostředkům oprávnění, které může udělit pouze správce. Než budete moct tuto aplikaci použít, požádejte správce o udělení oprávnění.)

V takovém případě se událost auditu bude protokolovat jako kategorie "ApplicationManagement", typ aktivity "souhlas s aplikací" a důvod stavu "riziková aplikace zjištěná".

> [!IMPORTANT]
> Správci by měli před schválením pečlivě [vyhodnotit všechny žádosti o souhlas](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) , zejména když společnost Microsoft zjistila riziko.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Zakázání nebo opětovné povolení souhlasu na základě rizik pomocí PowerShellu

Můžete použít modul Azure AD PowerShell Preview ([AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)), chcete-li zakázat krok k souhlasu správce, který je vyžadován v případech, kdy Microsoft zjistí riziko nebo ho znovu povolí, pokud byl dříve zakázán.

To lze provést pomocí stejných kroků, jak je uvedeno výše pro [konfiguraci souhlasu vlastníka skupiny pomocí prostředí PowerShell](#configure-group-owner-consent-using-powershell), ale nahrazením jiné hodnoty nastavení. Existují tři rozdíly v krocích: 

1. Seznamte se s hodnotami nastavení pro udělení souhlasu na základě rizik:

    | Nastavení       | Typ         | Popis  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Logická hodnota |  Příznak označující, zda bude při zjištění rizikové žádosti zablokován souhlas uživatele |

2. V kroku 3 nahraďte následující hodnotu:

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
3. V kroku 5 nahraďte jednu z následujících akcí:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>Další kroky

[Konfigurace pracovního postupu pro vyjádření souhlasu správce](configure-admin-consent-workflow.md)

[Naučte se spravovat souhlas s aplikacemi a hodnotit žádosti o souhlas.](manage-consent-requests.md)

[Udělení souhlasu správce na úrovni tenanta pro aplikaci](grant-admin-consent.md)

[Oprávnění a souhlas na platformě Microsoft identity](../develop/active-directory-v2-scopes.md)

[Azure AD v StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
