---
title: Vynucení zásad pojmenování skupin ve službě Azure Active Directory | Dokumenty společnosti Microsoft
description: Jak nastavit zásady pojmenování pro skupiny Office 365 ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 141e83e21db18f21468113fd9927c2bdd2ed176d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497880"
---
# <a name="enforce-a-naming-policy-on-office-365-groups-in-azure-active-directory"></a>Vynucení zásad pojmenování u skupin Office 365 ve službě Azure Active Directory

Pokud chcete vynutit konzistentní konvence pojmenování pro skupiny Office 365 vytvořené nebo upravené vašimi uživateli, nastavte zásady pojmenování skupin pro své klienty ve službě Azure Active Directory (Azure AD). Zásady pojmenování můžete například použít ke sdělení funkce skupiny, členství, zeměpisné oblasti nebo toho, kdo skupinu vytvořil. Zásady pojmenování můžete také použít ke kategorizaci skupin v adresáři. Zásadu můžete použít k zablokování použití určitých slov v názvech skupin a aliasech.

> [!IMPORTANT]
> Použití zásad pojmenování služby Azure AD pro skupiny Office 365 vyžaduje, abyste měli licenci Azure Active Directory Premium P1 nebo licenci Azure AD Basic EDU pro každého jedinečného uživatele, který je členem jedné nebo více skupin Office 365.

Zásady pojmenování se používají pro vytváření nebo úpravy skupin vytvořených napříč úlohami (například Outlook, Microsoft Teams, SharePoint, Exchange nebo Planner). Použije se jak pro název skupiny, tak pro alias skupiny. Pokud nastavíte zásady pojmenování ve službě Azure AD a máte existující zásady pojmenování skupiny Exchange, zásady pojmenování Azure AD se vynucují ve vaší organizaci.

