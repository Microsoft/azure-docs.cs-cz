---
title: Správa vlastních zásad pomocí PowerShellu
titleSuffix: Azure AD B2C
description: Použijte rutinu prostředí PowerShell pro Azure Active Directory (Azure AD) pro programovou správu vašich Azure AD B2C vlastních zásad. Vytváření, čtení, aktualizace a odstraňování vlastních zásad pomocí prostředí PowerShell.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fcc482e215e646fec20516f35641bd05398d2f2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96928711"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Správa Azure AD B2C vlastních zásad pomocí Azure PowerShell

Azure PowerShell poskytuje několik rutin pro správu vlastních zásad na základě příkazového řádku a skriptu v tenantovi Azure AD B2C. Naučte se používat modul Azure AD PowerShell k těmto akcím:

* Seznam vlastních zásad v tenantovi Azure AD B2C
* Stažení zásady z klienta
* Aktualizovat existující zásady přepsáním jejího obsahu
* Nahrajte do svého tenanta Azure AD B2C novou zásadu.
* Odstranění vlastních zásad z tenanta

## <a name="prerequisites"></a>Požadavky

* [Azure AD B2C tenant](tutorial-create-tenant.md)a přihlašovací údaje uživatele v adresáři s rolí [správce zásad IEF B2C](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)
* [Vlastní zásady](custom-policy-get-started.md) odeslané do vašeho tenanta
* [Azure AD PowerShell pro **modul Graph Preview**](/powershell/azure/active-directory/install-adv2)

## <a name="connect-powershell-session-to-b2c-tenant"></a>Připojení relace PowerShellu k tenantovi B2C

Pokud chcete pracovat s vlastními zásadami ve vašem tenantovi Azure AD B2C, musíte nejdřív připojit relaci PowerShellu k tenantovi pomocí příkazu [Connect-AzureAD][Connect-AzureAD] .

Spusťte následující příkaz a nahraďte `{b2c-tenant-name}` názvem vašeho tenanta Azure AD B2C. Přihlaste se pomocí účtu, který má přiřazenou roli [správce zásad B2C IEF](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) v adresáři.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Příklad výstupu příkazu znázorňujícího úspěšné přihlášení:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>Vypsat všechny vlastní zásady v tenantovi

Zjišťování vlastních zásad umožňuje správci Azure AD B2C kontrolovat, spravovat a přidávat obchodní logiku do jejich operací. Pomocí příkazu [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] můžete v tenantovi Azure AD B2C vrátit seznam identifikátorů vlastních zásad.

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

## <a name="download-a-policy"></a>Stáhnout zásadu

Po kontrole seznamu ID zásad můžete určit konkrétní zásadu pomocí [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] ke stažení jejího obsahu.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

V tomto příkladu se stáhla zásada s ID *B2C_1A_signup_signin* :

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

Pokud chcete obsah zásady upravit místně, přesměrujte výstup příkazu do souboru s `-OutputFilePath` argumentem a pak ho otevřete ve svém oblíbeném editoru.

Příklad příkazu odeslání výstupu do souboru:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Aktualizovat existující zásady

Po úpravě souboru zásad, který jste vytvořili nebo stáhli, můžete aktualizované zásady publikovat na Azure AD B2C pomocí příkazu [set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] .

Pokud vydáte `Set-AzureADMSTrustFrameworkPolicy` příkaz s ID zásady, která už ve vašem Azure AD B2C tenantovi existuje, obsah této zásady se přepíše.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Příklad příkazu:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

Další příklady najdete v referenčních informacích k příkazu [set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] .

## <a name="upload-a-new-policy"></a>Nahrát novou zásadu

Když provedete změnu vlastní zásady, která běží v produkčním prostředí, možná budete chtít publikovat více verzí zásad pro testovací scénáře a/B. Nebo můžete chtít vytvořit kopii existující zásady, upravit ji pomocí několika malých změn a pak ji nahrát jako novou zásadu pro použití v jiné aplikaci.

Pomocí příkazu [New-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] Nahrajte nové zásady:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Příklad příkazu:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Odstranění vlastních zásad

Pokud chcete zachovat životní cyklus čistých operací, doporučujeme pravidelně odebírat nepoužívané vlastní zásady. Například můžete chtít odebrat staré verze zásad po provedení migrace na novou sadu zásad a ověření funkčnosti nové zásady. Pokud se navíc pokusíte publikovat sadu vlastních zásad a obdržíte chybu, může být vhodné odebrat zásady, které byly vytvořeny jako součást neúspěšného vydání.

Pomocí příkazu [Remove-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] odstraňte zásadu z vašeho tenanta.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Příklad příkazu:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Řešení potíží při nahrávání zásad

Když se pokusíte publikovat novou vlastní zásadu nebo aktualizovat existující zásady, nesprávné formátování XML a chyby v řetězu dědičnosti souboru zásad můžou způsobit selhání ověřování.

Tady je například pokus o aktualizaci zásad s obsahem, který obsahuje chybně vytvořený kód XML (výstup je zkrácen pro zkrácení):

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

Informace o řešení potíží s vlastními zásadami najdete v tématu [řešení potíží s Azure AD B2C vlastní zásady a architektura prostředí identit](./troubleshoot-custom-policies.md).

## <a name="next-steps"></a>Další kroky

Informace o tom, jak pomocí PowerShellu nasadit vlastní zásady v rámci kanálu průběžné integrace nebo průběžného doručování (CI/CD), najdete v tématu [nasazení vlastních zásad z kanálu Azure DevOps](deploy-custom-policies-devops.md).

<!-- LINKS - External -->
[Connect-AzureAD]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/set-azureadmstrustframeworkpolicy
