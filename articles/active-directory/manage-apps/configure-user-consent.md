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
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 433ff5498baeb4c31473e43fc4a5d24f4ba9fd1c
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90605154"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Konfigurace způsobu vyjadřování souhlasu koncových uživatelů s aplikacemi

Své aplikace můžete integrovat s platformou Microsoft identity, aby se uživatelé mohli přihlašovat pomocí svého pracovního nebo školního účtu a přistupovat k datům vaší organizace, aby mohli poskytovat rozsáhlá prostředí založená na datech.

Předtím, než aplikace získá přístup k datům vaší organizace, musí uživateli udělit oprávnění aplikace. Různá oprávnění umožňují různé úrovně přístupu. Ve výchozím nastavení mohou všichni uživatelé udělit souhlas s aplikacemi oprávnění, které nevyžadují souhlas správce. Ve výchozím nastavení může uživatel například udělit souhlas s tím, že aplikaci má přístup ke své poštovní schránce, ale nemůže udělit souhlas, aby aplikace neomezený přístup ke čtení a zápisu do všech souborů ve vaší organizaci.

Díky tomu, že uživatelé můžou aplikacím udělit přístup k datům, můžou snadno získat užitečné aplikace a být produktivní. V některých případech však tato konfigurace může představovat riziko, pokud není sledována a kontrolována pečlivě.

## <a name="user-consent-settings"></a>Nastavení souhlasu uživatele

Chcete-li určit, které případy můžou uživatelé udělit souhlas s aplikacemi, vyberte zásadu souhlasu, která bude platit pro všechny uživatele. Tady jsou tři možnosti zásad souhlasu:

* **Zakázat souhlas uživatele** – uživatelé nemůžou udělovat oprávnění aplikacím. Uživatelé se mohou nadále přihlašovat k aplikacím, které dříve poslaly nebo které jim byly odsouhlaseny jménem správců, ale nebudou jim dovoleni souhlasit s novými oprávněními ani s novými aplikacemi. Pouze uživatelé, kterým byla udělena role adresáře, která zahrnuje oprávnění udělit souhlas, budou moci vyjádřit souhlas s novými oprávněními nebo novými aplikacemi.

