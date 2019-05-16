---
title: Vynutit zásady pojmenování skupin ze skupin Office 365 – Azure Active Directory | Dokumentace Microsoftu
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
ms.date: 05/06/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d21616938978e501cc112fde105be4db4499b2a
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65605548"
---
# <a name="enforce-a-naming-policy-on-office-365-groups-in-azure-active-directory"></a>Vynucení zásad pojmenování pro skupiny Office 365 ve službě Azure Active Directory

K vynucení konzistentní zásady vytváření názvů pro skupiny Office 365 vytvořených nebo upravených uživatele, nastavte skupinu zásad pojmenování pro své tenanty ve službě Azure Active Directory (Azure AD). Například můžete použít zásady vytváření názvů pro komunikaci funkce skupiny, členství, geografické oblasti nebo kteří vytvořili skupinu. Můžete také použít zásady pojmenování můžete uspořádat skupiny v adresáři. Zásady můžete použít k blokování konkrétních slov z používán ve skupině názvy a aliasy.

> [!IMPORTANT]
> Použití zásad vytváření názvů Azure AD pro skupiny Office 365 vyžaduje mít ale nemusí nutně jít přiřadit Azure Active Directory Premium P1 licence nebo licence Azure AD Basic EDU pro každý jedinečný uživatel, který je členem jedné nebo víc skupin Office 365.

Pojmenování zásady se použijí k vytvoření nebo úpravu skupiny vytvořené napříč úlohami (například Outlook, Microsoft Teams, SharePoint, Exchange nebo plánovač). Použije se název skupiny a alias skupiny. Pokud nastavíte zásady vytváření názvů v Azure AD a máte existující skupinu Exchange zásad pojmenování, pojmenování zásady služby Azure AD je vynutit ve vaší organizaci.

## <a name="naming-policy-features"></a>Pojmenování funkce zásad

Můžete vynutit zásady pojmenování skupin dvěma různými způsoby:

- **Zásady pojmenování předponu příponu** můžete definovat předpon a přípon, které se poté přidají automaticky k vynucení zásad vytváření názvů u skupin (například v názvu skupiny "GRP\_Japonsko\_Moje skupina\_ Technikům", GRP\_Japonsko\_ je předpona a \_inženýrství je přípona). 

- **Vlastní blokované slova** nahrajete sadu blokované slova specifické pro vaši organizaci se zablokuje ve skupinách vytvořených uživatelem (například "generální ředitel, výplatních pásek, HR").

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
- Správce uživatelů
- Uživatelé s oprávněním k zápisu do adresářů

## <a name="configure-naming-policy-in-azure-portal-preview"></a>Konfigurace zásad vytváření názvů na webu Azure portal (preview)

