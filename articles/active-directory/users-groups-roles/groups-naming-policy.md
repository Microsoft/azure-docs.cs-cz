---
title: Název zásady (preview) – skupin Office 365, Azure Active Directory skupiny | Dokumentace Microsoftu
description: Nastavení zásad pojmenování pro skupiny Office 365 ve službě Azure Active Directory (preview)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/28/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-jan
ms.openlocfilehash: bf357a4f46f0aed26b8f06c524faffa9e7431de2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176552"
---
# <a name="enforce-a-naming-policy-for-office-365-groups-in-azure-active-directory-preview"></a>Vynucení zásad pojmenování pro skupiny Office 365 ve službě Azure Active Directory (preview)

K vynucení konzistentní zásady vytváření názvů pro skupiny Office 365 vytvořených nebo upravených uživatele, nastavte skupinu zásad pojmenování pro své tenanty ve službě Azure Active Directory (Azure AD). Například můžete použít zásady vytváření názvů pro komunikaci funkce skupiny, členství, geografické oblasti nebo kteří vytvořili skupinu. Můžete také použít zásady pojmenování můžete uspořádat skupiny v adresáři. Zásady můžete použít k blokování konkrétních slov z používán ve skupině názvy a aliasy.

> [!IMPORTANT]
> Použití náhledu zásady pojmenování skupin Office 365 vyžaduje licence Azure Active Directory Premium P1 nebo licence Azure AD Basic EDU pro každý jedinečný uživatel, který je členem jedné nebo víc skupin Office 365.

Pojmenování zásady se použijí k vytvoření nebo úpravu skupiny vytvořené napříč úlohami (například Outlook, Microsoft Teams, SharePoint, Exchange nebo plánovač). Použije se název skupiny a alias skupiny. Pokud nastavíte zásady vytváření názvů v Azure AD a máte existující skupinu Exchange pojmenování zásady, použije se pojmenování zásady služby Azure AD.

## <a name="naming-policy-features"></a>Pojmenování funkce zásad
Zásady pojmenování pro skupiny Office 365 můžete vynutit dvěma různými způsoby:

-   **Zásady pojmenování předponu příponu** můžete definovat předpon a přípon, které se poté přidají automaticky k vynucení zásad vytváření názvů u skupin (například v názvu skupiny "GRP\_Japonsko\_Moje skupina\_ Technikům", GRP\_Japonsko\_ je předpona a \_inženýrství je přípona). 

-   **Vlastní blokované slova** nahrajete sadu blokované slova specifické pro vaši organizaci se zablokuje ve skupinách vytvořených uživatelem (například "generální ředitel, výplatních pásek, HR").

### <a name="prefix-suffix-naming-policy"></a>Zásady pojmenování předponu příponu

Obecná struktura zásad vytváření názvů je "Předpona [GroupName] přípona". Přestože můžete definovat více předpony a přípony, může mít jednu instanci [GroupName] pouze v nastavení. Předpon a přípon může být oprava řetězců nebo uživatelských atributů, jako \[oddělení\] , které jsou nahrazeny podle uživatele, který vytváří skupiny. Celkový počet povolený počet znaků pro vaše předpony a přípony řetězce kombinované je 53 znaků. 

Předpon a přípon mohou obsahovat speciální znaky, které jsou podporovány v název skupiny a alias skupiny. Všechny znaky v prefix nebo sufix, které nejsou podporované v alias skupiny jsou stále použít v názvu skupiny, ale odebrána z alias skupiny. Kvůli tomuto omezení předpon a přípon u názvu skupiny může lišit od těch, které jsou použity na alias skupiny. 

#### <a name="fixed-strings"></a>Oprava řetězců

Aby bylo snazší kontrolovat a odlišují jej od skupiny v seznamu globálního adresáře a v levém navigační odkazy skupiny úloh můžete použít řetězce. Některé z běžných předpon klíčových slov, jako jsou "Grp\_název", "\#název","\_název.

#### <a name="user-attributes"></a>Atributy uživatele

Můžete použít atributy, které vám pomůžou a vaši uživatelé identifikovat, které oddělení, office nebo geografické oblasti, pro kterou byla skupina vytvořena. Například pokud definujete zásadami vytváření názvů jako `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"`, a `User’s department = Engineering`, názvu vynucené skupiny může být "Skupiny Moje skupiny inženýrství." Nepodporuje Azure AD jsou \[oddělení\], \[společnosti\], \[Office\], \[StátNeboKraj\], \[CountryOrRegion \], \[Title\]. Nepodporovaná uživatelské atributy jsou považovány za pevnou řetězce; například "\[PSČ\]". Atributy rozšíření a vlastní atributy nejsou podporovány.

