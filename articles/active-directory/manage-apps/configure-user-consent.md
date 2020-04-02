---
title: Konfigurace způsobu, jakým koncoví uživatelé uzaměňují souhlas s aplikacemi používajícími Azure AD
description: Přečtěte si, jak spravovat, jak a kdy mohou uživatelé souhlasit s aplikacemi, které budou mít přístup k datům vaší organizace.
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
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519612"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Konfigurace způsobu, jakým koncoví uživatelé uzaměňují souhlas s aplikacemi

Aplikace se můžou integrovat s platformou Microsoft Identity, aby se uživatelé mohli přihlásit pomocí svého pracovního nebo školního účtu ve službě Azure Active Directory (Azure AD) a získat přístup k datům vaší organizace za účelem poskytování bohatých možností založených na datech. Různá oprávnění umožňují aplikaci různou úroveň přístupu k datům uživatelů a vaší organizace.

Ve výchozím nastavení mohou uživatelé souhlasit s aplikacemi, které přistupují k datům vaší organizace, i když pouze pro některá oprávnění. Ve výchozím nastavení může uživatel například souhlasit s tím, aby aplikace mohla přistupovat k vlastní poštovní schránce nebo k konverzacím teams pro tým, který uživatel vlastní, ale nemůže souhlasit s povolením bezobslužného přístupu aplikace ke čtení a zápisu na všechny sharepointové weby ve vaší organizaci. I když umožňuje uživatelům souhlas sám umožňuje uživatelům snadno získat užitečné aplikace, které integrují s Microsoft 365, Azure a další služby, může představovat riziko, pokud nejsou používány a pečlivě sledovány.

Společnost Microsoft doporučuje zakázat budoucí operace se souhlasem uživatele, aby se snížila plocha povrchu a zmírnilo toto riziko. Pokud je souhlas uživatele zakázán, předchozí udělení souhlasu bude stále dodrženo, ale všechny budoucí operace souhlasu musí být provedeny správcem. Souhlas správce pro celý klient mohou uživatelé požadovat prostřednictvím [integrovaného pracovního postupu žádosti o souhlas správce](configure-admin-consent-workflow.md) nebo prostřednictvím vlastních procesů podpory. Další podrobnosti najdete [v pěti krocích zabezpečení infrastruktury identit.](../../security/fundamentals/steps-secure-identity.md)

## <a name="configure-user-consent-to-applications"></a>Konfigurace souhlasu uživatele s aplikacemi
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Zakázání nebo povolení souhlasu uživatele z webu Azure Portal

