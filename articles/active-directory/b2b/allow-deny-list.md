---
title: Povolit nebo blokovat pozvánek uživatelům B2B z konkrétní organizací – Azure Active Directory | Microsoft Docs
description: Ukazuje, jak může správce pomocí portálu Azure nebo PowerShell nastavit přístup nebo odepřít seznamu chcete povolit nebo blokovat B2B uživatelé z některých domén.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/19/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 3b4b57dd2299c6278fe823f59a4f2c7d8721f712
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259997"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Povolování nebo blokování pozvánek uživatelů B2B z konkrétních organizací

Seznam povolených nebo zakázaných adres slouží k blokovaných nebo povolených pozvánek uživatelům B2B z konkrétní organizací. Například pokud chcete zablokovat domény, osobní e-mailovou adresu, můžete nastavit seznamu Odepřít, která obsahuje domény jako Gmail.com a Outlook.com. Nebo, pokud váš podnik má partnerství s konkurence jako Contoso.com, Fabrikam.com a Litware.com a chcete omezit pozvánek k jenom tyto organizace, můžete přidat Contoso.com, Fabrikam.com a Litware.com do vašeho seznamu povolených.
  
## <a name="important-considerations"></a>Důležité informace

- Můžete vytvořit seznam povolených nebo zakázaných adres. Nelze nastavit oba typy seznamů. Ve výchozím nastavení, ať domény nejsou v seznamu povolených nejsou na seznamu se zákazem a naopak. 
- Můžete vytvořit pouze jednu zásadu pro organizaci. Můžete aktualizovat zásadu, která bude obsahovat více domén, nebo vytvořte novou zásadu můžete odstranit. 
- Tento seznam funguje z Onedrivu pro firmy a Sharepointu Online seznamů povolených a blokovaných nezávisle. Pokud chcete omezit sdílení ve službě SharePoint Online jednotlivých souborů, budete muset nastavit povolit nebo odepřít seznamu pro OneDrive pro firmy a Sharepointu Online. Další informace najdete v tématu [omezený domén sdílení v SharePoint Online a OneDrive pro firmy](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Tento seznam se nevztahuje na externí uživatelé, kteří už byl uplatněn pozvánku. V seznamu se vynutí po seznamu nastavení. Pokud uživatel pozvánku je ve stavu čekání a nastavit zásady, které blokuje svoji doménu, uživatele pokus o uplatnit pozvánku se nezdaří.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Nastavit povolit nebo odepřít seznamu zásada na portálu

Ve výchozím nastavení **povolit zasílání požadavků do jakékoli domény (včetně nejvíce)** nastavení povolené. V takovém případě můžete uživatele pozvat, B2B z některé z organizací.

### <a name="add-a-deny-list"></a>Přidat seznam zakázaných

Toto je Nejobvyklejším scénářem, kde vaše organizace chce pracovat s téměř všechny organizace, ale chce chránit uživatelům z konkrétní domény pozvat jako uživatelé B2B.

Chcete-li přidat seznam zakázaných:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **Azure Active Directory** > **uživatelé** > **uživatelská nastavení**.
3. V části **externí uživatelé**, vyberte **spravovat nastavení externí spolupráce**.
4. V části **spolupráce omezení**, vyberte **odepřít pozvánek k doménám, zadaný**.
5. V části **cílové domény**, zadejte název jednoho z domén, které chcete blokovat. Pro několik domén zadejte na nový řádek každou doménu. Příklad:

   ![Zobrazuje možnost Odepřít přidání domény](./media/allow-deny-list/DenyListSettings.png)
 
6. Když jste hotovi, klikněte na tlačítko **Uložit**.

Jakmile nastavíte zásady, pokud se pokusíte pozvat uživatele z blokovaných domény, zobrazí se zpráva, že doména uživatele je aktuálně blokována zásady vaší pozvánku.
 
### <a name="add-an-allow-list"></a>Přidat seznam povolených položek

Toto je víc omezující konfigurace, kde můžete nastavit konkrétní domény v seznamu povolených a omezit pozvánek k žádnému organizace nebo doménách, které nejsou uvedené. 

Pokud chcete použít seznam povolených, ujistěte se, že strávíte čas k plně vyhodnocení potřeb organizace jsou. Pokud provedete tuto zásadu příliš omezující, uživatelé rozhodnout dokumenty odeslání e-mailu nebo najít další jiný IT schválené způsoby, jak spolupráce.


Chcete-li přidat seznam povolených položek:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **Azure Active Directory** > **uživatelé** > **uživatelská nastavení**.
3. V části **externí uživatelé**, vyberte **spravovat nastavení externí spolupráce**.
4. V části **spolupráce omezení**, vyberte **povolit zasílání požadavků pouze zadaná (nejvíc omezující)**.
5. V části **cílové domény**, zadejte název jednoho z domén, které chcete povolit. Pro několik domén zadejte na nový řádek každou doménu. Příklad:

   ![Zobrazuje možnost povolit přidání domény](./media/allow-deny-list/AllowListSettings.png)
 
6. Když jste hotovi, klikněte na tlačítko **Uložit**.

Jakmile nastavíte zásady, pokud se pokusíte pozvat uživatele z domény, který se nenachází na seznamu povolených, zobrazí se zpráva, že doména uživatele je aktuálně blokována zásady vaší pozvánku.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Povolit přepínač z tak, aby odepřel seznamu a naopak 

Pokud přejdete z jedněch zásad do druhého, zahodí se existující konfiguraci zásad. Ujistěte se, že jste před provedením přepínač zálohovat podrobnosti o konfiguraci. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Nastavit povolit nebo odepřít zásady seznamu pomocí prostředí PowerShell

### <a name="prerequisite"></a>Požadavek

Nastavit povolit nebo odepřít seznamu pomocí prostředí PowerShell, je nutné nainstalovat verzi preview služby Azure Active Directory modul pro prostředí Windows PowerShell. Konkrétně nainstalovat AzureADPreview verze modulu 2.0.0.98 nebo novější.

Kontrola verze modulu (a zda je nainstalována):
 
1. Otevřete prostředí Windows PowerShell jako uživatel se zvýšenými oprávněními (Spustit jako správce). 
2. Spusťte následující příkaz, který zda jsou všechny verze Azure Active Directory modul pro prostředí Windows PowerShell v počítači nainstalován:

   ````powershell  
   Get-Module -ListAvailable AzureAD*
   ````

Pokud modul není nainstalován nebo nemáte požadovaná verze, proveďte jednu z následujících akcí:

- Pokud se žádné výsledky, spusťte následující příkaz, který nainstalujte nejnovější verzi modulu AzureADPreview:
  
   ````powershell  
   Install-Module AzureADPreview
   ````
- Pokud jenom modul AzureAD je zobrazený ve výsledcích, spusťte následující příkazy pro instalaci modulu AzureADPreview: 

   ````powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ````
- Pokud jenom modul AzureADPreview je zobrazený ve výsledcích, ale verze je menší než 2.0.0.98, spusťte následující příkazy jej aktualizovat: 

   ````powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ````

- Pokud AzureAD i AzureADPreview moduly jsou uvedeny ve výsledcích, ale verze modulu AzureADPreview je menší než 2.0.0.98, spusťte následující příkazy, které ji aktualizovat: 

   ````powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ````

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Rutiny AzureADPolicy použít ke konfiguraci zásad

Chcete-li vytvořit povolit nebo odepřít seznamu, použijte [New-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) rutiny. Následující příklad ukazuje, jak nastavit seznam zakázaných blokující domény "live.com".

````powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

Následující příklad zobrazuje příkladě, ale s vložený definice zásady.

````powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

Chcete-li nastavit povolit nebo odepřít zásady seznamu, použijte [Set-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) rutiny. Příklad:

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

- Přehled Azure AD B2B najdete v tématu [co je spolupráce Azure AD B2B?](what-is-b2b.md)
- Informace o podmíněného přístupu a spolupráci B2B najdete v tématu [podmíněného přístupu pro uživatele spolupráce B2B](conditional-access.md).