Doporučujeme použít atributy, které se mají hodnoty pro všechny uživatele ve vaší organizaci a používat atributy, které mají dlouhé hodnoty.

### <a name="custom-blocked-words"></a>Zadejte vlastní slova blokované

Zablokované slovo seznamu je čárkou oddělený seznam slovní spojení, která se zablokuje ve skupině názvy a aliasy. Žádné hledání dílčího řetězce probíhají. Přesná shoda mezi název skupiny a jedním nebo více vlastních blokované slova se vyžaduje k aktivaci selhání. Hledání dílčího řetězce se neprovádí, takže uživatelé můžou používat běžná slova jako 'Class', i když je zablokované slovo 'třídy'.

Zablokované slovo seznam pravidel:

- Blokované slova se nerozlišují malá a velká písmena.
- Když uživatel zadá zablokované slovo jako součást názvu skupiny, se zobrazí chybová zpráva s zablokované slovo.
- Neexistují žádná omezení znaků na blokovaných slova.
- Je maximální limit je 5000 fráze, které se dá nakonfigurovat v seznamu blokovaných slova. 

### <a name="administrator-override"></a>Správce přepsání

Vybraný správce můžete vyloučené z těchto zásad ve všech skupin úloh a koncových bodů, tak, aby se můžou vytvářet skupiny pomocí blokované slova a s jejich vlastní zásady vytváření názvů. Následuje seznam rolí správce vyloučené ze skupiny zásad pojmenování.

- Globální správce
- Podpora partnerů úrovně 1
- Podpora partnerů úrovně 2
- Správce uživatelských účtů
- Uživatelé zapisující do adresáře

## <a name="install-powershell-cmdlets-to-configure-a-naming-policy"></a>Instalace rutin prostředí PowerShell ke konfiguraci zásad pojmenování

Před spouštěním příkazů PowerShellu nezapomeňte odinstalovat všechny starší verze modulu Azure Active Directory PowerShell pro Graph pro Windows PowerShell a nainstalovat [Azure Active Directory PowerShell pro Graph – Verze Public Preview 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137). 

1. Otevřete aplikaci Windows PowerShell jako správce.
2. Odinstalujte všechny předchozí verze AzureADPreview.
  
  ```
  Uninstall-Module AzureADPreview
  ```
3. Nainstalujte nejnovější verzi AzureADPreview.
  
  ```
  Install-Module AzureADPreview
  ```
Pokud se zobrazí výzva k potvrzení přístupu k nedůvěryhodnému úložišti, zadejte **Y**. Instalace nového modulu může trvat několik minut.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-ad-powershell"></a>Konfigurace skupiny zásad pojmenování pro tenanta pomocí Azure AD Powershellu

1. Otevřete okno Windows Powershellu ve vašem počítači. Můžete ji otevřít bez zvýšených oprávnění.

2. Spuštěním následujících příkazů se připravte na spouštění rutin.
  
  ```
  Import-Module AzureADPreview
  Connect-AzureAD
  ```
  Na obrazovce **Přihlášení k účtu**, která se otevře, zadejte svůj účet a heslo správce pro připojení k vaší službě a vyberte **Přihlásit se**.

3. Podle pokynů v tématu [Rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](groups-settings-cmdlets.md) vytvořte nastavení skupiny pro tohoto tenanta.

### <a name="view-the-current-settings"></a>Zobrazit aktuální nastavení

1. Načte aktuální zásady pojmenování, chcete-li zobrazit aktuální nastavení.
  
  ```
  $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ```
  
2. Zobrazte aktuální nastavení skupiny.
  
  ```
  $Setting.Values
  ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Nastavte zásady pojmenování a zadejte vlastní slova blokované

1. Nastavte předpony a přípony názvů skupin v Azure AD PowerShellu. Pro funkci tak, aby fungovala správně [GroupName] musí být součástí nastavení.
  
  ```
  $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
  ```
  
2. Nastavte vlastní blokovaná slova, která chcete zakázat. Následující příklad ukazuje, jak můžete přidat vlastní slova.
  
  ```
  $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
  ```
  
3. Uložte nastavení, aby nová zásada vstoupila v platnost, jak je znázorněno v následujícím příkladu.
  
  ```
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ```
  
A to je vše. Jste nastavit pojmenování zásady a přidá blokované slova.

## <a name="export-or-import-the-list-of-custom-blocked-words"></a>Exportovat nebo importovat seznam blokovaných zadejte vlastní slova

Další informace najdete v článku [rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](groups-settings-cmdlets.md).

Tady je příklad skriptu prostředí PowerShell pro export více blokované slov:

```
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Tady je příklad skriptu PowerShell k importu více blokované slov:

