---
title: Konfigurace souhlasu vlastníka skupiny u aplikací, které přistupují k datům skupiny pomocí Azure AD
description: Naučte se spravovat, jestli můžou vlastníci skupiny a týmu souhlasit s aplikacemi, které budou mít přístup k datům skupiny nebo týmu.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 719bdd6a4d629bb4a35e7e11631670b0340e72a6
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090004"
---
# <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Konfigurace souhlasu vlastníka skupiny u aplikací, které přistupují k datům skupiny

Vlastníci skupiny a týmu můžou autorizovat aplikace, jako jsou například aplikace publikované dodavateli třetích stran, pro přístup k datům vaší organizace, které jsou přidružené ke skupině. Vlastník týmu v Microsoft Teams může například dovolit aplikaci číst všechny týmy v týmu nebo zobrazit základní profil členů skupiny. Další informace najdete v článku o [souhlasu týkajícím se konkrétního prostředku v Microsoft Teams](/microsoftteams/resource-specific-consent) .

## <a name="manage-group-owner-consent-to-apps"></a>Správa souhlasu vlastníka skupiny s aplikacemi

Můžete nakonfigurovat, kteří uživatelé mají povolen souhlas s aplikacemi, které přistupují k datům jejich skupin nebo týmů, nebo můžete tuto možnost zakázat pro všechny uživatele.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pomocí těchto kroků můžete spravovat souhlas vlastníka skupiny s přístupem k datům skupiny v aplikacích:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako [globální správce](../roles/permissions-reference.md#global-administrator).
2. Vyberte **Azure Active Directory**  >  souhlasu **podnikových aplikací**  >  **a**  >  **nastavení souhlasu uživatele**.
3. V části **souhlas vlastníka skupiny pro přístup k datům** vyberte možnost, kterou chcete povolit.
4. Vyberte **Uložit** a uložte nastavení.

V tomto příkladu můžou všichni vlastníci skupiny udělit souhlas s aplikacemi, které přistupují k datům jejich skupin:

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="Nastavení souhlasu vlastníka skupiny":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pomocí modulu Azure AD PowerShell Preview, [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)můžete povolit nebo zakázat vlastníkům skupin souhlas s aplikacemi, které přistupují k datům vaší organizace pro skupiny, které vlastní.

1. Ujistěte se, že používáte modul [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) . Tento krok je důležitý, pokud jste nainstalovali modul [AzureAD](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) i modul [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) ).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Připojte se k Azure AD PowerShellu.

   ```powershell
   Connect-AzureAD
   ```

1. Načte aktuální hodnotu nastavení adresáře pro **nastavení zásad souhlasu** ve vašem tenantovi. K tomu je potřeba zkontrolovat, jestli se nastavení adresáře pro tuto funkci vytvořilo, a pokud ne, použijte hodnoty z odpovídajících šablon nastavení adresáře.

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

1. Pochopení hodnot nastavení. K dispozici jsou dvě hodnoty nastavení, které definují, kteří uživatelé budou moci aplikaci umožnit přístup k datům svých skupin:

    | Nastavení       | Typ         | Description  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Logická hodnota | Příznak označující, zda mohou vlastníci skupiny udělit oprávnění pro konkrétní skupinu. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Identifikátor GUID | Pokud je _EnableGroupSpecificConsent_ nastavené na hodnotu "true" a tato hodnota je nastavená na ID objektu skupiny, budou se členové identifikované skupiny oprávněni udělit skupinám, které vlastní, oprávnění pro skupiny, které vlastní. |

1. Aktualizovat hodnoty nastavení pro požadovanou konfiguraci:

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

1. Uložte nastavení.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

---

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématech:

* [Konfigurovat nastavení souhlasu uživatele](configure-user-consent.md)
* [Konfigurace pracovního postupu pro vyjádření souhlasu správce](configure-admin-consent-workflow.md)
* [Naučte se spravovat souhlas s aplikacemi a hodnotit žádosti o souhlas.](manage-consent-requests.md)
* [Udělení souhlasu správce v rámci celého tenanta aplikaci](grant-admin-consent.md)
* [Oprávnění a souhlas na platformě Microsoft identity](../develop/v2-permissions-and-consent.md)

Pokud chcete získat nápovědu nebo najít odpovědi na své otázky:
* [Azure AD v Microsoft Q&A ](https://docs.microsoft.com/answers/topics/azure-active-directory.html)