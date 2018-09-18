---
title: Povolení nebo blokování pozvánek uživatelů B2B z konkrétních organizací – Azure Active Directory | Dokumentace Microsoftu
description: Ukazuje, jak správce může pomocí webu Azure portal nebo prostředí PowerShell k nastavení přístupu nebo zamítnout seznam pro povolení nebo blokování uživatelů B2B z určitých domén.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 563b2d6393533a86305213b6cdec6ca901e53257
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985375"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Povolování nebo blokování pozvánek uživatelů B2B z konkrétních organizací

Seznam povolených nebo seznam zakázaných slouží k povolení nebo blokování pozvánek uživatelů B2B z konkrétních organizací. Například pokud chcete blokovat domény osobní e-mailových adres, nastavením seznamu Odepřít, která obsahuje domény, jako jsou Gmail.com a Outlook.com. Nebo, pokud vaše firma partnerství s jiným společnostem, třeba Contoso.com, Fabrikam.com a Litware.com a chcete zakázat zasílání pozvánek jen tyto organizace, můžete přidat Contoso.com, Fabrikam.com a Litware.com do vašeho seznamu povolených tříd.
  
## <a name="important-considerations"></a>Důležité informace

- Můžete vytvořit seznam povolených nebo seznam zakázaných. Nejde nastavit oba typy seznamů. Ve výchozím nastavení, ať domény nejsou v seznamu povolených položek jsou v seznamu Odepřít a naopak. 
- Můžete vytvořit pouze jednu zásadu pro každou organizaci. Můžete aktualizovat zásady tak, aby zahrnout další domény nebo vytvořte novou zásadu můžete odstranit. 
- Tento seznam funguje z Onedrivu pro firmy a Sharepointu Online seznamů povolení/blokování nezávisle na sobě. Pokud chcete omezit jednotlivých sdílení souborů v Sharepointu Online, musíte povolit nebo zamítnout seznam pro OneDrive pro firmy a Sharepointu Online. Další informace najdete v tématu [domén v Sharepointu Online a OneDrive pro firmy pro sdílení obsahu s omezením pomocí specifikátoru](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Tento seznam se nevztahuje na externí uživatelé, kteří už využili. e-mailové pozvánce. V seznamu se vynutí po nastavení seznamu. Pokud pozvání uživatele je ve stavu čekání a nastavit zásady, které blokuje svou doménu, se nezdaří pokus o pozvánku uplatnit uživatele.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Nastavení povolit nebo zamítnout seznam zásady na portálu

Ve výchozím nastavení **umožnit zasílání pozvánek na jakoukoli doménu (nejvolnější)** je povolené nastavení. V takovém případě můžete pozvat uživatele B2B z jakékoli organizace.

### <a name="add-a-deny-list"></a>Přidat seznam zakázaných

Toto je obvykle scénář, ve kterém vaše organizace chce, aby se pro práci s téměř v jakékoli organizaci, ale chce, aby se zabránilo uživatelům z konkrétní domény pozvat jako uživatele B2B.

Chcete-li přidat seznam zakázaných:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **Azure Active Directory** > **uživatelé** > **uživatelská nastavení**.
3. V části **externích uživatelů**vyberte **spravovat nastavení externí spolupráce**.
4. V části **omezení spolupráce**vyberte **zakázat zasílání pozvánek na konkrétní domény**.
5. V části **cílové domény**, zadejte název domény, které chcete blokovat. Pro více domén zadejte každou doménu na nový řádek. Příklad:

   ![Zobrazuje odepřít možnost přidání domén](./media/allow-deny-list/DenyListSettings.png)
 
6. Jakmile budete hotovi, klikněte na tlačítko **Uložit**.

Po nastavení zásad, pokud se pokusíte pozvat uživatele z blokovaných domény obdržet zprávu, že doména uživatele v tuto chvíli blokují vaše zásady pozvánky.
 
### <a name="add-an-allow-list"></a>Přidat seznam povolených

To je více omezující konfiguraci, kde můžete nastavit konkrétní domény v seznamu povolených a omezit pozvánky do jiných organizací nebo doménách, které nejsou uvedené. 

Pokud chcete použít seznam povolených, ujistěte se, že budete trávit čas plně vyhodnotit potřeby vaší firmy jsou. Pokud provedete tyto zásady příliš omezující, uživatelé mohou rozhodnout odesílat dokumenty prostřednictvím e-mailu nebo vyhledejte další bez IT schválené způsoby spolupráce.


Přidání seznamu povolených položek:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **Azure Active Directory** > **uživatelé** > **uživatelská nastavení**.
3. V části **externích uživatelů**vyberte **spravovat nastavení externí spolupráce**.
4. V části **omezení spolupráce**vyberte **umožnit zasílání pozvánek jen na konkrétní domény (nejvíce omezující)**.
5. V části **cílové domény**, zadejte název domény, které chcete povolit. Pro více domén zadejte každou doménu na nový řádek. Příklad:

   ![Ukazuje možnost povolit přidání domén](./media/allow-deny-list/AllowListSettings.png)
 
6. Jakmile budete hotovi, klikněte na tlačítko **Uložit**.

Po nastavení zásad, pokud se pokusíte pozvat uživatele z domény, který není na seznamu povolených, můžete obdržet zprávu, že doména uživatele v tuto chvíli blokují vaše zásady pozvánky.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Přepněte z povolit na zamítnout seznam a naopak 

Pokud přepnete z jedněch zásad do druhého to zahodí existující konfiguraci zásad. Ujistěte se, že pro zálohování podrobnosti o konfiguraci, před provedením přepínač. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Nastavení povolit nebo zamítnout seznam zásady pomocí Powershellu

### <a name="prerequisite"></a>Požadavek

Nastavení povolit nebo odepřít seznamu s použitím prostředí PowerShell, musíte nainstalovat verzi preview Azure Active Directory modulu pro Windows PowerShell. Konkrétně, instalace verze modulu 2.0.0.98 AzureADPreview nebo novější.

Zkontrolujte verzi modulu (a zjistit, zda je nainstalována):
 
1. Otevřete prostředí Windows PowerShell se zvýšenými oprávněními uživatele (Spustit jako správce). 
2. Spuštěním následujícího příkazu zkontrolujte, jestli je verze Azure Active Directory modulu pro Windows PowerShell v počítači nainstalovaný:

   ````powershell  
   Get-Module -ListAvailable AzureAD*
   ````

Pokud modul není nainstalován nebo nemáte požadovaná verze, proveďte jednu z následujících akcí:

- Pokud se nezobrazí žádné výsledky, spusťte následující příkaz k instalaci nejnovější verze modulu AzureADPreview:
  
   ````powershell  
   Install-Module AzureADPreview
   ````
- Pokud pouze v modulu Azure AD je zobrazena ve výsledcích, spusťte následující příkazy pro instalaci modulu AzureADPreview: 

   ````powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ````
- Pokud pouze v modulu AzureADPreview je zobrazena ve výsledcích, ale verze je menší než 2.0.0.98, spusťte následující příkazy ji aktualizovat: 

   ````powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ````

- Pokud Azure AD i AzureADPreview modulů se zobrazí ve výsledcích, ale verze modulu AzureADPreview je menší než 2.0.0.98, spusťte následující příkazy ji aktualizovat: 

   ````powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ````

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Nakonfigurujte zásady pomocí rutin AzureADPolicy

Chcete-li vytvořit povolit nebo odepřít seznamu, použijte [New-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) rutiny. Následující příklad ukazuje, jak nastavit seznam zakázaných blokující doménu "live.com".

````powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

Následující příklad zobrazuje stejný příklad, ale s vložené definice zásady.

````powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

Chcete-li nastavit povolit nebo zamítnout seznam zásad, použijte [Set-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) rutiny. Příklad:

````powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
````

Chcete-li získat zásady, použijte [Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) rutiny. Příklad:

````powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
````

Chcete-li odebrat zásady, použijte [odebrat AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) rutiny. Příklad:

````powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
````

## <a name="next-steps"></a>Další postup

- Přehled Azure AD s B2B, naleznete v tématu [co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- Informace o podmíněném přístupu a spolupráce B2B najdete v tématu [podmíněného přístupu pro uživatele spolupráce B2B](conditional-access.md).