* **Uživatelé můžou udělit souhlas s aplikacemi od ověřených vydavatelů, ale jenom pro oprávnění, která jste vybrali (Preview)** – všichni uživatelé můžou souhlasit jenom s aplikacemi, které publikoval [ověřený vydavatel](../develop/publisher-verification-overview.md) a aplikace, které jsou zaregistrované ve vašem tenantovi. Uživatelé můžou odsouhlasit jenom oprávnění, která jste klasifikováni jako "malý dopad", označovanou taky jako "nízké riziko". Co se považuje za nízké riziko pro jednu organizaci, jako je například aplikace, která zobrazuje e-mailovou adresu uživatelů, může být považována za vysoké riziko pro jinou organizaci. Z tohoto důvodu jsou oprávnění "nízká rizika" nastavena správcem pro tenanta.

  Nezapomeňte [klasifikovat oprávnění](#configure-permission-classifications-preview) a vybrat, která oprávnění uživatelé můžou udělit souhlas.

* **Uživatelé můžou udělit souhlas se všemi aplikacemi** – Tato možnost umožňuje všem uživatelům udělit souhlas s libovolným oprávněním, které pro žádnou aplikaci nevyžadují souhlas správce. 

   Chcete-li snížit riziko, že se škodlivé aplikace pokoušejí uživatelům získat přístup k datům vaší organizace, doporučujeme udělit souhlas uživateli pouze pro aplikace, které byly publikovány [ověřeným vydavatelem](../develop/publisher-verification-overview.md).

### <a name="configure-user-consent-settings-from-the-azure-portal"></a>Konfigurace nastavení souhlasu uživatele z Azure Portal

Konfigurace nastavení souhlasu uživatele prostřednictvím Azure Portal:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako [globální správce](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Vyberte **Azure Active Directory**  >  souhlasu**podnikových aplikací**  >  **a**  >  **nastavení souhlasu uživatele**.
1. V části **souhlas uživatele u aplikací**vyberte, které nastavení souhlasu se má nakonfigurovat pro všechny uživatele.
1. Vyberte **Uložit** a uložte nastavení.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Nastavení souhlasu uživatele":::

> [!TIP]
> Zvažte možnost povolit [pracovní postup souhlasu](configure-admin-consent-workflow.md) správce, aby uživatelé mohli požádat o revizi správce a schválení aplikace, které uživatel nemá oprávněně vyjádřit souhlas – například když je souhlas uživatele zakázán nebo když aplikace požaduje oprávnění, která uživatel nemá povoleno udělit.

### <a name="configure-user-consent-settings-using-powershell"></a>Konfigurace nastavení souhlasu uživatele pomocí prostředí PowerShell

Můžete použít nejnovější modul Azure AD PowerShell Preview [AzureADPreview](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)a vybrat, které zásady souhlasu určují souhlas uživatele pro aplikace.

* **Zakázat souhlas s uživatelem** – Chcete-li zakázat souhlas s uživatelem, nastavte zásady souhlasu, které budou řídit souhlas uživatele jako prázdný:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

* **Povolení souhlasu uživatele pro aplikace od ověřených vydavatelů pro vybraná oprávnění (Preview)** – Pokud chcete omezenému souhlasu uživatele udělit jenom pro aplikace od ověřených vydavatelů a aplikací registrovaných ve vašem tenantovi a jenom pro oprávnění, která jste klasifikují jako "nízký dopad", nakonfigurujte předdefinované zásady souhlasu s názvem `microsoft-user-default-low` :

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-low")
  ```

   Nezapomeňte [klasifikovat oprávnění](#configure-permission-classifications-preview) a vybrat, která oprávnění uživatelé můžou udělit souhlas.

* **Povolení souhlasu uživatele pro všechny aplikace** – povolení souhlasu uživatele pro všechny aplikace:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-legacy")
  ```

   Tato možnost umožňuje všem uživatelům udělit souhlas s jakýmkoli oprávněním, které nevyžaduje souhlas správce, pro žádnou aplikaci. Doporučujeme uživateli udělit souhlas jenom pro aplikace od ověřených vydavatelů.

## <a name="configure-permission-classifications-preview"></a>Konfigurace klasifikací oprávnění (Preview)

Klasifikace oprávnění umožňují určit dopad, který mají různá oprávnění podle zásad a hodnocení rizik vaší organizace. Klasifikace oprávnění můžete například použít v zásadách pro vyjádření souhlasu k identifikaci sady oprávnění, ke kterým můžou uživatelé udělit souhlas.

> [!NOTE]
> V současné době je podporována pouze klasifikace oprávnění "nízká dopad". Pouze delegovaná oprávnění, která nevyžadují souhlas správce, je možné klasifikovat jako "nízký dopad".

### <a name="classify-permissions-using-the-azure-portal"></a>Klasifikovat oprávnění pomocí Azure Portal

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako [globální správce](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Vyberte **Azure Active Directory**  >  souhlasu**podnikových aplikací**  >  **a**  >  **klasifikace oprávnění**.
1. Zvolením možnosti **Přidat oprávnění** Klasifikujte jiné oprávnění jako "nízký dopad". 
1. Vyberte rozhraní API a pak vyberte delegovaná oprávnění.

V tomto příkladu jsme klasifikováni s minimální sadou oprávnění vyžadovaných pro jednotné přihlašování:

:::image type="content" source="media/configure-user-consent/permission-classifications.png" alt-text="Klasifikace oprávnění":::

> [!TIP]
> Pro rozhraní Microsoft Graph API jsou minimální oprávnění potřebná k tomu, aby základní jednotné přihlašování `openid` , `profile` `User.Read` a `offline_access` . Pomocí těchto oprávnění může aplikace číst podrobnosti profilu přihlášeného uživatele a může tento přístup zachovat, i když uživatel už tuto aplikaci nepoužívá.

### <a name="classify-permissions-using-powershell"></a>Klasifikace oprávnění pomocí PowerShellu

K klasifikaci oprávnění můžete použít nejnovější modul Azure AD PowerShell Preview, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true). Klasifikace oprávnění jsou nakonfigurovány na objektu **ServicePrincipal** rozhraní API, který publikuje oprávnění.

#### <a name="to-read-the-current-permission-classifications-for-an-api"></a>Čtení aktuálních klasifikací oprávnění pro rozhraní API:

1. Načte objekt **ServicePrincipal** pro rozhraní API. Tady načteme objekt ServicePrincipal pro rozhraní API pro Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Přečtěte si klasifikace delegovaných oprávnění pro rozhraní API:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="to-classify-a-permission-as-low-impact"></a>Klasifikace oprávnění jako "nízký dopad":

1. Načte objekt **ServicePrincipal** pro rozhraní API. Tady načteme objekt ServicePrincipal pro rozhraní API pro Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Vyhledejte delegované oprávnění, které byste chtěli klasifikovat:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Nastavte klasifikaci oprávnění pomocí názvu a ID oprávnění:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="to-remove-a-delegated-permission-classification"></a>Odebrání klasifikace delegovaného oprávnění:

1. Načte objekt **ServicePrincipal** pro rozhraní API. Tady načteme objekt ServicePrincipal pro rozhraní API pro Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Vyhledejte klasifikaci delegovaného oprávnění, kterou chcete odebrat:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Odstraňte klasifikaci oprávnění:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Konfigurace souhlasu vlastníka skupiny u aplikací, které přistupují k datům skupiny

Vlastníci skupiny můžou autorizovat aplikace, jako jsou například aplikace publikované dodavateli třetích stran, pro přístup k datům vaší organizace, které jsou přidružené ke skupině. Vlastník týmu v Microsoft Teams může například dovolit aplikaci číst všechny týmy v týmu nebo zobrazit základní profil členů skupiny.

Můžete nakonfigurovat, kteří uživatelé můžou udělit souhlas aplikacím, které přistupují k datům jejich skupin, nebo můžete tuto funkci zakázat.

### <a name="configure-group-owner-consent-using-the-azure-portal"></a>Konfigurace souhlasu vlastníka skupiny pomocí Azure Portal

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako [globální správce](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Vyberte **Azure Active Directory**  >  souhlasu**podnikových aplikací**  >  **a**  >  **nastavení souhlasu uživatele**.
3. V části **souhlas vlastníka skupiny pro přístup k datům** vyberte možnost, kterou chcete povolit.
4. Vyberte **Uložit** a uložte nastavení.

V tomto příkladu můžou všichni vlastníci skupiny udělit souhlas s aplikacemi, které přistupují k datům jejich skupin:

:::image type="content" source="media/configure-user-consent/group-owner-consent.png" alt-text="Nastavení souhlasu vlastníka skupiny":::

### <a name="configure-group-owner-consent-using-powershell"></a>Konfigurace souhlasu vlastníka skupiny pomocí PowerShellu

Pomocí modulu Azure AD PowerShell Preview, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)můžete povolit nebo zakázat vlastníkům skupin souhlas s aplikacemi, které přistupují k datům vaší organizace pro skupiny, které vlastní.

1. Ujistěte se, že používáte modul [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) . Tento krok je důležitý, pokud jste nainstalovali modul [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0&preserve-view=true) i modul [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) ).

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

## <a name="configure-risk-based-step-up-consent"></a>Konfigurace souhlasu pro krokování na základě rizika

Souhlasu krokování na základě rizika pomáhá omezit vystavení uživatelů škodlivým aplikacím, které by vedly k neoprávněným [žádostem o souhlas](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants). Pokud společnost Microsoft zjistí rizikové žádosti o souhlas koncového uživatele, bude místo toho vyžadovat "krokování" na základě souhlasu správce. Tato funkce je ve výchozím nastavení povolená, ale při povolení souhlasu koncového uživatele bude mít za následek změnu chování.

Při zjištění rizikové žádosti o souhlas se zobrazí výzva k zadání souhlasu s oznámením, že je požadováno schválení správcem. Pokud je povolen [pracovní postup žádosti o souhlas správce](configure-admin-consent-workflow.md) , může uživatel odeslat požadavek správci pro další kontrolu přímo z výzvy k zadání souhlasu. Pokud není povolený, zobrazí se následující zpráva:

* **AADSTS90094:** &lt; clientAppDisplayName &gt; potřebuje oprávnění pro přístup k prostředkům ve vaší organizaci, které může udělit jenom správce. Please ask an admin to grant permission to this app before you can use it. (Test udělení souhlasu vyžaduje ve vaší organizaci pro přístup k prostředkům oprávnění, které může udělit pouze správce. Než budete moct tuto aplikaci použít, požádejte správce o udělení oprávnění.)

V takovém případě se událost auditu bude protokolovat jako kategorie "ApplicationManagement", typ aktivity "souhlas s aplikací" a důvod stavu "riziková aplikace zjištěná".

> [!IMPORTANT]
> Správci by měli před schválením žádosti pečlivě [vyhodnotit všechny žádosti o souhlas](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) , zejména když společnost Microsoft zjistila riziko.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Zakázání nebo opětovné povolení souhlasu na základě rizik pomocí PowerShellu

Můžete použít modul Azure AD PowerShell Preview [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)a zakázat krok do souhlasu správce, který je vyžadován v případech, kdy Microsoft zjistí riziko nebo ho znovu povolit, pokud byl dříve zakázán.

To můžete provést pomocí stejných kroků, jak je uvedeno výše pro [konfiguraci souhlasu vlastníka skupiny pomocí prostředí PowerShell](#configure-group-owner-consent-using-powershell), ale nahrazování jiné hodnoty nastavení. Existují tři rozdíly v krocích: 

1. Seznamte se s hodnotami nastavení pro udělení souhlasu na základě rizik:

    | Nastavení       | Typ         | Description  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Logická hodnota |  Příznak označující, zda bude při zjištění rizikové žádosti zablokován souhlas uživatele |

1. V kroku 3 nahraďte následující hodnotu:

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
    
1. V kroku 5 nahraďte jednu z následujících akcí:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématech:

* [Konfigurace pracovního postupu pro vyjádření souhlasu správce](configure-admin-consent-workflow.md)
* [Naučte se spravovat souhlas s aplikacemi a hodnotit žádosti o souhlas.](manage-consent-requests.md)
* [Udělení souhlasu správce v rámci celého tenanta aplikaci](grant-admin-consent.md)
* [Oprávnění a souhlas na platformě Microsoft identity](../develop/active-directory-v2-scopes.md)

Pokud chcete získat nápovědu nebo najít odpovědi na své otázky:
* [Azure AD v StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
