---
title: Správa vlastních zásad pomocí PowerShellu
titleSuffix: Azure AD B2C
description: Použijte rutinu Prostředí PowerShell azure active directory (Azure AD) pro programovou správu vlastních zásad Azure AD B2C. Vytvářejte, čtujte, aktualizujte a odstraňujte vlastní zásady pomocí PowerShellu.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebf0cfffa410d8dfe2f0e0b42a0fee0c16106fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187402"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Správa vlastních zásad Azure AD B2C pomocí Azure PowerShellu

Azure PowerShell poskytuje několik rutin pro správu vlastních zásad na základě příkazového řádku a skriptu ve vašem tenantovi Azure AD B2C. Zjistěte, jak pomocí modulu Azure AD PowerShell:

* Seznam vlastních zásad v tenantovi Azure AD B2C
* Stažení zásady od tenanta
* Aktualizace existující zásady přepsáním jejího obsahu
* Nahrání nové zásady do klienta Azure AD B2C
* Odstranění vlastních zásad z tenanta

## <a name="prerequisites"></a>Požadavky

* [Tenant Azure AD B2C](tutorial-create-tenant.md)a přihlašovací údaje pro uživatele v adresáři s rolí [správce zásad B2C IEF](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Vlastní zásady](custom-policy-get-started.md) nahrané do vašeho tenanta
* [Azure AD PowerShell pro **graf preview modul**](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

## <a name="connect-powershell-session-to-b2c-tenant"></a>Připojení relace Prostředí PowerShell k tenantovi B2C

Chcete-li pracovat s vlastními zásadami v tenantovi Azure AD B2C, musíte nejprve připojit relaci PowerShellu k tenantovi pomocí příkazu [Connect-AzureAD.][Connect-AzureAD]

Spusťte následující příkaz, který `{b2c-tenant-name}` nahrazuje název vašeho klienta Azure AD B2C. Přihlaste se pomocí účtu, kterému je přiřazena role [správce zásad B2C IEF](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) v adresáři.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Příklad výstupu příkazu zobrazujícího úspěšné přihlášení:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>Seznam všech vlastních zásad v tenantovi

Zjišťování vlastních zásad umožňuje správci Azure AD B2C kontrolovat, spravovat a přidávat obchodní logiku do svých operací. Pomocí příkazu [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] vrátíte seznam ID vlastních zásad v tenantovi Azure AD B2C.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

Příklad výstupu příkazu:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>Stažení zásad

Po kontrole seznamu ID zásad, můžete cílit na konkrétní zásady s [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] ke stažení jeho obsahu.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

V tomto příkladu se stáhne zásada s *id B2C_1A_signup_signin:*

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

Chcete-li upravit obsah zásad místně, naložte výstup `-OutputFilePath` příkazu do souboru s argumentem a otevřete soubor v oblíbeném editoru.

Příklad příkazu odesílajícího výstup do souboru:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Aktualizace existující zásady

Po úpravě souboru zásad, který jste vytvořili nebo stáhli, můžete publikovat aktualizovanou zásadu do Azure AD B2C pomocí příkazu [Set-AzureADMSTrustFrameworkPolicy.][Set-AzureADMSTrustFrameworkPolicy]

Pokud vydáte `Set-AzureADMSTrustFrameworkPolicy` příkaz s ID zásady, která již existuje ve vašem tenantovi Azure AD B2C, obsah této zásady je přepsán.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Příklad příkazu:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

Další příklady naleznete v odkazu příkazu [Set-AzureADMSTrustFrameworkPolicy.][Set-AzureADMSTrustFrameworkPolicy]

## <a name="upload-a-new-policy"></a>Nahrání nové zásady

Když provedete změnu vlastní zásady, která běží v produkčním prostředí, můžete chtít publikovat více verzí zásad pro scénáře záložního nebo ab testování. Nebo můžete chtít vytvořit kopii existující zásady, upravit ji s několika malými změnami a nahrát ji jako novou zásadu pro použití jinou aplikací.

Pomocí příkazu [New-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] nahrajte novou zásadu:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Příklad příkazu:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Odstranění vlastních zásad

Chcete-li zachovat čistý provozní životní cyklus, doporučujeme pravidelně odebírat nepoužívané vlastní zásady. Můžete například odebrat staré verze zásad po provedení migrace na novou sadu zásad a ověření funkce nové zásady. Navíc pokud se pokusíte publikovat sadu vlastních zásad a obdržíte chybu, může mít smysl odebrat zásady, které byly vytvořeny jako součást neúspěšné verze.

Pomocí příkazu [Remove-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] odstraňte zásady z vašeho tenanta.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Příklad příkazu:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Poradce při potížích s odesíláním zásad

Při pokusu o publikování nové vlastní zásady nebo aktualizaci existující zásady může nesprávné formátování XML a chyby v řetězci dědičnosti souboru zásad způsobit selhání ověření.

Například je zde pokus o aktualizaci zásady s obsahem, který obsahuje poškozený XML (výstup je zkrácen pro stručnost):

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

Informace o řešení potíží s vlastními zásadami najdete [v tématu Poradce při potížích s vlastními zásadami Azure AD B2C a rozhraním Identity Experience Framework](active-directory-b2c-guide-troubleshooting-custom.md).

## <a name="next-steps"></a>Další kroky

Informace o použití prostředí PowerShell k nasazení vlastních zásad jako součásti kanálu průběžné integrace/průběžného doručování (CI/CD) najdete [v tématu Nasazení vlastních zásad z kanálu Azure DevOps](deploy-custom-policies-devops.md).

<!-- LINKS - External -->
[Connect-AzureAD]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/set-azureadmstrustframeworkpolicy
