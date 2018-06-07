---
title: Název nastavení zásad skupiny pro skupiny Office 365 ve službě Azure Active Directory (preview) | Microsoft Docs
description: Jak nastavit vypršení platnosti pro skupiny Office 365 ve službě Azure Active Directory (preview)
services: active-directory
documentationcenter: ''
author: curtand
manager: michael.tillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 05/21/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: fb4f43dd85f887fa051aaa8050ddf6b37313e376
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594546"
---
# <a name="enforce-a-naming-policy-for-office-365-groups-in-azure-active-directory-preview"></a>Vynutit zásady pojmenování pro skupiny Office 365 ve službě Azure Active Directory (preview)

Pokud chcete vynutit konzistentní zásady vytváření názvů pro skupiny Office 365 vytvořená nebo upravená vaši uživatelé, nastavení skupiny pojmenování zásad pro klienty v Azure Active Directory (Azure AD). Například můžete použít pravidla pro názvy komunikovat funkce skupiny, členství, zeměpisnou oblast, nebo který vytvoření skupiny. Můžete také použít pravidla pro názvy můžete uspořádat skupiny v adresáři. Zásady můžete blokovat určitá slova z používá v názvech skupin a aliasy.

> [!IMPORTANT]
> Použití ve verzi preview zásady pojmenování skupiny Office 365 vyžaduje Azure Active Directory Premium P1 licence nebo Azure AD Basic EDU licence pro každého uživatele jedinečné, který je členem více skupin Office 365.

Pojmenování zásady se použijí k vytvoření nebo úprava skupiny vytvořené napříč zatížení (například Outlook, Teams společnosti Microsoft, SharePoint, Exchange nebo Planner). Je použito k název skupiny a skupiny alias. Pokud nastavíte zásady vaší pojmenování ve službě Azure AD a máte existující skupinu Exchange pojmenování zásady, se použije názvy zásad Azure AD.

## <a name="naming-policy-features"></a>Názvy zásad funkce
Zásady pojmenování pro skupiny Office 365 můžete vynutit dvěma různými způsoby:

-   **Zásady pojmenování předponu příponu** můžete definovat předpony nebo přípony, které se pak přidají automaticky k vynucení zásad vytváření názvů u skupin (například v názvu skupiny "skupiny\_Japonsko\_mé skupině\_ Technikům", skupiny\_Japonsko\_ je předpona, a \_inženýrství je přípona). 

-   **Vlastní blokované slova** můžete nahrát sadu blokované slova, které jsou specifické pro vaši organizaci zablokuje v skupiny vytvořené uživateli (například "CEO mzdy, HR").

### <a name="prefix-suffix-naming-policy"></a>Zásady pojmenování předponu příponu

Obecnou strukturu zásad vytváření názvů je 'Příponu předponu [GroupName]'. Zatímco můžete definovat více předpony a přípony, může mít pouze jednu instanci [GroupName] v nastavení. Předpony nebo přípony může být buď pevné řetězce nebo uživatelské atributy, jako \[oddělení\] , jsou nahrazena podle uživatele, který je vytváření skupiny. Celkový počet povolený počet znaků pro vaši předponu a přípona řetězce kombinaci je 53 znaků. 

Předpony a přípony může obsahovat speciální znaky, které jsou podporovány v název skupiny a skupiny alias. Znaky v předponu nebo příponu, které nejsou podporované v aliasu skupiny jsou stále použité v názvu skupiny, ale odebrat ze skupiny alias. Z důvodu toto omezení může být liší od u skupiny alias předpony a přípony u názvu skupiny. 

#### <a name="fixed-strings"></a>Opravené řetězce

Aby bylo snazší kontrolovat a odlišují jej od skupiny v globálním adresáři a v levém navigační odkazy skupiny úloh můžete použít řetězce. Některé běžné předpony jsou klíčová slova jako ' skupiny\_název ', '\#název ','\_název.

#### <a name="user-attributes"></a>Atributy uživatele

Můžete použít atributy, které vám mohou pomoci a vaši uživatelé identifikovat, které oddělení, office nebo zeměpisnou oblast, pro které byla skupina vytvořena. Pokud definujete vaše pojmenování zásady, jako například `PrefixSuffixNamingRequirement = “GRP [GroupName] [Department]”`, a `User’s department = Engineering`, potom název vynucené skupiny může být "Moje skupina skupiny Technici." Podporované Azure AD atributy jsou \[oddělení\], \[společnosti\], \[Office\], \[StátNeboKraj\], \[CountryOrRegion \], \[Název\]. Nepodporované uživatelské atributy jsou považovány za pevné řetězce; například "\[postalCode\]". Atributy rozšíření a vlastní atributy nejsou podporovány.

