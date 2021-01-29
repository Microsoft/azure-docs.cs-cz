---
title: Konfigurace způsobu souhlasu koncových uživatelů s aplikacemi pomocí Azure AD
description: Naučte se, jak spravovat, jak a kdy si uživatelé můžou udělit souhlas s aplikacemi, které budou mít přístup k datům vaší organizace.
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
ms.openlocfilehash: 184263badc909881f597213a222272bd675fc362
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051970"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Konfigurace způsobu vyjadřování souhlasu koncových uživatelů s aplikacemi

Své aplikace můžete integrovat s platformou Microsoft identity, aby se uživatelé mohli přihlašovat pomocí svého pracovního nebo školního účtu a přistupovat k datům vaší organizace, aby mohli poskytovat rozsáhlá prostředí založená na datech.

Předtím, než aplikace získá přístup k datům vaší organizace, musí uživateli udělit oprávnění aplikace. Různá oprávnění umožňují různé úrovně přístupu. Ve výchozím nastavení mohou všichni uživatelé udělit souhlas s aplikacemi oprávnění, které nevyžadují souhlas správce. Ve výchozím nastavení může uživatel například udělit souhlas s tím, že aplikaci má přístup ke své poštovní schránce, ale nemůže udělit souhlas, aby aplikace neomezený přístup ke čtení a zápisu do všech souborů ve vaší organizaci.

Díky tomu, že uživatelé můžou aplikacím udělit přístup k datům, můžou snadno získat užitečné aplikace a být produktivní. V některých případech však tato konfigurace může představovat riziko, pokud není sledována a kontrolována pečlivě.

> [!IMPORTANT]
> Chcete-li snížit riziko, že se škodlivé aplikace pokoušejí uživatelům získat přístup k datům vaší organizace, doporučujeme udělit souhlas uživateli pouze pro aplikace, které byly publikovány [ověřeným vydavatelem](../develop/publisher-verification-overview.md).

## <a name="user-consent-settings"></a>Nastavení souhlasu uživatele

Zásady pro vyjádření souhlasu aplikace popisují podmínky, které musí být splněné před tím, než může být aplikace poslána. Tyto zásady můžou zahrnovat podmínky pro aplikaci požadující přístup a také oprávnění, která aplikace požaduje.

Výběrem zásad pro vyjádření souhlasu s aplikacemi pro všechny uživatele můžete nastavit omezení, pokud koncovým uživatelům povolíte udělit souhlas aplikacím a kdy budou muset požádat o revizi a schválení správce:

* **Zakázat souhlas uživatele** – uživatelé nemůžou udělovat oprávnění aplikacím. Uživatelé se mohou nadále přihlašovat k aplikacím, které dříve poslaly nebo které jim byly odsouhlaseny jménem správců, ale nebudou jim dovoleni souhlasit s novými oprávněními ani s novými aplikacemi. Pouze uživatelé, kterým byla udělena role adresáře, která zahrnuje oprávnění udělit souhlas, budou moci vyjádřit souhlas s novými aplikacemi.

* **Uživatelé můžou udělit souhlas s aplikacemi od ověřených vydavatelů nebo vaší organizace, ale jenom pro oprávnění** , která vyberete – všichni uživatelé můžou souhlasit jenom s aplikacemi, které publikoval [ověřený vydavatel](../develop/publisher-verification-overview.md) a aplikace, které jsou zaregistrované ve vašem tenantovi. Uživatelé můžou odsouhlasit jenom oprávnění, která jste klasifikováni jako "malý dopad". Musíte [klasifikovat oprávnění](configure-permission-classifications.md) , abyste mohli vybrat, která oprávnění uživatelé můžou udělit souhlas.

* **Uživatelé můžou udělit souhlas se všemi aplikacemi** – Tato možnost umožňuje všem uživatelům udělit souhlas s oprávněním, která pro každou aplikaci nevyžadují souhlas správce.

