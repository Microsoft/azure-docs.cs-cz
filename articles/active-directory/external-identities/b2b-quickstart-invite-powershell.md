---
title: 'Rychlý Start: Přidání uživatele typu Host přes PowerShell – Azure AD'
description: V tomto rychlém startu se dozvíte, jak použít PowerShell k odeslání pozvánky externímu uživateli spolupráce B2B služby Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0faaf0394bddc2e443afc194bbd0ecef72625f9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87906051"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>Rychlý Start: Přidání uživatele typu host pomocí PowerShellu

Existuje mnoho způsobů, jak pomocí spolupráce B2B služby Azure Active Directory pozvat externí partnery k vašim aplikacím a službám. V předchozím rychlém startu jste zjistili, jak na portálu pro správu Azure Active Directory přidat uživatele typu host přímo. Uživatele typu host můžete přidat taky pomocí PowerShellu, a to buď po jednom nebo hromadně. V tomto rychlém startu přidáte do tenanta Azure pomocí příkazu New-AzureADMSInvitation jednoho uživatele typu host.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Předpoklady

### <a name="install-the-latest-azureadpreview-module"></a>Instalace nejnovějšího modulu AzureADPreview
Přesvědčte se, že instalujete nejnovější verzi modulu Azure AD PowerShell pro Graph (AzureADPreview). 

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

### <a name="get-a-test-email-account"></a>Získání testovacího e-mailového účtu

Potřebujete testovací e-mailový účet, na který můžete pozvánku odeslat. Účet se nesmí nacházet ve vaší organizaci. Můžete použít libovolný typ účtu včetně sociálních účtů, jako jsou adresy gmail.com nebo outlook.com.

## <a name="sign-in-to-your-tenant"></a>Přihlášení k tenantovi

Spuštěním následujícího příkazu se připojte k doméně klienta:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Například, `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Po zobrazení výzvy zadejte své přihlašovací údaje.

## <a name="send-an-invitation"></a>Odeslání pozvánky

1. Pokud chcete poslat pozvánku na testovací e-mailový účet, spusťte následující příkaz PowerShellu (nahraďte **"písku"** a **písku \@ fabrikam.com** názvem vašeho testovacího e-mailového účtu a e-mailovou adresou): 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.microsoft.com -SendInvitationMessage $true
   ```
2. Příkaz pozvánku odešle na zadanou e-mailovou adresu. Zkontrolujte výstup, který by měl vypadat nějak takto:

   ![Výstup PowerShellu zobrazující zatím neschválené pozvání uživatele](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>Ověření existence uživatele v adresáři

1. Pokud chcete ověřit, zda se uživatel do Azure AD přidal, spusťte následující příkaz:
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. Zkontrolujte výstup, abyste se ujistili, že je uvedený uživatel, který jste pozvali, a hlavní název uživatele (UPN) ve formátu *emailaddress*#EXT # \@ *doména*. Například *sanda_fabrikam. com # ext # \@ contoso.onmicrosoft.com*, kde contoso.onmicrosoft.com je organizace, ze které jste pozvánky odeslali.

   ![Výstup PowerShellu zobrazující přidaného uživatele typu host](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už testovací uživatelský účet v adresáři nepotřebujete, můžete ho odstranit. Uživatelský účet odstraníte spuštěním následujícího příkazu:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Příklad: `Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste pomocí PowerShellu pozvali a přidali jednoho uživatele typu host do adresáře. Dále se pomocí PowerShellu naučíte pozvat uživatele typu host hromadně.

> [!div class="nextstepaction"]
> [Kurz: Hromadné pozvání uživatelů spolupráce B2B služby Azure AD](tutorial-bulk-invite.md)