Na webu Azure Portal můžete zakázat nebo povolit uživatelům souhlas s aplikacemi, které přistupují k datům vaší organizace:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako [globální správce](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Vyberte **Azure Active Directory**, pak podnikové **aplikace**a potom **nastavení uživatele**.
3. Povolení nebo zakázání souhlasu uživatele s ovládacím prvkem označeným **uživatelé mohou souhlasit s tím, aby aplikace přistupovaly k firemním datům jejich jménem**.
4. (Nepovinné) Nakonfigurujte [pracovní postup žádosti o souhlas správce,](configure-admin-consent-workflow.md) abyste zajistili, že uživatelé, kterým není povolen souhlas s aplikací, mohou požádat o schválení.

> [!TIP]
> Chcete-li uživatelům umožnit požádat správce o kontrolu aplikace, se kterou uživatel nemůže souhlasit (například proto, že byl zakázán souhlas uživatele nebo protože aplikace požaduje oprávnění, která uživatel nemůže udělit), [zvažte konfiguraci pracovního postupu souhlasu správce](configure-admin-consent-workflow.md).

### <a name="disable-or-enable-user-consent-using-powershell"></a>Zakázání nebo povolení souhlasu uživatele pomocí PowerShellu

Pomocí modulu Azure AD PowerShell v1 ([MSOnline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)) můžete povolit nebo zakázat uživatelům možnost souhlasu s aplikacemi, které přistupují k datům vaší organizace.

1. Přihlaste se k organizaci spuštěním této rutiny:

    ```powershell
    Connect-MsolService
    ```

2. Zkontrolujte, zda je souhlas uživatele povolen spuštěním této rutiny:

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. Povolení nebo zakázání souhlasu uživatele. Chcete-li například zakázat souhlas uživatele, spusťte tuto rutinu:

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Konfigurace souhlasu vlastníka skupiny s aplikacemi přistupujícími k datům skupiny

> [!IMPORTANT]
> Následující informace jsou určeny pro nadcházející funkci, která vlastníkům skupin umožní udělit aplikacím přístup k datům svých skupin. Po uvolnění této funkce bude ve výchozím nastavení povolena. Přestože tato funkce ještě není vydána široce, můžete tuto instruktou funkci před vydáním zakázat.

Vlastníci skupiny mohou povolit aplikacím (například aplikacím publikovaným dodavateli třetích stran) přístup k datům vaší organizace přidruženým ke skupině. Například vlastník týmu (který je vlastníkem skupiny Office 365 pro tým) může aplikaci povolit číst všechny zprávy Teams v týmu nebo uvést základní profil členů skupiny.

> [!NOTE]
> Nezávisle na tomto nastavení může vlastník skupiny vždy přidávat další uživatele nebo aplikace přímo jako vlastníky skupiny.

### <a name="configure-group-owner-consent-using-powershell"></a>Konfigurace souhlasu vlastníka skupiny pomocí PowerShellu

Pomocí modulu Azure AD PowerShell Preview[(AzureADPreview)](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)můžete povolit nebo zakázat možnost vlastníků skupinu udělit souhlas s aplikacemi, které přistupují k datům vaší organizace pro skupiny, které vlastní.

1. Ujistěte se, že používáte modul [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) (tento krok je důležitý, pokud jste nainstalovali modul [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) i modul [AzureADPreview).](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Připojte se k Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

3. Načtěte aktuální hodnotu nastavení adresáře *Nastavení zásad souhlasu* v tenantovi. To vyžaduje kontrolu, zda bylo vytvořeno nastavení adresáře pro tuto funkci, a pokud ne, pomocí hodnot z odpovídající šablony nastavení adresáře.

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

4. Seznamte se s hodnotami nastavení. Existují dvě hodnoty nastavení, které definují, kteří uživatelé by mohli aplikaci povolit přístup k datům své skupiny:

    | Nastavení       | Typ         | Popis  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Logická hodnota |  Příznak označující, zda vlastníci skupin mohou udělit oprávnění specifická pro skupinu. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Identifikátor GUID | Pokud _EnableGroupSpecificConsent_ je nastavena na "True" a tato hodnota nastavena na ID objektu skupiny, členové skupiny určené budou oprávněni udělit oprávnění specifické pro skupinu skupiny, které vlastní. |

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

## <a name="configure-risk-based-step-up-consent"></a>Konfigurace souhlasu s posílením na základě rizika

Zpřísňování souhlasu založeného na rizicích pomáhá snížit vystavení uživatelů škodlivým aplikacím, které žádají [o nezákonný souhlas](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants). Pokud společnost Microsoft zjistí riskantní žádost o souhlas koncového uživatele, bude žádost vyžadovat "step-up" na souhlas správce místo. Tato funkce je ve výchozím nastavení povolena, ale bude mít za následek změnu chování pouze v případě, že je povolen souhlas koncového uživatele.

Pokud je zjištěna neriziková žádost o souhlas, zobrazí se v výzvě k udělení souhlasu zpráva s oznámením, že je potřeba schválení správcem. Pokud je [pracovní postup žádosti o souhlas správce](configure-admin-consent-workflow.md) povolen, může uživatel odeslat žádost správci k dalšímu přezkoumání přímo z výzvy k souhlasu. Pokud není povolena, zobrazí se následující zpráva:

* **AADSTS90094:** &lt;clientAppDisplayName&gt; potřebuje oprávnění pro přístup k prostředkům ve vaší organizaci, které může udělit pouze správce. Please ask an admin to grant permission to this app before you can use it. (Test udělení souhlasu vyžaduje ve vaší organizaci pro přístup k prostředkům oprávnění, které může udělit pouze správce. Než budete moct tuto aplikaci použít, požádejte správce o udělení oprávnění.)

V takovém případě bude auditová událost také zaznamenána s kategorií "ApplicationManagement", Typem aktivity "Souhlas s aplikací" a důvodem stavu "Zjištěna riziková aplikace".

> [!IMPORTANT]
> Správci by měli před schválením pečlivě [vyhodnotit všechny žádosti o souhlas,](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) zejména pokud společnost Microsoft zjistila riziko.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Zakázání nebo opětovné povolení stupňovacího souhlasu založeného na rizicích pomocí prostředí PowerShell

Pomocí modulu Azure AD PowerShell Preview[(AzureADPreview)](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)můžete zakázat souhlas správce požadovaný v případech, kdy Microsoft zjistí riziko, nebo ho znovu povolit, pokud byl dříve zakázán.

To lze provést pomocí stejných kroků, jak je uvedeno výše pro [konfiguraci souhlasu vlastníka skupiny pomocí prostředí PowerShell](#configure-group-owner-consent-using-powershell), ale nahrazení jinou hodnotu nastavení. Existují tři rozdíly v krocích: 

1. Seznamte se s hodnotami nastavení pro souhlas s posílením na základě rizik:

    | Nastavení       | Typ         | Popis  |
    | ------------- | ------------ | ------------ |
    | _Aplikace BlockUserConsentForRiskyApps_   | Logická hodnota |  Příznak označující, zda bude souhlas uživatele blokován, když je zjištěn a riskantní požadavek. |

2. V kroku 3 nahraďte následující hodnotu:

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
3. V kroku 5 nahraďte jednu z následujících možností:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>Další kroky

[Konfigurace pracovního postupu souhlasu správce](configure-admin-consent-workflow.md)

[Přečtěte si, jak spravovat souhlas s žádostmi a vyhodnocovat žádosti o souhlas](manage-consent-requests.md)

[Udělení souhlasu správce pro celý klient s aplikací](grant-admin-consent.md)

[Oprávnění a souhlas v platformě microsoft identity](../develop/active-directory-v2-scopes.md)

[Azure AD na StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
