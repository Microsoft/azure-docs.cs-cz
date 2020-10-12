---
title: Jednorázové ověřování hesla pro uživatele typu Host B2B – Azure AD
description: Jak používat jednorázové heslo e-mailu k ověřování uživatelů typu Host B2B bez nutnosti účet Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 714e4484c71b995bee186a2d94dc45c7ff82c50d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87908553"
---
# <a name="email-one-time-passcode-authentication-preview"></a>E-mailové ověřování heslem jednorázového hesla (Preview)

> [!NOTE]
> Heslo pro jednorázové e-maily je funkce veřejné verze Preview Azure Active Directory. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Tento článek popisuje, jak povolit jednorázové ověřování e-mailových hesel pro uživatele typu Host B2B. Funkce jednorázového hesla pro E-mail ověřuje uživatele typu Host B2B, když se nemůžou ověřit jiným způsobem jako Azure AD, účet Microsoft (MSA) nebo Google Federation. Při jednorázovém ověřování pomocí hesla není nutné vytvářet účet Microsoft. Když uživatel typu Host uplatňuje pozvánku nebo přistupuje ke sdílenému prostředku, může požádat o dočasný kód, který se pošle na svou e-mailovou adresu. Pak tento kód zadá, aby bylo možné pokračovat v přihlašování.