* **Vlastní zásady pro vyjádření souhlasu s aplikacemi** – pro další možnosti, které se týkají souhlasu uživatele, můžete [vytvořit vlastní zásadu pro souhlas s aplikacemi](manage-app-consent-policies.md#create-a-custom-app-consent-policy)a nakonfigurovat, aby se projevily pro souhlas uživatele.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Konfigurace nastavení souhlasu uživatele prostřednictvím Azure Portal:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako [globální správce](../roles/permissions-reference.md#global-administrator--company-administrator).
1. Vyberte **Azure Active Directory**  >  souhlasu **podnikových aplikací**  >  **a**  >  **nastavení souhlasu uživatele**.
1. V části **souhlas uživatele u aplikací** vyberte, které nastavení souhlasu se má nakonfigurovat pro všechny uživatele.
1. Vyberte **Uložit** a uložte nastavení.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Nastavení souhlasu uživatele":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Můžete použít nejnovější modul Azure AD PowerShell Preview [AzureADPreview](/powershell/azure/active-directory/install-adv2?preserve-view=true&view=azureadps-2.0-preview)a vybrat, které zásady souhlasu aplikací řídí souhlas uživatele pro aplikace.

#### <a name="disable-user-consent"></a>Zakázat souhlas uživatele

Pokud chcete zakázat souhlas s uživatelem, nastavte zásady souhlasu, které budou řídit souhlas uživatele v prázdném prostředí:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>Povolení souhlasu uživatele v souladu se zásadami souhlasu aplikace

Pokud chcete povolit souhlas s uživatelem, vyberte, které zásady souhlasu aplikace by se měly řídit autorizací uživatelů pro udělení souhlasu aplikacím:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

Nahraďte `{consent-policy-id}` ID zásady, kterou chcete použít. Můžete zvolit [vlastní zásadu souhlasu s aplikací](manage-app-consent-policies.md#create-a-custom-app-consent-policy) , kterou jste vytvořili, nebo si můžete vybrat z následujících předdefinovaných zásad:

| ID | Description |
|:---|:------------|
| Microsoft – uživatel – výchozí – nízká | **Povoluje uživateli souhlas s vybranými oprávněními pro aplikace od ověřených vydavatelů.**<br /> Povoluje omezený souhlas s uživatelem jenom pro aplikace od ověřených vydavatelů a aplikací registrovaných ve vašem tenantovi a jenom pro oprávnění, která klasifikujete jako "nízký dopad". (Nezapomeňte [klasifikovat oprávnění](configure-permission-classifications.md) a vybrat, která oprávnění uživatelé můžou udělit souhlas.) |
| Microsoft – uživatel – výchozí – starší verze | **Povolení souhlasu uživatele pro aplikace**<br /> Tato možnost umožňuje všem uživatelům udělit souhlas s jakýmkoli oprávněním, které nevyžaduje souhlas správce, pro žádnou aplikaci. |
  
Pokud například chcete povolit souhlas uživatele v souladu s předdefinovanými zásadami `microsoft-user-default-low` :

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> [Povolte pracovní postup souhlasu správce](configure-admin-consent-workflow.md) a Umožněte uživatelům požádat o revizi a schválení aplikace správce, které uživatel nemá oprávněně vyjádřit souhlas – například když je souhlas uživatele zakázán nebo když aplikace požaduje oprávnění, která uživatel nemá povoleno udělit.

## <a name="risk-based-step-up-consent"></a>Souhlas se krokem na základě rizika

Souhlasu krokování na základě rizika pomáhá omezit vystavení uživatelů škodlivým aplikacím, které by vedly k neoprávněným [žádostem o souhlas](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants). Pokud společnost Microsoft zjistí rizikové žádosti o souhlas koncového uživatele, bude místo toho vyžadovat "krokování" na základě souhlasu správce. Tato funkce je ve výchozím nastavení povolená, ale při povolení souhlasu koncového uživatele bude mít za následek změnu chování.

Při zjištění rizikové žádosti o souhlas se zobrazí výzva k zadání souhlasu s oznámením, že je požadováno schválení správcem. Pokud je povolen [pracovní postup žádosti o souhlas správce](configure-admin-consent-workflow.md) , může uživatel odeslat požadavek správci pro další kontrolu přímo z výzvy k zadání souhlasu. Pokud není povolený, zobrazí se následující zpráva:

* **AADSTS90094:** &lt; clientAppDisplayName &gt; potřebuje oprávnění pro přístup k prostředkům ve vaší organizaci, které může udělit jenom správce. Please ask an admin to grant permission to this app before you can use it. (Test udělení souhlasu vyžaduje ve vaší organizaci pro přístup k prostředkům oprávnění, které může udělit pouze správce. Než budete moct tuto aplikaci použít, požádejte správce o udělení oprávnění.)

V takovém případě se událost auditu bude protokolovat jako kategorie "ApplicationManagement", typ aktivity "souhlas s aplikací" a důvod stavu "riziková aplikace zjištěná".

> [!IMPORTANT]
> Správci by měli před schválením žádosti pečlivě [vyhodnotit všechny žádosti o souhlas](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) , zejména když společnost Microsoft zjistila riziko.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Zakázání nebo opětovné povolení souhlasu na základě rizik pomocí PowerShellu

Můžete použít modul Azure AD PowerShell Preview [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)a zakázat krok do souhlasu správce, který je vyžadován v případech, kdy Microsoft zjistí riziko nebo ho znovu povolit, pokud byl dříve zakázán.

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

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. Pochopení hodnoty nastavení:

    | Nastavení       | Typ         | Description  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Logická hodnota |  Příznak označující, zda bude při zjištění rizikové žádosti zablokován souhlas uživatele |

1. Aktualizovat hodnotu nastavení pro požadovanou konfiguraci:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
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

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématech:

* [Konfigurovat nastavení souhlasu uživatele](configure-user-consent.md)
* [Správa zásad souhlasu pro aplikace](manage-app-consent-policies.md)
* [Konfigurace pracovního postupu pro vyjádření souhlasu správce](configure-admin-consent-workflow.md)
* [Naučte se spravovat souhlas s aplikacemi a hodnotit žádosti o souhlas.](manage-consent-requests.md)
* [Udělení souhlasu správce v rámci celého tenanta aplikaci](grant-admin-consent.md)
* [Oprávnění a souhlas na platformě Microsoft identity](../develop/v2-permissions-and-consent.md)

Pokud chcete získat nápovědu nebo najít odpovědi na své otázky:
* [Azure AD v Microsoft Q&A.](https://docs.microsoft.com/answers/topics/azure-active-directory.html)