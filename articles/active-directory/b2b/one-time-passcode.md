---
title: Jednorázové ověřování přístupového kódu pro uživatele typu Host B2B – Azure AD
description: Jak používat e-mail ový přístupový kód k ověření uživatelů typu Host B2B bez nutnosti účtu Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6d897bb983eb06baa4f1573f1f875eea8bb8afc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263371"
---
# <a name="email-one-time-passcode-authentication-preview"></a>E-mailem ověření jednorázového přístupového kódu (náhled)

|     |
| --- |
| E-mailový jednorázový přístupový kód je funkce veřejného náhledu služby Azure Active Directory. Další informace o náhledech najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Tento článek popisuje, jak povolit e-mail jednorázové ověřování přístupového kódu pro uživatele typu Host B2B. Funkce E-mail s jednorázovým přístupovým kódem ověřuje uživatele typu Host B2B, když je nelze ověřit jinými prostředky, jako je Azure AD, účet Microsoft (MSA) nebo federace Google. Díky jednorázovému ověřování pomocí hesla není nutné vytvářet účet Microsoft. Když uživatel hostu uplatní pozvánku nebo přistupuje ke sdílenému prostředku, může požádat o dočasný kód, který je odeslán na jeho e-mailovou adresu. Poté zadají tento kód, aby se mohli dál přihlašovat.

