---
title: Povolení nebo blokování pozvánek konkrétním organizacím – Azure AD
description: Ukazuje, jak může správce pomocí Azure Portal nebo PowerShellu nastavit přístup nebo seznam zamítnutí a povolit nebo zablokovat uživatelům B2B z určitých domén.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 07/15/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cc336781e9a55bbcb6c51677b01bfc402126f4a
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071896"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Povolení nebo blokování pozvánek uživatelům B2B z konkrétních organizací

Pomocí seznamu povolených nebo zakázaných seznamů můžete povolit nebo zablokovat pozvánky uživatelům B2B z konkrétních organizací. Například pokud chcete blokovat domény osobní e-mailové adresy, můžete nastavit seznam odepření, který obsahuje domény, jako je Gmail.com a Outlook.com. Nebo pokud má vaše firma partnerství s jinými společnostmi, jako jsou Contoso.com, Fabrikam.com a Litware.com, a chcete omezit pozvánky jenom na tyto organizace, můžete do seznamu povolených aplikací přidat Contoso.com, Fabrikam.com a Litware.com.
  
## <a name="important-considerations"></a>Důležité informace

- Můžete vytvořit seznam povolených nebo zakázaných. Nemůžete nastavit oba typy seznamů. Ve výchozím nastavení nejsou žádné domény v seznamu povolených v seznamu povolených aplikací a naopak. 
- V každé organizaci můžete vytvořit jenom jednu zásadu. Zásady můžete aktualizovat tak, aby zahrnovaly více domén, nebo můžete zásadu odstranit a vytvořit novou. 
- Počet domén, které můžete přidat do seznamu povolených nebo zakázaných domén, je omezený jenom o velikost zásad. Maximální velikost celé zásady je 25 KB (25 000 znaků), která zahrnuje seznam povolených nebo zakázaných položek a všechny další parametry nakonfigurované pro jiné funkce.
- Tento seznam funguje nezávisle na seznamu povolených a blokovaných seznamů OneDrivu pro firmy a SharePointu Online. Pokud chcete omezit individuální sdílení souborů v SharePointu Online, musíte nastavit seznam povolených nebo odepřených aplikací pro OneDrive pro firmy a SharePoint Online. Další informace najdete v tématu [sdílení domén s omezeným přístupem v SharePointu Online a OneDrivu pro firmy](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Seznam se nevztahuje na externí uživatele, kteří už tuto pozvánku provedli. Seznam se vynutil po nastavení seznamu. Pokud je pozvánka uživatele ve stavu čekání na vyřízení a nastavíte zásady, které blokují svou doménu, pokus uživatele o uplatnění pozvánky se nezdaří.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Nastavení zásady povolit nebo odepřít seznam na portálu

Ve výchozím nastavení je povolená možnost **Povolit odesílání pozvánek do všech domén (nejčastěji)** . V takovém případě můžete pozvat uživatele B2B z jakékoli organizace.

### <a name="add-a-deny-list"></a>Přidat seznam odepření

Toto je nejběžnější scénář, ve kterém vaše organizace potřebuje pracovat s téměř libovolnou organizací, ale chce zabránit uživatelům v konkrétních doménách, aby byli pozváni jako uživatelé B2B.

Přidání seznamu odepření:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vyberte **Azure Active Directory**  >    >  **uživatelská nastavení** uživatelů.
3. V části **externí uživatelé** vyberte **Spravovat nastavení externí spolupráce**.
4. V části **omezení spolupráce** vyberte **Odepřít pozvánky zadaným doménám**.
5. V části **cílové domény** zadejte název jedné z domén, které chcete blokovat. V případě více domén zadejte každou doménu na nový řádek. Příklad:

   ![Zobrazí možnost Odepřít u přidaných domén.](./media/allow-deny-list/DenyListSettings.png)
 
6. Až skončíte, klikněte na **Uložit**.

Pokud se po nastavení zásady pokusíte pozvat uživatele z Blokované domény, zobrazí se zpráva s informací o tom, že je doména uživatele v tuto chvíli zablokovaná vašimi zásadami pro pozvánky.
 
### <a name="add-an-allow-list"></a>Přidat seznam povolených

Jedná se o přísnější konfiguraci, kde můžete nastavit konkrétní domény v seznamu povolených a omezit pozvánky na jakékoli jiné organizace nebo domény, které nejsou zmíněné. 

Pokud chcete použít seznam povolených, ujistěte se, že strávíte čas potřebný k úplnému vyhodnocení vašich obchodních potřeb. Pokud tuto zásadu nastavíte příliš omezující, můžou si uživatelé odeslat dokumenty přes e-mail nebo najít jiné neschválené způsoby spolupráce.


Chcete-li přidat seznam povolených:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vyberte **Azure Active Directory**  >    >  **uživatelská nastavení** uživatelů.
3. V části **externí uživatelé** vyberte **Spravovat nastavení externí spolupráce**.
4. V části **omezení spolupráce** vyberte možnost **povoluje pozvánky pouze do zadaných domén (nejvíce omezující)**.
5. V části **cílové domény** zadejte název jedné z domén, které chcete udělit. V případě více domén zadejte každou doménu na nový řádek. Příklad:

   ![Zobrazí možnost Allow s přidanými doménami.](./media/allow-deny-list/AllowListSettings.png)
 
6. Až skončíte, klikněte na **Uložit**.

Pokud se po nastavení zásady pokusíte pozvat uživatele z domény, která není v seznamu povolených, zobrazí se zpráva s informací o tom, že je doména uživatele v tuto chvíli zablokovaná vašimi zásadami pro pozvánky.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Přepnutí z povoleného seznamu povolit na odepření a naopak 

Pokud přepnete z jedné zásady na druhou, odstraní se stávající konfigurace zásad. Před provedením přepínače nezapomeňte zálohovat podrobnosti o konfiguraci. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Nastavení zásady povolit nebo odepřít seznam pomocí prostředí PowerShell

### <a name="prerequisite"></a>Požadavek

> [!Note]
> Modul AzureADPreview není plně podporovaný modul, protože je ve verzi Preview. 

Pokud chcete nastavit seznam povolených nebo zakázaných aplikací pomocí PowerShellu, musíte nainstalovat verzi Preview modulu Azure Active Directory pro Windows PowerShell. Konkrétně nainstalujte modul AzureADPreview verze 2.0.0.98 nebo novější.

Chcete-li zjistit verzi modulu (a zjistit, zda je nainstalován):
 
1. Spusťte Windows PowerShell jako uživatel se zvýšenými oprávněními (Spustit jako správce). 
2. Spuštěním následujícího příkazu zjistíte, jestli máte na svém počítači nainstalovanou nějaké verze modulu Azure Active Directory pro Windows PowerShell:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Pokud modul není nainstalován nebo nemáte požadovanou verzi, proveďte jednu z následujících akcí:

- Pokud se nevrátí žádné výsledky, spusťte následující příkaz, který nainstaluje nejnovější verzi modulu AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Pokud se ve výsledcích zobrazí jenom modul AzureAD, spusťte následující příkazy, abyste nainstalovali modul AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Pokud se ve výsledcích zobrazí jenom modul AzureADPreview, ale verze je menší než 2.0.0.98, spusťte následující příkazy a aktualizujte je: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Pokud se ve výsledcích zobrazí oba moduly AzureAD a AzureADPreview, ale verze modulu AzureADPreview je menší než 2.0.0.98, spusťte následující příkazy a aktualizujte je: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Konfigurace zásad pomocí rutin AzureADPolicy

Chcete-li vytvořit seznam povolených nebo odepřených, použijte rutinu [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) . Následující příklad ukazuje, jak nastavit seznam odepření, který blokuje doménu "live.com".

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

V následujícím příkladu vidíte stejný příklad, ale s vloženou definicí zásad.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Pokud chcete nastavit zásadu seznamu povolených nebo zakázaných, použijte rutinu [set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) . Příklad:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

K získání této zásady použijte rutinu [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) . Příklad:

```powershell
$currentpolicy = Get-AzureADPolicy -All $true | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

Pokud chcete zásadu odebrat, použijte rutinu [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) . Příklad:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>Další kroky

- Přehled služby Azure AD B2B najdete v tématu [co je spolupráce B2B Azure AD?](what-is-b2b.md)
- Informace o službě podmíněný přístup a spolupráci B2B najdete v tématu [podmíněný přístup pro uživatele spolupráce B2B](conditional-access.md).