Tato funkce je aktuálně dostupná pro verzi Preview (viz možnost přihlašování [k verzi Preview](#opting-in-to-the-preview) níže). Po zobrazení verze Preview bude tato funkce ve výchozím nastavení zapnutá pro všechny klienty.

> [!NOTE]
> Jednorázovým uživatelům se musí přihlásit pomocí odkazu, který obsahuje kontext tenanta (například `https://myapps.microsoft.com/?tenantid=<tenant id>` nebo nebo `https://portal.azure.com/<tenant id>` v případě ověřené domény `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` ). Přímé odkazy na aplikace a prostředky fungují i tak dlouho, dokud budou zahrnovat kontext tenanta. Uživatelé typu Host se aktuálně nemohou přihlašovat pomocí koncových bodů, které nemají kontext tenanta. Například použití `https://myapps.microsoft.com` , `https://portal.azure.com` nebo společný koncový bod týmů bude mít za následek chybu. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Uživatelské prostředí pro uživatele typu Host pro jednorázové heslo
Při jednorázovém ověřování pomocí hesla může uživatel typu Host uplatnit pozvánku kliknutím na přímý odkaz nebo pomocí e-mailu s pozvánkou. V obou případech zpráva v prohlížeči indikuje, že se kód pošle na e-mailovou adresu uživatele typu Host. Uživatel typu host vybere **Odeslat kód**:
 
   ![Snímek obrazovky zobrazující tlačítko pro odeslání kódu](media/one-time-passcode/otp-send-code.png)
 
Heslo se pošle na e-mailovou adresu uživatele. Uživatel načte heslo z e-mailu a vloží ho do okna prohlížeče:
 
   ![Snímek obrazovky, na které se zobrazuje znaková stránka ENTER](media/one-time-passcode/otp-enter-code.png)
 
Uživatel typu Host je teď ověřený a může zobrazit sdílený prostředek nebo pokračovat v přihlašování. 

> [!NOTE]
> Jednorázová hesla platí po dobu 30 minut. Po 30 minutách již není konkrétní zadání jednorázového hesla platné a uživatel musí požádat o nový. Platnost uživatelských relací vyprší za 24 hodin. Po uplynutí této doby obdrží uživatel typu Host při přístupu k prostředku nové heslo. Vypršení platnosti relace poskytuje zabezpečení, zejména v případě, že uživatel typu Host opustí svou společnost nebo už nepotřebuje přístup.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Kdy uživatel typu Host získá jednorázové heslo?

Když uživatel typu Host uplatní pozvánku nebo použije odkaz na prostředek, který s nimi sdílí, obdrží jednorázové heslo, pokud:
- Nemají účet Azure AD. 
- Nemají účet Microsoft 
- Tenant pro pozvání nevytvořil pro uživatele Google Federation. @gmail.com @googlemail.com 

V době pozvání není k dispozici žádný náznak, že uživatel, kterého Zvete, bude používat jednorázové ověřování pomocí hesla. Pokud se ale uživatel typu Host přihlásí, bude metoda jednorázového ověřování hesla záložní metodou, pokud nebude možné použít žádné jiné metody ověřování. 

Uživatele typu Host, kteří se v Azure Portal ověřují pomocí jednorázových hesel, můžete zobrazit tak, že **Azure Active Directory**  >  **uživatele**.

![Snímek obrazovky, který zobrazuje jednorázového uživatele se zdrojovou hodnotou JEDNORÁZOVého hesla](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Když uživatel uplatňuje jednorázové heslo a později získá MSA, účet Azure AD nebo jiný federovaný účet, budou se i nadále ověřovat pomocí jednorázového hesla. Pokud chcete aktualizovat metodu ověřování, můžete odstranit svůj uživatelský účet hosta a znovu ho pozvat.

### <a name="example"></a>Příklad
Uživatel typu host alexdoe@gmail.com je pozván společnosti Fabrikam, která nemá nastavenu Google Federation. Alex nemá účet Microsoft. Obdrží pro ověřování jednorázové heslo.

## <a name="opting-in-to-the-preview"></a>Přihlaste se k verzi Preview 
Může to trvat několik minut, než se akce přihlášení projeví. Pak budou používat jenom nově pozvaní uživatelé, kteří splňují podmínky uvedené výše, k ověřování pomocí jednorázového hesla. Uživatelé typu Host, kteří dřív použili pozvánku, budou i nadále používat stejnou metodu ověřování.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Výslovný souhlas s používáním portálu Azure AD
1.  Přihlaste se k [Azure Portal](https://portal.azure.com/) jako globální správce Azure AD.
2.  V navigačním podokně vyberte **Azure Active Directory**.
3.  Vyberte externí **identity**externí  >  **nastavení spolupráce**.
5.  V části **Povolit e-mailové One-Time heslo pro hosty (Preview)** vyberte **Ano**.
 
### <a name="to-opt-in-using-powershell"></a>Výslovný souhlas s používáním PowerShellu

Nejdřív budete muset nainstalovat nejnovější verzi Azure AD PowerShellu pro modul Graph (AzureADPreview). Pak určíte, zda zásady B2B již existovaly, a spusťte příslušné příkazy.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Předpoklad: instalace nejnovějšího modulu AzureADPreview
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

#### <a name="check-for-existing-policies-and-opt-in"></a>Vyhledat existující zásady a vyjádřit se

V dalším kroku zkontrolujte, jestli B2BManagementPolicy aktuálně existuje, spuštěním následujícího:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Pokud je výstup nepravdivý, zásada v tuto chvíli neexistuje. Vytvořte nový B2BManagementPolicy a přihlaste se ke službě Preview spuštěním následujícího:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Pokud má výstup hodnotu true, zásada B2BManagementPolicy aktuálně existuje. Chcete-li aktualizovat zásadu a přihlásit se k verzi Preview, spusťte následující příkaz:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Vypnutí náhledu po jeho přijetí
Může to trvat několik minut, než se akce odsouhlasení projeví. Pokud vypnete verzi Preview, všichni uživatelé typu Host, kteří se znovu považovali za jednorázové heslo, se nebudou moct přihlásit. Uživatele typu Host můžete odstranit a znovu pozvat, aby se mohli znovu přihlásit pomocí jiné metody ověřování.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Vypnutí verze Preview pomocí portálu Azure AD
1.  Přihlaste se k [Azure Portal](https://portal.azure.com/) jako globální správce Azure AD.
2.  V navigačním podokně vyberte **Azure Active Directory**.
3.  Vyberte externí **identity**externí  >  **nastavení spolupráce**.
5.  V části **Povolit e-mailové One-Time heslo pro hosty (Preview)** vyberte **ne**.

### <a name="to-turn-off-the-preview-using-powershell"></a>Vypnutí verze Preview pomocí PowerShellu
Pokud ho ještě nemáte, nainstalujte nejnovější modul AzureADPreview (viz [předpoklad: Nainstalujte nejnovější modul AzureADPreview](#prerequisite-install-the-latest-azureadpreview-module) výše). Pak ověřte, že aktuálně existuje zásada jednorázového náhledu hesla, a to spuštěním následujícího:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Pokud má výstup hodnotu true, odhlaste se od verze Preview spuštěním následujícího:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