Tato funkce je v současné době k dispozici pro náhled (viz [Přihlášení k náhledu](#opting-in-to-the-preview) níže). Po náhledu bude tato funkce ve výchozím nastavení zapnuta pro všechny klienty.

> [!NOTE]
> Uživatelé jednorázového přístupového kódu se musí přihlásit pomocí odkazu, `https://portal.azure.com/<tenant id>`který obsahuje kontext klienta `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`(například `https://myapps.microsoft.com/?tenantid=<tenant id>` nebo , nebo v případě ověřené domény). Přímé odkazy na aplikace a prostředky také fungovat tak dlouho, dokud zahrnují kontext klienta. Uživatelé typu Host se aktuálně nemohou přihlásit pomocí koncových bodů, které nemají žádný kontext klienta. Například použití `https://myapps.microsoft.com` `https://portal.azure.com`, , nebo týmy společný koncový bod bude mít za následek chybu. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Uživatelské prostředí pro uživatele typu host a jednorázový přístupový kód
Pomocí jednorázového ověřování pomocí hesla může uživatel typu Host uplatnit vaši pozvánku kliknutím na přímý odkaz nebo pomocí e-mailu s pozvánkou. V obou případech zpráva v prohlížeči označuje, že kód bude odeslán na e-mailovou adresu uživatele hosta. Uživatel typu Host vybere **odeslat kód**:
 
   ![Snímek obrazovky s tlačítkem Odeslat kód](media/one-time-passcode/otp-send-code.png)
 
Na e-mailovou adresu uživatele je odeslán přístupový kód. Uživatel načte přístupový kód z e-mailu a zadá jej do okna prohlížeče:
 
   ![Snímek obrazovky s kódovou stránkou Enter](media/one-time-passcode/otp-enter-code.png)
 
Uživatel typu Host je nyní ověřen a může zobrazit sdílený prostředek nebo pokračovat v přihlášení. 

> [!NOTE]
> Jednorázové přístupové kódy jsou platné po dobu 30 minut. Po 30 minutách již tento jednorázový přístupový kód není platný a uživatel musí požádat o nový. Platnost uživatelských relací vyprší po 24 hodinách. Po uplynutí této doby uživatel typu Host obdrží nový přístupový kód při přístupu k prostředku. Vypršení platnosti relace poskytuje další zabezpečení, zejména v případě, že uživatel typu Host opustí svou společnost nebo již nepotřebuje přístup.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Kdy uživatel typu Host získá jednorázový přístupový kód?

Když uživatel typu Host uplatní pozvánku nebo použije odkaz na prostředek, který s ním byl sdílen, obdrží jednorázový přístupový kód, pokud:
- Nemají účet Azure AD 
- Nemají účet Microsoft 
- Zvoucí tenant nenastavil @gmail.com federaci Google pro uživatele a @googlemail.com uživatele. 

V době pozvání nic nenasvědčuje tomu, že by uživatel, kterého zvete, používal jednorázové ověřování pomocí hesla. Ale když se uživatel host přihlásí, jednorázové ověřování přístupového kódu bude záložní metodou, pokud nelze použít žádné jiné metody ověřování. 

Uživatele typu Host, kteří se ověřují pomocí jednorázových přístupových kódů na webu Azure Portal, můžete zobrazit tak, že přejdete do**organizačních vztahů Služby** >  **Azure Active Directory** > **Uživatelé z jiných organizací**.

![Snímek obrazovky s uživatelem jednorázového přístupového kódu se zdrojovou hodnotou OTP](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Když uživatel uplatní jednorázový přístupový kód a později získá účet MSA, Azure AD nebo jiný federovaný účet, bude nadále ověřován pomocí jednorázového přístupového kódu. Pokud chcete aktualizovat jejich metodu ověřování, můžete odstranit jejich uživatelský účet hosta a znovu je pozvat.

### <a name="example"></a>Příklad
Uživatel alexdoe@gmail.com typu Host je pozván do společnosti Fabrikam, která nemá nastavenou federaci Google. Alex nemá účet Microsoft. Obdrží jednorázový přístupový kód pro ověřování.

## <a name="opting-in-to-the-preview"></a>Přihlášení k náhledu 
Může trvat několik minut, než se akce opt-in projeví. Poté budou používat jednorázové ověřování pomocí hesla pouze nově pozvaní uživatelé, kteří splňují výše uvedené podmínky. Uživatelé typu Host, kteří dříve uplatnili pozvánku, budou nadále používat stejnou metodu ověřování.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Přihlášení pomocí portálu Azure AD
1.  Přihlaste se k [portálu Azure](https://portal.azure.com/) jako globální správce Azure AD.
2.  V navigačním podokně vyberte **Azure Active Directory**.
3.  V části **Manage**vyberte **Položku Organizační vztahy**.
4.  Vyberte **Nastavení**.
5.  V části **Povolit e-mailový jednorázový přístupový kód pro hosty (náhled)** vyberte **Ano**.
 
### <a name="to-opt-in-using-powershell"></a>Přihlášení pomocí PowerShellu

Nejprve budete muset nainstalovat nejnovější verzi modulu Azure AD PowerShell pro graf (AzureADPreview). Pak určíte, zda zásady B2B již existují a spusťte příslušné příkazy.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Předpoklad: Instalace nejnovějšího modulu AzureADPreview
Nejdřív zkontrolujte, které moduly jste nainstalovali. Otevřete Windows PowerShell se zvýšenými uživatelskými oprávněními (Spustit jako správce) a spusťte následující příkaz:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Pokud se modul AzureADPreview zobrazí bez zprávy s oznámením, že existuje novější verze, je všechno připravené. V opačném případě proveďte na základě výstupu jednu z následujících akcí:

- Pokud se nezobrazí žádné výsledky, spusťte k instalaci modulu AzureADPreview následující příkaz:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Pokud se ve výsledcích zobrazí pouze modul AzureAD, spusťte k instalaci modulu AzureADPreview tyto příkazy: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Pokud se ve výsledcích zobrazí pouze modul AzureADPreview, ale zobrazí se zpráva, že existuje novější verze, aktualizujte ho následujícími příkazy: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Může se zobrazit výzva, že modul instalujete z nedůvěryhodného úložiště. K tomu dochází, pokud jste úložiště PSGallery dříve nenastavili jako důvěryhodné. Stisknutím klávesy **Y** modul nainstalujte.

#### <a name="check-for-existing-policies-and-opt-in"></a>Kontrola stávajících zásad a přihlášení

Dále zkontrolujte, zda B2BManagementPolicy aktuálně existuje spuštěním následující:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Pokud je výstup False, zásada aktuálně neexistuje. Vytvořte nový B2BManagementPolicy a přihlaste se k náhledu spuštěním následujícího:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Pokud je výstup True, aktuálně existuje zásada B2BManagementPolicy. Chcete-li zásadu aktualizovat a přihlásit se k verzi Preview, spusťte následující:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Odhlášení z náhledu po přihlášení
Může trvat několik minut, než se akce odhlášení projeví. Pokud náhled vypnete, nebudou se moci přihlásit žádní uživatelé typu Host, kteří uplatní jednorázový přístupový kód. Můžete odstranit uživatele typu Host a znovu pozvat uživatele, aby se mohl znovu přihlásit pomocí jiné metody ověřování.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Vypnutí náhledu pomocí portálu Azure AD
1.  Přihlaste se k [portálu Azure](https://portal.azure.com/) jako globální správce Azure AD.
2.  V navigačním podokně vyberte **Azure Active Directory**.
3.  V části **Manage**vyberte **Položku Organizační vztahy**.
4.  Vyberte **Nastavení**.
5.  V části **Povolit e-mailový jednorázový přístupový kód pro hosty (náhled)** vyberte **Ne**.

### <a name="to-turn-off-the-preview-using-powershell"></a>Vypnutí náhledu pomocí PowerShellu
Nainstalujte nejnovější modul AzureADPreview, pokud ho ještě nemáte (viz [Předpoklad: Nainstalujte nejnovější modul AzureADPreview](#prerequisite-install-the-latest-azureadpreview-module) výše). Potom ověřte, zda zásady náhledu jednorázového hesla aktuálně existuje spuštěním následujícího:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Pokud je výstup True, odhlaste se z náhledu spuštěním následujícího:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

