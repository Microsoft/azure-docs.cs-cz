---
title: Ověřování jednorázovým heslem pro uživatele typu Host B2B – Azure Active Directory | Dokumentace Microsoftu
description: Jak použít jednorázové heslo e-mailu k ověření uživatelů typu Host B2B bez účtu Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 1/25/2019
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.openlocfilehash: c6bfa9acb29820a2c9646d6302871bb6a2dd1ac6
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982161"
---
# <a name="email-one-time-passcode-authentication-preview"></a>Ověření e-mailu jednorázové heslo (preview)

|     |
| --- |
| Jednorázové heslo e-mailu je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Funkce jednorázové heslo e-mailu ověří uživatele typu Host B2B, když nemůže být ověřen jiným způsobem, jako je Azure AD, účet Microsoft (MSA) nebo federační služby Google. S ověřováním jednorázovým heslem není nutné vytvořit účet Microsoft. Když uživatel typu Host uplatňuje pozvánku nebo přistupuje ke sdílenému prostředku, můžete požádat o dočasný kód, která je odeslána na e-mailová adresa. Pak zadejte tento kód Pokračujte přihlášením.

Tato funkce je aktuálně dostupná ve verzi preview (viz [vyjádření výslovného souhlasu s Náhled](#opting-in-to-the-preview) níže). Za verzi preview tato funkce bude mít ve výchozím nastavení zapnutá pro všechny tenanty.

> [!NOTE]
> Jednorázové heslo uživatele musíte se přihlásit pomocí odkazu, který zahrnuje kontextu tenanta (například `https://myapps.microsoft.com/?tenantid=<tenant id>` nebo `https://portal.azure.com/<tenant id>`, nebo v případě ověřenou doménu, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Přímé odkazy k aplikacím a prostředkům také fungovat tak dlouho, dokud zahrnují kontextu tenanta. K přihlášení pomocí koncových bodů, které nemají žádný kontext tenanta v tuto chvíli nedaří uživatele typu Host. Například použití `https://myapps.microsoft.com`, `https://portal.azure.com`, nebo koncový bod pro běžné týmy způsobí chybu. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Činnost koncového uživatele pro uživatele typu Host jednorázové heslo
S ověřováním jednorázovým heslem může uživatel typu Host uplatnit pozvání, kliknutím na přímý odkaz nebo s použitím e-mailová pozvánka. V obou případech se zobrazí v prohlížeči se zpráva, že kód se pošle e-mailovou adresu uživatele typu Host. Uživatel typu Host vybere **poslat kód**:
 
   ![Správa přístupových panelech aplikace](media/one-time-passcode/otp-send-code.png)
 
Heslo se odešle e-mailovou adresu uživatele. Uživatel načte hesla v e-mailu a vloží jej do okna prohlížeče:
 
   ![Správa přístupových panelech aplikace](media/one-time-passcode/otp-enter-code.png)
 
Uživatel typu Host je nyní ověřen a můžete zobrazit sdílený prostředek nebo pokračovat v přihlašování. 

> [!NOTE]
> Jednorázového hesla jsou platné po dobu 30 minut. Po 30 minutách již není platný tento konkrétní jednorázového hesla a uživatel musí požádat o nový. Uživatelské relace vyprší za 24 hodin. Po tomto datu se uživatel typu Host obdrží nové heslo při přístupu k prostředku. Vypršení platnosti relace poskytuje dodatečné zabezpečení, zejména v případě, že uživatel typu Host opustí společnost nebo už nepotřebuje přístup.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Když uživatel typu Host získat jednorázové heslo?

Když uživatel typu Host uplatňuje pozvánku nebo používá odkaz na prostředek, který s nimi někdo sdílí, obdrží jednorázové heslo, pokud:
- Nemají účet Azure AD 
- Nemají účet Microsoft 
- Pozvání tenanta nenastavili Google federaci pro @gmail.com a @googlemail.com uživatelů 

V době pozvánky neexistuje žádné označení, že uživatel, kterého jste pozvání použije ověřování jednorázovým heslem. Ale při přihlášení uživatele typu Host, ověřování jednorázovým heslem bude náhradní metody, pokud žádné jiné metody ověřování, je možné. 

Můžete zobrazit uživatele typu Host, kteří používají ověřování pomocí jednorázových hesel na webu Azure Portal tak, že přejdete do **Azure Active Directory** > **organizační vztahy**  >   **Uživatelé z jiných organizací**.

![Zobrazit jednorázové heslo uživatele na webu Azure Portal, zdroje se rovná hodnotě ověřování jednorázovým HESLEM](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Když uživatel uplatňuje jednorázové heslo a později získá MSA, účet Azure AD nebo jiný federovaný účet, budou dál možné ověřit pomocí jednorázového hesla. Pokud chcete aktualizovat své metody ověřování, můžete odstranit jeho uživatelský účet guest a znovu je pozvat.

### <a name="example"></a>Příklad:
Uživatel typu Host alexdoe@gmail.com organizace pozvaný ke společnosti Fabrikam, který nemá nastavení federace služby Google. Alex nemá účet Microsoft. Zobrazí mu pro ověřování jednorázovým heslem.

## <a name="opting-in-to-the-preview"></a>Vyjádření výslovného souhlasu ve verzi Preview 
Může trvat několik minut, než akce opt-in se projeví. Poté použije pouze nově pozvaným uživatelům, kteří splňují podmínky výše ověřování jednorázovým heslem. Uživatelé typu Host, kteří dříve uplatnit pozvání bude nadále používat stejnou metodu ověření.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>K přihlášení na portálu Azure AD
1.  Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce Azure AD.
2.  V navigačním podokně vyberte **Azure Active Directory**.
3.  V části **spravovat**vyberte **organizační vztahy**.
4.  Vyberte **nastavení**.
5.  V části **povolit e-mailu jednorázového hesla pro hosty (Preview)** vyberte **Ano**.
 
### <a name="to-opt-in-using-powershell"></a>K přihlášení pomocí Powershellu

Nejprve budete muset nainstalovat nejnovější verzi Azure AD PowerShell pro modul grafu (AzureADPreview). Pak je nutné určit, jestli zásady B2B ještě neexistuje a spusťte odpovídající příkazy.

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

#### <a name="check-for-existing-policies-and-opt-in"></a>Vyhledejte existující zásady a vyjádřit výslovný souhlas

V dalším kroku zkontrolujte, jestli aktuálně existuje B2BManagementPolicy spuštěním následujícího:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Pokud výstupem je False, neexistuje aktuálně zásady. Vytvořte nový B2BManagementPolicy a přihlásit se k verzi preview spuštěním následujícího:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Pokud výstupem je hodnota True, zásady B2BManagementPolicy aktuálně existuje. Aktualizovat zásady a přihlásit se k verzi preview, spusťte následující příkaz:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Výslovné odhlášení z verze preview po aktivaci ochrany
Může trvat několik minut, než akce výslovného nesouhlasu se projeví. Pokud vypnete verzi preview, všechny uživatele typu Host, kteří mají uplatnit jednorázové heslo nebudou moct přihlásit. Můžete odstranit uživatele typu Host a znovu pozvat uživatele, aby se mohly přihlásit znovu použít jinou metodu ověřování.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Chcete-li vypnout náhled pomocí portálu Azure AD
1.  Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce Azure AD.
2.  V navigačním podokně vyberte **Azure Active Directory**.
3.  V části **spravovat**vyberte **organizační vztahy**.
4.  Vyberte **nastavení**.
5.  V části **povolit e-mailu jednorázového hesla pro hosty (Preview)** vyberte **ne**.

### <a name="to-turn-off-the-preview-using-powershell"></a>Chcete-li vypnout náhled pomocí Powershellu
Pokud ho ještě nemáte, nainstalujte nejnovější modulu AzureADPreview (viz [požadovaných součástí: Instalace nejnovější modulu AzureADPreview](#prerequisite-install-the-latest-azureadpreview-module) výše). Pak ověřte, že zásady jednorázové heslo ve verzi preview momentálně existuje spuštěním následujícího:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Pokud výstupem je hodnota True, vyjádřit výslovný nesouhlas náhledu spuštěním následujícího:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