```
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
```

## <a name="remove-the-naming-policy"></a>Odebrat zásady pojmenování

1. Prázdná skupina název předpon a přípon v Azure AD PowerShell.
  
  ```
  $Setting["PrefixSuffixNamingRequirement"] =""
  ```
  
2. Prázdný vlastní blokované slova. 
  
  ```
  $Setting["CustomBlockedWordsList"]=""
  ```
  
3. Uložte nastavení.
  
  ```
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ```


## <a name="naming-policy-experiences-across-office-365-apps"></a>Pojmenování zásady prostředí v aplikacích Office 365

Jakmile nastavíte zásady pojmenování skupin ve službě Azure AD, když uživatel vytvoří skupinu v aplikaci Office 365, zobrazí se jim: 

* Náhled A názvu souladu s vašimi zásadami vytváření názvů (u předpony a přípony) jakmile uživatel zadá název skupiny
* Pokud uživatel zadá blokované slova, zobrazí proto jsou blokované slova odebrat chybovou zprávu.

Úloha | Dodržování předpisů
----------- | -------------------------------
Azure Portal služby Active Directory | Na portálu Azure AD a na přístupovém panelu portálu zobrazí název názvového vynucená zásada když uživatel zadá název skupiny při vytváření nebo úpravách skupiny. Když uživatel zadá vlastní zablokované slovo, zobrazí se chybová zpráva s zablokované slovo tak, aby uživatel jej může odebrat.
Outlook Web Access (OWA) | Aplikace Outlook Web Access zobrazují pojmenování zásady vynucují název, když uživatel zadá název skupiny nebo alias skupiny. Když uživatel zadá vlastní zablokované slovo, chybová zpráva se zobrazí v uživatelském rozhraní spolu s zablokované slovo tak, aby uživatel může odstranit ji.
Desktopová verze aplikace Outlook | Jsou kompatibilní s pojmenování nastavení zásad skupiny vytvořené v desktopová verze aplikace Outlook. Desktopová aplikace Outlook nezobrazí zatím ve verzi preview je název skupiny vynucené a nevrací chyby vlastní zablokované slovo, když uživatel zadá název skupiny. Ale zásady pojmenování je automaticky použijí, když vytváříte nebo upravujete skupinu a uživatelé zobrazit chybové zprávy, když jsou k dispozici vlastní blokované slova v názvu skupiny nebo alias.
Microsoft Teams | Microsoft Teams ukazuje názvy název zásady vynucovat, když uživatel zadá název týmu skupin. Když uživatel zadá vlastní zablokované slovo, chybová zpráva se zobrazí spolu s zablokované slovo tak, aby uživatel může odstranit ji.
SharePoint  |  SharePoint zobrazí název pojmenování zásady vynucují při uživatelské typy a lokality jméno nebo e-mailovou adresu skupiny. Když uživatel zadá vlastní zablokované slovo, chybová zpráva se zobrazí, spolu s zablokované slovo tak, aby uživatel může odstranit ji.
Microsoft Stream | Microsoft Stream ukazuje názvy název zásady vynucovat, když uživatel zadá název skupiny nebo skupiny e-mailový alias skupin. Když uživatel zadá vlastní zablokované slovo, chybová zpráva se zobrazí s zablokované slovo, můžete ho odebrat uživatele.
Zařízení s iOS aplikace Outlook a aplikace pro Android | Skupiny vytvořené v aplikace Outlook jsou kompatibilní s nakonfigurovanou zásadou vytváření názvů. Mobilní aplikace Outlook nezobrazí zatím ve verzi preview název názvového vynucená zásada a nevrací vlastní zablokované slovo chyby, když uživatel zadá název skupiny. Ale zásady pojmenování se automaticky využije na kliknutí na vytvořit či upravit a uživatelé zobrazit chybové zprávy, když jsou k dispozici vlastní blokované slova v názvu skupiny nebo alias.
Skupiny mobilní aplikace | Skupiny vytvořené v mobilní aplikaci skupiny jsou kompatibilní se zásadami vytváření názvů. Skupiny mobilní aplikace není uveden ve verzi preview zásady pojmenování a nevrací vlastní zablokované slovo chyby, když uživatel zadá název skupiny. Ale zásady pojmenování je automaticky použijí, když vytváříte nebo upravujete skupinu a uživateli se zobrazí s příslušné chyby, pokud jsou k dispozici vlastní blokované slova v názvu skupiny nebo alias.
Planner | Plánovač vyhovuje zásadám vytváření názvů. Planneru zobrazuje pojmenování zásady, ve verzi preview, když zadáte název plánu. Když uživatel zadá vlastní zablokované slovo, je při vytváření plánu zobrazí chybová zpráva.
Dynamics 365 for Customer Engagement | Dynamics 365 for Customer Engagement je v souladu se zásadami vytváření názvů. Dynamics 365 zobrazuje název názvového vynucená zásada, když uživatel zadá název skupiny nebo skupiny e-mailový alias. Když uživatel zadá vlastní zablokované slovo, chybová zpráva se zobrazí s zablokované slovo, můžete ho odebrat uživatele.
School Data Sync (SDS) | Skupiny vytvořené pomocí SDS v souladu s pojmenování zásady, ale zásady pojmenování se nepoužije automaticky. Správci SDS muset připojí k názvy tříd, u kterých skupin je potřeba vytvořit a pak nahrají do SDS předpon a přípon. Vytvoření skupiny nebo úpravy jinak selže.
Outlook Customer Managerem (OCM) | Outlook Customer Managerem je kompatibilní s pojmenování zásady, které se automaticky využije na skupiny vytvořené v aplikaci Outlook Customer Managerem. Pokud se zjistí vlastní zablokované slovo, vytvoření skupiny v OCM blokovaný a uživatel je blokován pomocí OCM aplikace.
Aplikace Classroom | Skupiny vytvořené v aplikaci Classroom v souladu se zásadami vytváření názvů, ale zásady pojmenování se použijí automaticky a pojmenování zásady, ve verzi preview není zobrazen uživatelům při zadávání názvu skupiny učebnu. Uživatelé musíte zadat název skupiny vynucené učebny s předpon a přípon. Pokud ne, classroom skupiny vytvořte nebo upravte operace se nezdaří s chybami.
Power BI | Pracovní prostory Power BI jsou kompatibilní se zásadami vytváření názvů.    
Yammer | Když uživatel přihlášení na Yammer se svým účtem Azure Active Directory vytvoří skupinu nebo upraví název skupiny, název skupiny dodržovat zásady pojmenování. To platí i pro skupiny Office 365 připojené a všechny ostatní skupiny Yammeru.<br>Pokud připojené skupiny Office 365 byl vytvořen před zásady pojmenování je na místě, nebude podle názvu skupiny automaticky zásady vytváření názvů. Když uživatel upraví název skupiny, jsou vyzváni k přidání předpon a přípon.
StaffHub  | Týmy Staffhubu nepostupujte podle zásady pojmenování, ale základní skupina Office 365. Název týmu Staffhubu doporučení se netýká předpon a přípon a nekontroluje blokované zadejte vlastní slova. Ale Staffhubu použít předpon a přípon a odebere blokované slova ze základní skupiny Office 365.
Exchange PowerShell | Rutiny Powershellu v Exchangi vyhovují zásadám vytváření názvů. Uživatelé dostanou odpovídající chybové zprávy se navrhované předpony a přípony a pro blokované zadejte vlastní slova, pokud není řídí zásady pojmenování v název skupiny a alias skupiny (mailNickname).
Rutiny Azure Powershellu pro Active Directory | Rutiny Azure Powershellu pro Active Directory jsou kompatibilní s zásad pojmenování. Uživatelé dostanou odpovídající chybové zprávy se navrhované předpony a přípony a pro blokované zadejte vlastní slova, pokud není řídí zásady vytváření názvů v názvech skupin a alias skupiny.
Centra pro správu Exchange | Centra pro správu Exchange je kompatibilní s zásad pojmenování. Uživatelé dostanou odpovídající chybové zprávy se navrhované předpony a přípony a pro blokované zadejte vlastní slova, pokud není řídí zásady vytváření názvů v název skupiny a alias skupiny.
Centrum pro správu Office 365 | Centrum pro správu Office 365 je kompatibilní s zásad pojmenování. Když uživatel vytvoří nebo názvy skupin úpravy, bude automaticky použito zásad vytváření názvů a uživatelé obdrží příslušné chyby při zadávání blokované zadejte vlastní slova. Centrum pro správu Office 365 ještě nezobrazí ve verzi preview zásady pojmenování a nevrací vlastní zablokované slovo chyby, když uživatel zadá název skupiny.

## <a name="next-steps"></a>Další postup
Tyto články poskytují další informace o skupinách Azure AD.

* [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Zásady vypršení platnosti pro skupiny Office 365](groups-lifecycle.md)
* [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Správa členů skupiny](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