Doporučujeme vám, že používáte atributy, které mají hodnoty vyplněno pro všechny uživatele ve vaší organizaci a nepoužívejte atributy, které mají dlouhé hodnoty.

### <a name="custom-blocked-words"></a>Vlastní blokované slova

Seznam blokovaných word je čárkami oddělený seznam frází zablokuje v názvech skupin a aliasy. Jsou prováděny žádné dílčí řetězec hledání. Přesnou shodu mezi název skupiny a jeden nebo více vlastních blokované slova je potřeba aktivovat selhání. Dílčí řetězec vyhledávání však není provedena tak, aby uživatelé mohou používat běžná slova jako 'Class', i když 'dy' je blokované aplikace word.

Pravidla seznamu blokovaných word:
- Blokované slova se nerozlišují malá a velká písmena.
- Když uživatel zadá blokované aplikace word v rámci názvu skupiny, zobrazí chybová zpráva s blokované aplikace word.
- Neexistují žádná omezení znak na blokovaných slova.
- Je horní limit 5000 frází, které lze konfigurovat v seznamu blokovaných slova. 

### <a name="administrator-override"></a>Správce přepsání

Vybrané skupině správců může vyloučené z těchto zásad ve všech skupin úloh a koncových bodů, tak, aby mohli vytvářet skupiny pomocí blokované slova a vlastní zásady vytváření názvů. Následuje seznam rolí správce, které jsou vyloučené z pojmenování zásad skupiny.

- Globální správce
- Podpora vrstvy 1 partnera
- Podpora vrstvy 2 partnera
- Správce účtu uživatele
- Uživatelé zapisující do adresáře

## <a name="install-powershell-cmdlets-to-configure-a-naming-policy"></a>Nainstalovat rutiny prostředí PowerShell ke konfiguraci pravidla pro názvy

Nezapomeňte odinstalujte všechny starší verze Azure Active Directory PowerShell pro modul grafu pro prostředí Windows PowerShell a nainstalujte [Azure Active Directory PowerShell pro grafu – veřejné verze Preview 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) před spuštěním Příkazy prostředí PowerShell. 

1. Otevřete aplikaci prostředí Windows PowerShell jako správce.
2. Odinstalujte všechny předchozí verze AzureADPreview.
  
  ````
  Uninstall-Module AzureADPreview
  ````
3. Nainstalujte nejnovější verzi AzureADPreview.
  
  ````
  Install-Module AzureADPreview
  ````
Pokud se zobrazí výzva týkající se přístupu k nedůvěryhodné úložiště, zadejte **Y**. Může trvat několik minut, než nový modul pro instalaci.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-ad-powershell"></a>Konfigurovat skupinu pro pojmenování zásad pro klienta pomocí Azure AD PowerShell

1. Otevřete okno prostředí Windows PowerShell ve vašem počítači. Můžete ji otevřít bez zvýšených oprávnění.

2. Spusťte následující příkazy k přípravě spuštění rutiny.
  
  ````
  Import-Module AzureADPreview
  Connect-AzureAD
  ````
  V **přihlásit ke svému účtu** obrazovky, které se otevře, zadejte účet správce a heslo, které jste připojení k službě a vyberte **přihlášení**.

3. Postupujte podle kroků v [rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](active-directory-accessmanagement-groups-settings-cmdlets.md) se vytvořit nastavení skupiny pro tohoto klienta.

### <a name="view-the-current-settings"></a>Zobrazit aktuální nastavení

1. Načtení aktuální pravidla pro názvy, chcete-li zobrazit aktuální nastavení.
  
  ````
  $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ````
  
2. Zobrazí aktuální nastavení skupiny.
  
  ````
  $Setting.Values
  ````
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Nastavit pravidla pro názvy a vlastní blokované slova

1. Nastavte skupinu název předpony a přípony v Azure AD PowerShell.
  
  ````
  $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
  ````
  
2. Nastavte vlastní blokované slova, která chcete omezit. Následující příklad ilustruje, jak můžete přidat vlastní vlastní slova.
  
  ````
  $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
  ````
  
3. Uložte nastavení pro novou zásadu účinná, například v následujícím příkladu.
  
  ````
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ````
  
A je to. Jste si pojmenování zásadu nastavit a přidat váš blokované slova.

## <a name="export-or-import-the-list-of-custom-blocked-words"></a>Exportujete nebo importujete seznam blokovaných vlastní slova

Další informace najdete v článku [rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](active-directory-accessmanagement-groups-settings-cmdlets.md).

