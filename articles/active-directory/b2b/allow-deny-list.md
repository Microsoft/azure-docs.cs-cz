---
title: Povolení nebo blokování pozvánek do konkrétních organizací – Azure AD
description: Ukazuje, jak může správce pomocí portálu Azure nebo PowerShellu nastavit seznam přístupu nebo odepření pro povolení nebo blokování uživatelů B2B z určitých domén.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/15/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b5100c4406cfd4a8395dfa177dc3cd5e911decb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273427"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Povolení nebo blokování pozvánek pro uživatele B2B z konkrétních organizací

Seznam povolených položek nebo seznam zamítnutí můžete použít k povolení nebo blokování pozvánek pro uživatele B2B z konkrétních organizací. Chcete-li například blokovat domény osobních e-mailových adres, můžete nastavit seznam odepření, který obsahuje domény, jako jsou Gmail.com a Outlook.com. Pokud má vaše firma partnerství s jinými firmami, jako jsou Contoso.com, Fabrikam.com a Litware.com, a chcete omezit pozvánky pouze na tyto organizace, můžete do seznamu povolených položek přidat Contoso.com, Fabrikam.com a Litware.com.
  
## <a name="important-considerations"></a>Důležité informace

- Můžete vytvořit seznam povolených nebo odepřít. Nelze nastavit oba typy seznamů. Ve výchozím nastavení jsou libovolné domény, které nejsou v seznamu povolených domén, v seznamu odepřít a naopak. 
- Pro jednu organizaci můžete vytvořit pouze jednu zásadu. Zásadu můžete aktualizovat tak, aby zahrnovala více domén, nebo můžete zásadu odstranit a vytvořit novou. 
- Počet domén, které můžete přidat do seznamu povolených nebo odepřít, je omezen pouze velikostí zásady. Maximální velikost celé zásady je 25 kB (25 000 znaků), která zahrnuje seznam povolených nebo povolených položek a všechny další parametry nakonfigurované pro další funkce.
- Tento seznam funguje nezávisle na seznamech povolených a blokovaných OneDrivu pro firmy a SharePointu Online. Pokud chcete omezit sdílení jednotlivých souborů v SharePointu Online, musíte nastavit seznam povolených nebo povolavých položek pro OneDrive pro firmy a SharePoint Online. Další informace najdete [v tématu Omezené domény sdílení v SharePointu Online a OneDrivu pro firmy](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Seznam se nevztahuje na externí uživatele, kteří již pozvánku uplatnili. Seznam bude vynucen po nastavení seznamu. Pokud je pozvánka uživatele ve stavu čekající na vyřízení a nastavíte zásadu, která blokuje jejich doménu, pokus uživatele o uplatnění pozvánky se nezdaří.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Nastavení zásad povolit nebo odepřít seznam na portálu

Ve výchozím nastavení je povoleno nastavení **Povolit odeslání pozvánek do libovolné domény (nejvíce včetně).** V takovém případě můžete pozvat uživatele B2B z libovolné organizace.

### <a name="add-a-deny-list"></a>Přidání seznamu zamítnutí

Toto je nejtypičtější scénář, kdy vaše organizace chce pracovat s téměř jakoukoli organizací, ale chce zabránit uživatelům z konkrétních domén, které mají být pozváni jako uživatelé B2B.

Přidání seznamu zamítnutí:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte**nastavení uživatele****služby** >  **Azure Active Directory** > .
3. V části **Externí uživatelé**vyberte **Spravovat nastavení externí spolupráce**.
4. V části **Omezení spolupráce**vyberte Možnost **Odepřít pozvánky do určených domén**.
5. V **části TARGET DOMAINS**zadejte název jedné z domén, které chcete blokovat. Pro více domén zadejte každou doménu na novém řádku. Například:

   ![Zobrazuje možnost odepřít s přidanými doménami.](./media/allow-deny-list/DenyListSettings.png)
 
6. Až budete hotovi, klikněte na **Uložit**.

Po nastavení zásady, pokud se pokusíte pozvat uživatele z blokované domény, zobrazí se zpráva, že doména uživatele je aktuálně blokována zásadami pozvání.
 
### <a name="add-an-allow-list"></a>Přidání seznamu povolených položek

Jedná se o více omezující konfiguraci, kde můžete nastavit konkrétní domény v seznamu povolených položek a omezit pozvánky na jiné organizace nebo domény, které nejsou uvedeny. 

Pokud chcete použít seznam povolených položek, ujistěte se, že trávíte čas, abyste plně vyhodnotili, jaké jsou vaše obchodní potřeby. Pokud tyto zásady upravíte příliš restriktivní, mohou se uživatelé rozhodnout odeslat dokumenty prostředem e-mailu nebo najít jiné způsoby spolupráce, které nejsou schváleny IT.


Přidání seznamu povolených položek:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte**nastavení uživatele****služby** >  **Azure Active Directory** > .
3. V části **Externí uživatelé**vyberte **Spravovat nastavení externí spolupráce**.
4. V části **Omezení spolupráce**vyberte **Povolit pozvánky pouze do určených domén (nejvíce omezujících).**
5. V **části TARGET DOMAINS**zadejte název jedné z domén, které chcete povolit. Pro více domén zadejte každou doménu na novém řádku. Například:

   ![Zobrazuje možnost povolit s přidanými doménami.](./media/allow-deny-list/AllowListSettings.png)
 
6. Až budete hotovi, klikněte na **Uložit**.

Po nastavení zásady, pokud se pokusíte pozvat uživatele z domény, která není na seznamu povolených, zobrazí se zpráva, že doména uživatele je aktuálně blokována zásadami pozvání.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Přepnutí ze seznamu povolených zamítnutí a naopak 

Pokud přepnete z jedné zásady na druhou, zahodí se existující konfigurace zásad. Před provedením přepínače nezapomeňte zálohovat podrobnosti o konfiguraci. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Nastavení zásad povolit nebo odepřít seznam pomocí Prostředí PowerShell

### <a name="prerequisite"></a>Požadavek

> [!Note]
> Modul AzureADPreview není plně podporovaný modul, jak je ve verzi Preview. 

Chcete-li nastavit seznam povolených nebo popřených pomocí prostředí PowerShell, je nutné nainstalovat verzi preview modulu Azure Active Directory Module pro prostředí Windows PowerShell. Konkrétně nainstalujte modul AzureADPreview verze 2.0.0.98 nebo novější.

Chcete-li zkontrolovat verzi modulu (a zjistit, zda je nainstalován):
 
1. Spusťte prostředí Windows PowerShell jako uživatele se zvýšenými oprávněními (Spustit jako správce). 
2. Spusťte následující příkaz a zjistěte, jestli máte v počítači nainstalované nějaké verze modulu Azure Active Directory Module pro prostředí Windows PowerShell:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Pokud modul není nainstalován nebo nemáte požadovanou verzi, proveďte jednu z následujících akcí:

- Pokud nejsou vráceny žádné výsledky, spusťte následující příkaz k instalaci nejnovější verze modulu AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Pokud je ve výsledcích zobrazen pouze modul AzureAD, spusťte následující příkazy k instalaci modulu AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Pokud je ve výsledcích zobrazen pouze modul AzureADPreview, ale verze je menší než 2.0.0.98, aktualizujte ho následujícími příkazy: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Pokud jsou ve výsledcích zobrazeny moduly AzureAD i AzureADPreview, ale verze modulu AzureADPreview je menší než 2.0.0.98, spusťte následující příkazy k jeho aktualizaci: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Ke konfiguraci zásad použijte rutiny AzureADPolicy

Chcete-li vytvořit seznam povolených nebo odepřít, použijte rutinu [New-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) Následující příklad ukazuje, jak nastavit seznam odepřít, který blokuje doménu "live.com".

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Následující ukazuje stejný příklad, ale s definice zásady v řádku.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Chcete-li nastavit zásady povolit nebo odepřít seznam, použijte rutinu [Set-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) Například:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

Chcete-li získat zásady, použijte rutinu [Get-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) Například:

```powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

Chcete-li zásadu odebrat, použijte rutinu [Remove-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) Například:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>Další kroky

- Přehled Azure AD B2B najdete v tématu [Co je spolupráce Azure AD B2B?](what-is-b2b.md)
- Informace o podmíněném přístupu a spolupráci b2B naleznete [v tématu Podmíněný přístup pro uživatele spolupráce B2B](conditional-access.md).