Když je nakonfigurovaná zásada pojmenování skupin, bude použita na nové skupiny Office 365 vytvořené koncovými uživateli. Zásady pojmenování se nevztahují na určité role adresáře, například globální správce nebo správce uživatelů (úplný seznam rolí vyňatých ze zásad pojmenování skupin naleznete níže. Pro existující skupiny Office 365 zásady nebudou okamžitě platit v době konfigurace. Jakmile vlastník skupiny uúpravy název skupiny pro tyto skupiny, zásady pojmenování budou vynuceny.

## <a name="naming-policy-features"></a>Pojmenování funkcí zásad

Zásady pojmenování skupin můžete vynutit dvěma různými způsoby:

- **Zásady pojmenování s předponou a příponou** Můžete definovat předpony nebo přípony, které jsou pak přidány automaticky vynutit konvence pojmenování na\_vaše\_skupiny (například v názvu skupiny "GRP JAPONSKO Moje\_skupina Engineering", GRP\_JAPONSKO\_ je předpona a \_inženýrství je přípona). 

- **Vlastní blokovaná slova** Můžete nahrát sadu blokovaných slov specifických pro vaši organizaci, která mají být blokována ve skupinách vytvořených uživateli (například "CEO, Payroll, HR").

### <a name="prefix-suffix-naming-policy"></a>Zásady pojmenování s předponou a příponou

Obecná struktura konvence pojmenování je "Prefix[GroupName]Přípona". I když můžete definovat více předpon a přípon, můžete mít v nastavení pouze jednu instanci [GroupName]. Předpony nebo přípony mohou být pevné řetězce nebo uživatelské \[atributy, jako je například oddělení,\] které jsou nahrazeny na základě uživatele, který vytváří skupinu. Celkový povolený počet znaků pro řetězce předpony a přípony v kombinaci je 53 znaků. 

Předpony a přípony mohou obsahovat speciální znaky, které jsou podporovány v názvu skupiny a aliasu skupiny. Všechny znaky v předponě nebo příponě, které nejsou podporovány ve skupinovém aliasu, budou stále použity v názvu skupiny, ale odebrány z aliasu skupiny. Z důvodu tohoto omezení se předpony a přípony použité na název skupiny mohou lišit od předpon použitých pro alias skupiny. 

#### <a name="fixed-strings"></a>Pevné řetězce

Pomocí řetězců můžete usnadnit skenování a rozlišení skupin v globálním seznamu adres a v levém navigačním propojení úloh skupiny. Některé běžné předpony jsou klíčová slova\_jako "Grp Name" , '\#Name', '\_Name'

#### <a name="user-attributes"></a>Uživatelské atributy

Můžete použít atributy, které vám a vašim uživatelům pomohou určit, které oddělení, kancelář nebo zeměpisnou oblast, pro které byla skupina vytvořena. Pokud například definujete zásady `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"`pojmenování jako , a `User’s department = Engineering`, může být vynucený název skupiny "GRP My Group Engineering". Podporované atributy Azure \[AD jsou \[\]oddělení \[\] \[, společnost\],\]úřad \[\], stateorprovince\], \[countryorregion , title . Nepodporované uživatelské atributy jsou považovány za pevné řetězce; například "\[PSČ\]". Atributy rozšíření a vlastní atributy nejsou podporovány.

Doporučujeme používat atributy, které mají hodnoty vyplněny pro všechny uživatele ve vaší organizaci a nepoužívejte atributy, které mají dlouhé hodnoty.

### <a name="custom-blocked-words"></a>Vlastní blokovaná slova

Blokovaný seznam slov je seznam frází oddělených čárkami, které mají být blokovány v názvech skupin a aliasech. Nejsou prováděna žádná hledání podřetězců. K aktivaci selhání je nutná přesná shoda mezi názvem skupiny a jedním nebo více vlastními blokovanými slovy. Vyhledávání podřetězců se neprovádí tak, aby uživatelé mohli používat běžná slova jako "Třída", i když je "lass" blokované slovo.

Pravidla seznamu blokovaných slov:

- Blokovaná slova nerozlišují malá a velká písmena.
- Když uživatel zadá blokované slovo jako součást názvu skupiny, zobrazí se mu chybová zpráva s blokovaným slovem.
- U blokovaných slov neexistují žádná omezení znaků.
- Existuje horní limit 5000 frází, které lze nakonfigurovat v seznamu blokovaných slov. 

### <a name="roles-and-permissions"></a>Role a oprávnění

Chcete-li nakonfigurovat zásady pojmenování, je vyžadována jedna z následujících rolí:
- Globální správce
- Správce skupiny
- Správce uživatele

Vybraní správci mohou být z těchto zásad vyňati ve všech úlohách skupiny a koncových bodech, aby mohli vytvářet skupiny pomocí blokovaných slov a s vlastními konvencemi pojmenování. Následuje seznam rolí správce vyňatých ze zásad pojmenování skupin.

- Globální správce
- Podpora partnerské úrovně 1
- Podpora partnerské úrovně 2
- Správce uživatele
- Adresář spisovatelů

## <a name="configure-naming-policy-in-azure-portal"></a>Konfigurace zásad pojmenování na webu Azure Portal

1. Přihlaste se k [Centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu správce skupiny.
1. Vyberte **Skupiny**, pak vyberte **Zásady pojmenování** a otevřete stránku Zásady pojmenování.

    ![Otevřete stránku Zásady pojmenování v Centru pro správu](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Zobrazení nebo úprava zásady pojmenování předpony

1. Na stránce **Zásady pojmenování** vyberte **zásady pojmenování skupin**.
1. Aktuální zásady pojmenování předpony nebo přípony můžete zobrazit nebo upravit jednotlivě výběrem atributů nebo řetězců, které chcete vynutit jako součást zásad pojmenování.
1. Pokud chcete odebrat předponu nebo příponu ze seznamu, vyberte předponu nebo příponu a pak vyberte **Odstranit**. Současně lze odstranit více položek.
1. Uložte změny, aby se nová zásada projevila, výběrem **možnosti Uložit**.

### <a name="edit-custom-blocked-words"></a>Úprava vlastních blokovaných slov

1. Na stránce **Zásady pojmenování** vyberte **Blokovaná slova**.

    ![upravit a odeslat seznam blokovaných slov pro zásady pojmenování](./media/groups-naming-policy/blockedwords.png)

1. Zobrazení nebo úprava aktuálního seznamu vlastních blokovaných slov výběrem **možnosti Stáhnout**.
1. Nahrajte nový seznam vlastních blokovaných slov výběrem ikony souboru.
1. Uložte změny, aby se nová zásada projevila, výběrem **možnosti Uložit**.

## <a name="install-powershell-cmdlets"></a>Instalace rutin PowerShellu

Před spouštěním příkazů PowerShellu nezapomeňte odinstalovat všechny starší verze modulu Azure Active Directory PowerShell pro Graph pro Windows PowerShell a nainstalovat [Azure Active Directory PowerShell pro Graph – Verze Public Preview 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

1. Otevřete aplikaci Windows PowerShell jako správce.
2. Odinstalujte všechny předchozí verze AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   ```

3. Nainstalujte nejnovější verzi AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```

   Pokud se zobrazí výzva k přístupu k nedůvěryhodnému úložišti, zadejte **y**. Instalace nového modulu může trvat několik minut.

## <a name="configure-naming-policy-in-powershell"></a>Konfigurace zásad pojmenování v PowerShellu

1. Otevřete v počítači okno prostředí Windows PowerShell. Můžete ji otevřít bez zvýšených oprávnění.

1. Spuštěním následujících příkazů se připravte na spouštění rutin.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   Na obrazovce **Přihlášení k účtu**, která se otevře, zadejte svůj účet a heslo správce pro připojení k vaší službě a vyberte **Přihlásit se**.

1. Podle pokynů v tématu [Rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](groups-settings-cmdlets.md) vytvořte nastavení skupiny pro tohoto tenanta.

### <a name="view-the-current-settings"></a>Zobrazení aktuálního nastavení

1. Načtením aktuální zásady pojmenování zobrazíte aktuální nastavení.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Zobrazte aktuální nastavení skupiny.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Nastavení zásad pojmenování a vlastních blokovaných slov

1. Nastavte předpony a přípony názvů skupin v Azure AD PowerShellu. Aby funkce fungovala správně, musí být do nastavení zahrnuta funkce [GroupName].
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Nastavte vlastní blokovaná slova, která chcete zakázat. Následující příklad ukazuje, jak můžete přidat vlastní slova.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Uložte nastavení pro nové zásady, které vstoupí v platnost, například v následujícím příkladu.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
A to je vše. Nastavili jste zásady pojmenování a přidali blokovaná slova.

## <a name="export-or-import-custom-blocked-words"></a>Export nebo import vlastních blokovaných slov

Další informace naleznete v článku [Rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](groups-settings-cmdlets.md).

Zde je příklad skriptu prostředí PowerShell pro export více blokovaných slov:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Zde je příklad skriptu prostředí PowerShell pro import více blokovaných slov:

``` PowerShell
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
```

## <a name="remove-the-naming-policy"></a>Odebrání zásad pojmenování

### <a name="remove-the-naming-policy-using-azure-portal"></a>Odebrání zásad pojmenování pomocí portálu Azure Portal

1. Na stránce **Zásady pojmenování** vyberte **Odstranit zásady**.
1. Po potvrzení odstranění je zásada pojmenování odebrána, včetně všech zásad pojmenování předpony a všech vlastních blokovaných slov.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Odebrání zásad pojmenování pomocí Prostředí Azure AD PowerShell

1. Vyprázdněte předpony a přípony názvu skupiny v prostředí Azure AD PowerShell.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
1. Vyprázdněte vlastní blokovaná slova.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=""
   ```
  
1. Uložte nastavení.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="experience-across-office-365-apps"></a>Zkušenosti s aplikacemi Office 365

Když uživatel vytvoří skupinu v aplikaci Office 365, když po nastavení zásad pojmenování skupiny ve službě Azure AD vytvoří skupinu, zobrazí se:

- Náhled názvu podle zásad pojmenování (s předponami a příponami), jakmile uživatel zadá název skupiny
- Pokud uživatel zadá blokovaná slova, zobrazí se mu chybová zpráva, aby mohl blokovaná slova odstranit.

Úloha | Dodržování předpisů
----------- | -------------------------------
Portály Azure Active Directory | Portál Azure AD a portál přístupového panelu zobrazují název vynucený zásady pojmenování, když uživatel zadá název skupiny při vytváření nebo úpravách skupiny. Když uživatel zadá vlastní blokované slovo, zobrazí se chybová zpráva s blokovaným slovem, aby ji uživatel mohl odebrat.
Outlook Web Access (OWA) | Aplikace Outlook Web Access zobrazuje název vynucený zásadou pojmenování, když uživatel zadá název skupiny nebo alias skupiny. Když uživatel zadá vlastní blokované slovo, zobrazí se v uživatelském rozhraní chybová zpráva spolu s blokovaným slovem, aby ho uživatel mohl odebrat.
Plocha Outlooku | Skupiny vytvořené v aplikaci Outlook Desktop jsou kompatibilní s nastavením zásad pojmenování. Aplikace Outlook pro klasickou pracovní plochu zatím nezobrazuje náhled vynuceného názvu skupiny a nevrací vlastní blokované chyby slov, když uživatel zadá název skupiny. Zásady pojmenování se však automaticky použijí při vytváření nebo úpravách skupiny a uživatelům se zobrazí chybové zprávy, pokud jsou v názvu nebo aliasu skupiny vlastní blokovaná slova.
Microsoft Teams | Microsoft Teams zobrazuje zásady pojmenování skupiny vynucený název, když uživatel zadá název týmu. Když uživatel zadá vlastní blokované slovo, zobrazí se spolu s blokovaným slovem chybová zpráva, aby ji uživatel mohl odebrat.
SharePoint  |  SharePoint zobrazuje název vynucený zásadou pojmenování, když uživatel zadá název webu nebo e-mailovou adresu skupiny. Když uživatel zadá vlastní blokované slovo, zobrazí se chybová zpráva spolu s blokovaným slovem, aby ho uživatel mohl odebrat.
Microsoft Stream | Microsoft Stream zobrazuje zásady pojmenování skupiny vynucený název, když uživatel zadá název skupiny nebo e-mailový alias skupiny. Když uživatel zadá vlastní blokované slovo, zobrazí se chybová zpráva s blokovaným slovem, aby ji uživatel mohl odebrat.
Aplikace Outlook pro iOS a Android | Skupiny vytvořené v aplikacích Outlooku jsou kompatibilní s nakonfigurovanými zásadami pojmenování. Mobilní aplikace Outlook zatím nezobrazuje náhled názvu vynuceného zásady pojmenování a při zadání názvu skupiny uživatelem nevrací vlastní blokované chyby slov. Zásady pojmenování jsou však automaticky použity při klepnutí na tlačítko vytvořit nebo upravit a uživatelům se zobrazí chybové zprávy, pokud jsou v názvu skupiny nebo aliasu vlastní blokovaná slova.
Skupiny mobilní aplikace | Skupiny vytvořené v mobilní aplikaci Skupiny jsou kompatibilní se zásadami pojmenování. Skupiny mobilní aplikace nezobrazuje náhled zásad pojmenování a nevrací vlastní blokované chyby slov, když uživatel zadá název skupiny. Zásady pojmenování jsou však automaticky použity při vytváření nebo úpravách skupiny a uživatelům jsou uvedeny příslušné chyby, pokud jsou v názvu nebo aliasu skupiny vlastní blokovaná slova.
Planner | Plánovač je kompatibilní se zásadami pojmenování. Plánovač zobrazuje náhled zásad pojmenování při zadávání názvu plánu. Když uživatel zadá vlastní blokované slovo, zobrazí se při vytváření plánu chybová zpráva.
Dynamics 365 for Customer Engagement | Dynamics 365 for Customer Engagement je kompatibilní se zásadami pojmenování. Dynamics 365 zobrazuje název vynucený zásadou pojmenování, když uživatel zadá název skupiny nebo e-mailový alias skupiny. Když uživatel zadá vlastní blokované slovo, zobrazí se chybová zpráva s blokovaným slovem, aby ji uživatel mohl odebrat.
Synchronizace školních dat (SDS) | Skupiny vytvořené prostřednictvím sds v souladu se zásadami pojmenování, ale zásady pojmenování není použita automaticky. Správci SDS musí připojit předpony a přípony k názvům tříd, pro které je třeba vytvořit skupiny a poté je odeslat do sds. Vytvoření nebo úprava skupiny by jinak selhaly.
Správce zákazníků aplikace Outlook (OCM) | Správce zákazníků aplikace Outlook je kompatibilní se zásadami pojmenování, které se automaticky použijí na skupinu vytvořenou ve Správci zákazníků aplikace Outlook. Pokud je zjištěna vlastní blokované slovo, vytváření skupin y v OCM je blokováno a uživatel i on je blokován používat aplikaci OCM.
Aplikace Pro učebny | Skupiny vytvořené v aplikaci Učebna splňují zásady pojmenování, ale zásady pojmenování se nepoužijí automaticky a náhled zásad pojmenování se uživatelům při zadávání názvu skupiny ve třídě nezobrazí. Uživatelé musí zadat vynucený název skupiny ve třídě s předponami a příponami. Pokud tomu tak není, skupina třídy vytvoří nebo upraví operaci s chybami.
Power BI | Pracovní prostory Power BI jsou kompatibilní se zásadami pojmenování.    
Yammer | Když uživatel přihlášený k Yammeru pomocí svého účtu Azure Active Directory vytvoří skupinu nebo uvede název skupiny, bude název skupiny v souladu se zásadami pojmenování. To platí jak pro připojené skupiny Office 365, tak pro všechny ostatní skupiny Yammeru.<br>Pokud byla připojená skupina Office 365 vytvořena před zavedením zásad pojmenování, název skupiny nebude automaticky dodržovat zásady pojmenování. Když uživatel upraví název skupiny, bude vyzván k přidání předpony a přípony.
StaffHub  | StaffHub týmy nedodržují zásady pojmenování, ale základní skupiny Office 365 dělá. Název týmu StaffHub nepoužije předpony a přípony a nekontroluje vlastní blokovaná slova. Ale StaffHub použije předpony a přípony a odstraní blokovaná slova ze základní skupiny Office 365.
Exchange PowerShell | Rutiny prostředí Exchange PowerShell jsou kompatibilní se zásadami pojmenování. Uživatelé obdrží příslušné chybové zprávy s navrhovanými předčíslími a příponami a pro vlastní blokovaná slova, pokud nedodržují zásady pojmenování v názvu skupiny a aliasu skupiny (mailNickname).
Rutiny prostředí PowerShell služby Azure Active Directory | Rutiny prostředí Azure Active Directory PowerShell jsou kompatibilní se zásadami pojmenování. Uživatelé obdrží příslušné chybové zprávy s navrhovanými předčíslími a příponami a pro vlastní blokovaná slova, pokud nedodržují konvence pojmenování v názvech skupin a aliasu skupiny.
Centrum pro správu Exchange | Centrum pro správu Exchange je kompatibilní se zásadami pojmenování. Uživatelé obdrží příslušné chybové zprávy s navrhovanými předčíslími a příponami a pro vlastní blokovaná slova, pokud nedodržují konvence pojmenování v názvu skupiny a aliasu skupiny.
Centrum pro správu Microsoftu 365 | Centrum pro správu Microsoftu 365 je kompatibilní se zásadami pojmenování. Když uživatel vytvoří nebo uvede názvy skupin, zásady pojmenování se automaticky použijí a uživatelé obdrží příslušné chyby při zadávání vlastních blokovaných slov. Centrum pro správu Microsoftu 365 zatím nezobrazuje náhled zásad pojmenování a při zadání názvu skupiny uživatelem nevrací vlastní blokované chyby slov.

## <a name="next-steps"></a>Další kroky

Tyto články poskytují další informace o skupinách Azure AD.

- [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Zásady vypršení platnosti pro skupiny Office 365](groups-lifecycle.md)
- [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Správa členů skupiny](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