Tady je příklad skriptu prostředí PowerShell pro export více blokované slova:

````
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
````

Tady je příklad skriptu prostředí PowerShell k importu více blokované slova:

````
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
$Settings["EnableMSStandardBlockedWords"] = $True
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
````

## <a name="naming-policy-experiences-across-office-365-apps"></a>Názvy zásad výsledky v různých aplikací Office 365

Po nastavení pojmenování zásady skupiny ve službě Azure AD, když uživatel vytvoří skupinu v aplikaci Office 365, uvidí: 

* Náhled A názvu souladu s vašimi zásadami pojmenování (s předpony a přípony) co nejdříve uživatel zadá název skupiny
* Pokud uživatel zadá blokované slova, chybová zpráva se zobrazí, tak odeberou blokované slova.

Úloha | Dodržování předpisů
----------- | -------------------------------
Portály pro Azure Active Directory | Portálu Azure AD a přístupového panelu portálu zobrazit pojmenování název vynucené zásady, když uživatel zadá název skupiny při vytváření nebo úpravách skupiny. Když uživatel zadá vlastní blokované aplikace word, se zobrazí chybová zpráva s blokované aplikace word tak, aby jej odebrat uživatele.
Outlook Web Access (OWA) | Aplikace Outlook Web Access zobrazuje pravidla pro názvy vynutí název, když uživatel zadá název skupiny nebo alias skupiny. Když uživatelé zadá vlastní blokované aplikace word, chybová zpráva se zobrazí v uživatelském rozhraní společně s blokované aplikace word tak, aby jej odebrat uživatele.
Desktopová verze aplikace Outlook | Jsou kompatibilní s pojmenování nastavení zásad skupiny vytvořené v desktopová verze aplikace Outlook. Desktopová aplikace Outlook ještě nezobrazí verze preview název vynucené skupiny a nevrací chyby vlastní blokované aplikace word, když uživatel zadá název skupiny. Ale pravidla pro názvy se automaticky použije při vytváření nebo úpravách skupiny a uživatelé zobrazit chybové zprávy, pokud jsou k dispozici vlastní blokované slova v názvu skupiny nebo alias.
Microsoft Teams | Microsoft Teams ukazuje skupině pojmenování název zásady vynucují, když uživatel zadá název týmu. Když uživatel zadá vlastní blokované aplikace word, chybová zpráva se zobrazí spolu s blokované aplikace word tak, aby uživatel jej odebrat.
SharePoint  |  SharePoint zobrazuje název pojmenování vynucené zásady, když název typy uživatelů a webu nebo skupinu e-mailovou adresu. Když uživatel zadá vlastní blokované slova, chybová zpráva se zobrazí, společně s blokované aplikace word tak, aby uživatel jej odebrat.
Microsoft Stream | Microsoft Stream ukazuje skupině pojmenování název zásady vynucují, když uživatel zadá název skupiny nebo skupiny e-mailový alias. Uživatel s zadá vlastní blokované aplikace word, chybová zpráva se zobrazí se blokované aplikace word, můžete ho odebrat uživatele.
IOS aplikace Outlook a aplikace pro Android | Jsou kompatibilní s nakonfigurované pojmenování zásad skupiny vytvořené v aplikace Outlook. Mobilní aplikace Outlook ještě nezobrazí ve verzi preview pojmenování názvu zásady vynucují a nevrací chyby vlastní blokované aplikace word, když uživatel zadá název skupiny. Ale pojmenování zásady se automaticky použijí po kliknutí na tlačítko vytvořit či upravit a uživatelé zobrazit chybové zprávy, pokud jsou k dispozici vlastní blokované slova v názvu skupiny nebo alias.
Skupiny mobilní aplikace | Zásady pojmenování vyhovují skupiny vytvořené v mobilní aplikaci skupiny. Skupiny mobilní aplikace nejsou zobrazeny ve verzi preview pojmenování zásad a nevrací chyby vlastní blokované aplikace word, když uživatel zadá název skupiny. Ale pravidla pro názvy se automaticky použije při vytváření nebo úpravách skupiny a uživatelé je nabídnuta příslušné chyby, pokud jsou k dispozici vlastní blokované slova v názvu skupiny nebo alias.
Planner | Plánovač dodržuje pravidla pro názvy. Plánovač zobrazuje pojmenování preview zásad, když zadáte název plánu. Když uživatel zadá vlastní blokované aplikace word, chybová zpráva se zobrazí při vytváření plánu.
Dynamics 365 pro zákaznické Engagement | Dynamics 365 pro zákaznické Engagement dodržuje pravidla pro názvy. Dynamics 365 zobrazuje název pojmenování vynucené zásady, když uživatel zadá název skupiny nebo skupiny e-mailový alias. Když uživatel zadá vlastní blokované aplikace word, chybová zpráva se zobrazí s blokované aplikace word, můžete ho odebrat uživatele.
Synchronizaci dat školní (SDS) | Skupiny vytvořené prostřednictvím SDS v souladu s názvy zásad, ale není automaticky použita pravidla pro názvy. Správci SDS nemusí připojí k názvy tříd, pro které skupiny musí být vytvořen a pak odesláno na SDS předpony a přípony. Vytvoření skupiny nebo úpravy by jinak selhala.
Správce zákazníka aplikace Outlook (OCM) | Správce zákazníka aplikace Outlook je kompatibilní s pojmenování zásady, které se automaticky použije na skupiny vytvořené v aplikaci Outlook zákazníka správce. Pokud je zjištěn vlastní blokované aplikace word, vytvořit skupinu v OCM je blokovaný a uživatel je blokováno v aplikaci OCM.
Učebna aplikace | Skupiny vytvořené v aplikaci učebny splňovat pravidla pro názvy, ale není automaticky použita pravidla pro názvy a pojmenování zásad preview není zobrazen uživatelům při zadávání názvu skupiny učebny. Uživatelé musí zadejte název skupiny vynucené učebny s předpony a přípony. Pokud ne, učebny skupiny vytvořte nebo upravte operace selže s chybami.
Power BI | Zásady pojmenování vyhovují pracovních prostorů Power BI.    
Yammer | Když uživatel přihlášení k Yammeru pomocí svého účtu Azure Active Directory vytvoří skupinu nebo upraví název skupiny, je název skupiny dodržovat pojmenování zásad. To platí jak pro Office 365 připojené skupiny a všech ostatních skupin Yammer.<br>Pokud připojené skupiny pro Office 365 byl vytvořen dříve, než pravidla pro názvy je na místě, nebude podle názvu skupiny automaticky pojmenování zásady. Pokud uživatel upravuje název skupiny, se zobrazí výzva k přidání předponu a příponu.
StaffHub  | Týmy StaffHub nepostupujte podle pravidla pro názvy, ale nemá podkladové skupiny Office 365. Název týmu StaffHub nevztahuje předpony a přípony a nekontroluje vlastní blokované slova. Ale StaffHub použít předpony a přípony a odebere blokované slova z podkladové skupiny Office 365.
Prostředí PowerShell systému Exchange | Zásady pojmenování vyhovují rutiny Exchange PowerShell. Uživatelé obdrží odpovídající chybové zprávy se navrhované předpony a přípony a pro vlastní blokované slova, pokud nemáte postupují pravidla pro názvy v název skupiny a skupiny alias (mailNickname).
Rutiny služby Azure Active Directory PowerShell. | Rutiny služby Azure Active Directory PowerShell jsou kompatibilní s názvy zásad. Uživatelé obdrží odpovídající chybové zprávy se navrhované předpony a přípony a pro vlastní blokované slova, pokud nemáte postupují zásady vytváření názvů v názvech skupin a alias skupiny.
Centrum pro správu systému Exchange | Centrum pro správu systému Exchange je kompatibilní s názvy zásad. Uživatelé obdrží odpovídající chybové zprávy se navrhované předpony a přípony a pro vlastní blokované slova, pokud nemáte postupují zásady vytváření názvů v název skupiny a skupiny alias.
Centrum pro správu Office 365 | Centrum pro správu Office 365 je kompatibilní s názvy zásad. Když uživatel vytvoří nebo názvy skupin úpravy, pravidla pro názvy se automaticky použije a uživatelům zobrazit příslušné chyby při jejich zadejte vlastní blokované slova. Do centra pro správu Office 365 ještě nezobrazí náhled pravidla pro názvy a nevrací chyby vlastní blokované aplikace word, když uživatel zadá název skupiny.

## <a name="next-steps"></a>Další postup
Tyto články poskytují další informace o skupinách Azure AD.

* [Zobrazení existujících skupin](active-directory-groups-view-azure-portal.md)
* [Zásady vypršení platnosti pro skupiny Office 365](active-directory-groups-lifecycle-azure-portal.md)
* [Spravovat nastavení skupiny](active-directory-groups-settings-azure-portal.md)
* [Správa členů skupiny](active-directory-groups-members-azure-portal.md)
* [Správa členství ve skupině](active-directory-groups-membership-azure-portal.md)
* [Dynamická pravidla pro uživatele ve skupině pro správu](active-directory-groups-dynamic-membership-azure-portal.md)