1. Přihlaste se k [centrum pro správu Azure AD](https://aad.portal.azure.com) pomocí uživatelského účtu správce.
1. Vyberte **skupiny**a pak vyberte **zásad pojmenování** otevřete stránku zásad pojmenování.

    ![Otevřete stránku zásad pojmenování v Centru pro správu](./media/groups-naming-policy/policy-preview.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Umožňuje zobrazit nebo upravit zásady vytváření názvů předponu příponu

1. Na **zásad pojmenování** stránce **pojmenování zásady skupiny**.
1. Můžete zobrazit nebo upravit aktuální předpona nebo přípona jednotlivě pojmenování zásady tak, že vyberete atributy nebo řetězce, které chcete vynutit jako součást zásady pojmenování.
1. Prefix nebo sufix odebrat ze seznamu, vyberte, jakou předponu nebo příponu a potom vyberte **odstranit**. Více položek lze odstranit ve stejnou dobu.
1. Uložte provedené změny pro nové zásady k cenám tak, že vyberete **Uložit**.

### <a name="edit-custom-blocked-words"></a>Upravit vlastní blokované slova

1. Na **zásad pojmenování** stránce **blokované slova**.

    ![Upravit a uložit seznam blokovaných slova pro pojmenování zásady](./media/groups-naming-policy/blockedwords-preview.png)

1. Umožňuje zobrazit nebo upravit tak, že vyberete aktuální seznam blokovaných zadejte vlastní slova **Stáhnout**.
1. Nahrajte nový seznam blokovaných zadejte vlastní slova tak, že vyberete ikonu souboru.
1. Uložte provedené změny pro nové zásady k cenám tak, že vyberete **Uložit**.

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

   Pokud se zobrazí výzva o přístupu k nedůvěryhodné úložiště, zadejte **Y**. Instalace nového modulu může trvat několik minut.

## <a name="configure-naming-policy-in-powershell"></a>Konfigurace zásady pojmenování v prostředí PowerShell

1. Otevřete okno Windows Powershellu ve vašem počítači. Můžete ji otevřít bez zvýšených oprávnění.

1. Spuštěním následujících příkazů se připravte na spouštění rutin.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   Na obrazovce **Přihlášení k účtu**, která se otevře, zadejte svůj účet a heslo správce pro připojení k vaší službě a vyberte **Přihlásit se**.

1. Podle pokynů v tématu [Rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](groups-settings-cmdlets.md) vytvořte nastavení skupiny pro tohoto tenanta.

### <a name="view-the-current-settings"></a>Zobrazit aktuální nastavení

1. Načte aktuální zásady pojmenování, chcete-li zobrazit aktuální nastavení.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Zobrazte aktuální nastavení skupiny.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Nastavte zásady pojmenování a zadejte vlastní slova blokované

1. Nastavte předpony a přípony názvů skupin v Azure AD PowerShellu. Pro funkci tak, aby fungovala správně [GroupName] musí být součástí nastavení.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Nastavte vlastní blokovaná slova, která chcete zakázat. Následující příklad ukazuje, jak můžete přidat vlastní slova.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Uložte nastavení pro novou zásadu cenám, jako v následujícím příkladu.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
A to je vše. Jste nastavit pojmenování zásady a přidá blokované slova.

## <a name="export-or-import-custom-blocked-words"></a>Export nebo import vlastních blokované slova

Další informace najdete v článku [rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](groups-settings-cmdlets.md).

Tady je příklad skriptu prostředí PowerShell pro export více blokované slov:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Tady je příklad skriptu PowerShell k importu více blokované slov:

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

## <a name="remove-the-naming-policy"></a>Odebrat zásady pojmenování

### <a name="remove-the-naming-policy-using-azure-portal-preview"></a>Odebrat zásady pojmenování pomocí webu Azure portal (preview)

1. Na **zásad pojmenování** stránce **odstranit zásadu**.
1. Jakmile potvrdíte odstranění, zásady pojmenování Odebereme, včetně všech předpon přípon pojmenování zásady a jakékoli vlastní blokované slova.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Odebrat zásady pojmenování pomocí Azure AD Powershellu

1. Prázdná skupina název předpon a přípon v Azure AD PowerShell.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
1. Prázdný vlastní blokované slova.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=""
   ```
  
1. Uložte nastavení.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="experience-across-office-365-apps"></a>Prostředí v aplikacích Office 365

Jakmile nastavíte zásady pojmenování skupin ve službě Azure AD, když uživatel vytvoří skupinu v aplikaci Office 365, zobrazí se jim:

- Náhled A názvu souladu s vašimi zásadami vytváření názvů (u předpony a přípony) jakmile uživatel zadá název skupiny
- Pokud uživatel zadá blokované slova, zobrazí proto jsou blokované slova odebrat chybovou zprávu.

Úloha | Kompatibilita
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
Centrum pro správu služeb Microsoft 365 | Centrum pro správu služeb Microsoft 365 je kompatibilní s zásad pojmenování. Když uživatel vytvoří nebo názvy skupin úpravy, bude automaticky použito zásad vytváření názvů a uživatelé obdrží příslušné chyby při zadávání blokované zadejte vlastní slova. Centrum pro správu služeb Microsoft 365 nezobrazí zatím ve verzi preview zásady pojmenování a nevrací vlastní zablokované slovo chyby, když uživatel zadá název skupiny.

## <a name="next-steps"></a>Další postup

Tyto články poskytují další informace o skupinách Azure AD.

- [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Zásady vypršení platnosti pro skupiny Office 365](groups-lifecycle.md)
- [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Správa členů skupiny](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
