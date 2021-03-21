---
title: Vynutilit zásady pojmenování skupin v Azure Active Directory | Microsoft Docs
description: Nastavení zásad pro pojmenovávání skupin Microsoft 365 v Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00291907ddea75ba4507779d1226502dc8343aea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100365697"
---
# <a name="enforce-a-naming-policy-on-microsoft-365-groups-in-azure-active-directory"></a>Vynutili zásady pojmenování u skupin Microsoft 365 v Azure Active Directory

Pokud chcete vyhodnotit konzistentní konvence pojmenování pro Microsoft 365 skupiny vytvořené nebo upravované uživateli, nastavte zásady pro pojmenování skupin pro vaše organizace v Azure Active Directory (Azure AD). Zásady pojmenování můžete použít například ke komunikaci funkce skupiny, členství, geografické oblasti nebo osoby, která skupinu vytvořila. Můžete také použít zásady pojmenování, které vám pomohou kategorizovat skupiny v adresáři. Pomocí zásad můžete blokovat použití určitých slov v názvech skupin a aliasech.

> [!IMPORTANT]
> Použití zásad pro pojmenování Azure AD pro skupiny Microsoft 365 vyžaduje, abyste pro každého jedinečného uživatele, který je členem jedné nebo více skupin Microsoft 365, ale nemuseli přiřadit licenci Azure Active Directory Premium P1 nebo Azure AD Basic licence EDU.

Zásady pojmenování se aplikují na vytváření nebo úpravy skupin vytvořených napříč úlohami (například Outlook, Microsoft teams, SharePoint, Exchange nebo Planner). Aplikuje se na název skupiny i alias skupiny. Pokud nastavíte zásady pro pojmenování ve službě Azure AD a máte stávající zásady pojmenování skupin Exchange, ve vaší organizaci se vynutila zásada pro pojmenování Azure AD.

Při konfiguraci zásady pojmenování skupin se zásada použije na nové skupiny Microsoft 365 vytvořené koncovými uživateli. Zásady pojmenování se nevztahují na určité role adresáře, jako je například globální správce nebo Správce uživatelů (níže najdete úplný seznam rolí vyjmutých ze zásad pro pojmenování skupin). U existujících skupin Microsoft 365 se zásada v době konfigurace okamžitě nepoužije. Když vlastník skupiny upraví název skupiny pro tyto skupiny, vynutily se zásady pojmenování.

## <a name="naming-policy-features"></a>Funkce zásad pojmenovávání

Zásady pro pojmenování skupin můžete vynutili dvěma různými způsoby:

- **Zásady pojmenování přípon předpon** Můžete definovat předpony nebo přípony, které se pak automaticky přidají, aby se vynutila konvence pojmenování pro vaše skupiny (například v názvu skupiny "GRP \_ Japonsko The \_ Group \_ Engineering", GRP \_ Japonsko \_ je předpona a \_ inženýr je přípona). 

- **Vlastní blokovaná slova** Můžete nahrát sadu blokovaných slov, která jsou specifická pro vaši organizaci, aby byla blokovaná ve skupinách vytvořených uživateli (například generální ředitel, mzdy, HR).

### <a name="prefix-suffix-naming-policy"></a>Zásady pojmenování přípon předpon

Obecnou strukturou konvence pojmenování je prefix [název_skupiny] přípona. I když můžete definovat více předpon a přípon, v nastavení může být pouze jedna instance [název_skupiny]. Předpony nebo přípony mohou být buď pevné řetězce, nebo atributy uživatele \[ , například oddělení \] , které je nahrazeno v závislosti na uživateli, který vytváří skupinu. Celkový povolený počet znaků pro předponu a řetězce přípony, včetně názvu skupiny, je 53 znaků. 

Předpony a přípony mohou obsahovat speciální znaky, které jsou podporovány v názvu skupiny a aliasu skupiny. Všechny znaky v předponě nebo příponě, které nejsou podporované v aliasu skupiny, se pořád aplikují v názvu skupiny, ale odeberou se z aliasu skupiny. Z důvodu tohoto omezení se můžou předpony a přípony použité na název skupiny lišit od těch, které se použijí pro alias skupiny. 

#### <a name="fixed-strings"></a>Pevné řetězce

Pomocí řetězců můžete snáze kontrolovat a rozlišovat skupiny v globálním seznamu adres a v levém navigačním propojení úloh skupin. Některými běžnými předponami jsou klíčová slova, jako je \_ název GRP, \# název a \_ název.

#### <a name="user-attributes"></a>Atributy uživatele

Můžete použít atributy, které vám a vašim uživatelům pomůžou zjistit, které oddělení, kancelář nebo geografické oblasti, pro které se skupina vytvořila. Pokud například definujete zásady pojmenování jako `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"` a `User’s department = Engineering` , pak název vynutilé skupiny může být "GRP my Group Engineering". Podporovány jsou atributy služby Azure \[ AD \] : oddělení, \[ Společnost \] , \[ kancelář \] , \[ StateOrProvince \] , \[ CountryorRegion \] , \[ název \] . Nepodporované atributy uživatele jsou považovány za pevné řetězce; například " \[ PSČ \] ". Atributy rozšíření a vlastní atributy nejsou podporovány.

Doporučujeme použít atributy, které mají hodnoty vyplněné pro všechny uživatele ve vaší organizaci, a nepoužívejte atributy, které mají dlouhé hodnoty.

### <a name="custom-blocked-words"></a>Vlastní blokovaná slova

Seznam blokovaných slov je čárkami oddělený seznam frází, které je třeba zablokovat v názvech skupin a aliasech. Neprovádí se žádná hledání dílčích řetězců. K aktivaci chyby se vyžaduje přesná shoda mezi názvem skupiny a jedním nebo více blokovanými slovy. Hledání dílčích řetězců se neprovádí, takže uživatelé můžou použít běžná slova, jako je "Class", a to i v případě, že ' třídy ' je blokované slovo.

Blokovaná pravidla pro seznam slov:

- Blokovaná slova nerozlišují velká a malá písmena.
- Když uživatel zadá blokované slovo jako součást názvu skupiny, zobrazí se mu chybová zpráva s blokovaným slovem.
- Neexistují žádná omezení znaků u blokovaných slov.
- V seznamu blokovaných slov se dá nakonfigurovat horní limit 5000 frází. 

### <a name="roles-and-permissions"></a>Role a oprávnění

Pokud chcete nakonfigurovat zásady pojmenování, je potřeba jedna z následujících rolí:
- Globální správce
- Správce skupiny
- Zapisovač adresářů


Vybraní Správci mohou být z těchto zásad vyloučeni napříč všemi úlohami skupin a koncovými body, aby mohli vytvářet skupiny pomocí blokovaných slov a jejich vlastních zásad vytváření názvů. Níže jsou uvedeny role správců, které jsou vyňaté ze zásady pojmenování skupin.

- Globální správce
- Správce uživatelů

## <a name="configure-naming-policy-in-azure-portal"></a>Konfigurace zásady pojmenování v Azure Portal

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu správce skupiny.
1. Vyberte **skupiny** a pak výběrem **zásady pojmenování** otevřete stránku zásady pojmenování.

    ![Otevřete stránku zásady pojmenování v centru pro správu.](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Zobrazit nebo upravit zásady pojmenování přípon předpon

1. Na stránce **zásady pojmenování** vyberte **zásady pojmenování skupin**.
1. Aktuální předponu nebo zásady pojmenování přípon můžete zobrazit nebo upravit jednotlivě tak, že vyberete atributy nebo řetězce, které chcete vykonat jako součást zásad pojmenování.
1. Pokud chcete ze seznamu odebrat předponu nebo příponu, vyberte předponu nebo příponu a pak vyberte **Odstranit**. Současně lze odstranit více položek.
1. Uložte změny pro nové zásady, aby se projevily, a to tak, že vyberete **Uložit**.

### <a name="edit-custom-blocked-words"></a>Upravit vlastní blokovaná slova

1. Na stránce **zásady pojmenování** vyberte **blokovaná slova**.

    ![seznam blokovaných slov pro úpravy a nahrání pro zásady pojmenování](./media/groups-naming-policy/blockedwords.png)

1. Výběrem položky **Stáhnout** zobrazíte nebo upravíte aktuální seznam vlastních blokovaných slov.
1. Kliknutím na ikonu souboru Nahrajte nový seznam vlastních blokovaných slov.
1. Uložte změny pro nové zásady, aby se projevily, a to tak, že vyberete **Uložit**.

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

   Pokud se zobrazí výzva k přístupu k nedůvěryhodnému úložišti, zadejte **Y**. Instalace nového modulu může trvat několik minut.

## <a name="configure-naming-policy-in-powershell"></a>Konfigurace zásady pojmenování v PowerShellu

1. Otevřete v počítači okno prostředí Windows PowerShell. Můžete ho otevřít bez zvýšených oprávnění.

1. Spuštěním následujících příkazů se připravte na spouštění rutin.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   Na obrazovce **Přihlášení k účtu**, která se otevře, zadejte svůj účet a heslo správce pro připojení k vaší službě a vyberte **Přihlásit se**.

1. Podle pokynů v části [Azure Active Directory rutiny nakonfigurujte nastavení skupiny](../enterprise-users/groups-settings-cmdlets.md) pro vytvoření nastavení skupiny pro tuto organizaci.

### <a name="view-the-current-settings"></a>Zobrazit aktuální nastavení

1. Načte aktuální zásady pojmenování a zobrazí aktuální nastavení.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Zobrazte aktuální nastavení skupiny.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Nastavení zásad pro pojmenování a vlastních blokovaných slov

1. Nastavte předpony a přípony názvů skupin v Azure AD PowerShellu. Aby funkce fungovala správně, musí být v nastavení zahrnutá [název_skupiny].
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Nastavte vlastní blokovaná slova, která chcete zakázat. Následující příklad ukazuje, jak můžete přidat vlastní slova.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Uložte nastavení, aby se nová zásada projevila, jako v následujícím příkladu.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
Hotovo. Nastavili jste zásady pro pojmenování a Přidali jste blokovaná slova.

## <a name="export-or-import-custom-blocked-words"></a>Export nebo import vlastních blokovaných slov

Další informace najdete v článku [rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../enterprise-users/groups-settings-cmdlets.md).

Tady je příklad skriptu PowerShellu pro export více blokovaných slov:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Tady je příklad skriptu PowerShellu pro import více blokovaných slov:

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

## <a name="remove-the-naming-policy"></a>Odebrání zásady pojmenování

### <a name="remove-the-naming-policy-using-azure-portal"></a>Odeberte zásady pojmenování pomocí Azure Portal

1. Na stránce **zásady pojmenování** vyberte **Odstranit zásadu**.
1. Po potvrzení odstranění se odeberou zásady pojmenování, včetně všech zásad pojmenování přípon předpon a všech blokovaných slov.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Odebrání zásady pojmenování pomocí Azure AD PowerShellu

1. Ve službě Azure AD PowerShell vyprázdněte předpony a přípony názvu skupiny.
  
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

## <a name="experience-across-microsoft-365-apps"></a>Prostředí v aplikacích Microsoft 365

Když ve službě Azure AD nastavíte zásadu pro pojmenování skupin, uživatel vytvoří skupinu v aplikaci Microsoft 365, zobrazí tyto informace:

- Náhled názvu v závislosti na vašich zásadách pro pojmenování (s předponami a příponami), jakmile uživatel zadá název skupiny
- Pokud uživatel zadá blokovaná slova, uvidí chybovou zprávu, aby mohla odebrat blokovaná slova.

Úloha | Dodržování předpisů
----------- | -------------------------------
Portály Azure Active Directory | Na portálu Azure AD a na portálu přístupového panelu se při vytváření nebo úpravách skupiny zobrazují názvy vytvářené v zásadách pojmenování při zadání názvu skupiny. Když uživatel zadá vlastní blokované slovo, zobrazí se chybová zpráva s blokovaným slovem, aby ho uživatel mohl odebrat.
Aplikace Outlook Web Access (OWA) | Pokud uživatel zadá název skupiny nebo alias skupiny, Web Access aplikace Outlook zobrazí název vynucované zásady pojmenování. Když uživatel zadá vlastní blokované slovo, zobrazí se v uživatelském rozhraní spolu s blokovaným slovem chybová zpráva, aby ho uživatel mohl odebrat.
Aplikace Outlook Desktop | Skupiny vytvořené v aplikaci Outlook Desktop jsou kompatibilní s nastavením zásad pojmenování. Desktopová aplikace pro Outlook zatím nezobrazuje náhled vynutilého názvu skupiny a nevrátí vlastní blokované chyby Wordu, když uživatel zadá název skupiny. Zásady pojmenování se ale automaticky aplikují při vytváření nebo úpravách skupiny a uživatelé uvidí chybové zprávy, pokud se v názvu skupiny nebo aliasu nacházejí vlastní blokovaná slova.
Microsoft Teams | Pokud uživatel zadá název týmu, zobrazí se v Microsoft Teams název vynutilí zásady pojmenování skupin. Když uživatel zadá vlastní blokované slovo, zobrazí se chybová zpráva spolu s blokovaným slovem, aby ho uživatel mohl odebrat.
SharePoint  |  Pokud uživatel zadá název webu nebo e-mailovou adresu skupiny, SharePoint zobrazí název vynutilí zásady pojmenování. Když uživatel zadá vlastní blokované slovo, zobrazí se chybová zpráva spolu s blokovaným slovem, aby ho uživatel mohl odebrat.
Microsoft Stream | Pokud uživatel zadá název skupiny nebo e-mailový alias skupiny, Microsoft Stream zobrazí název vykonatelné zásady pojmenování skupin. Když uživatel zadá vlastní blokované slovo, zobrazí se chybová zpráva s blokovaným slovem, aby ho uživatel mohl odebrat.
Aplikace Outlook pro iOS a Android | Skupiny vytvořené v aplikacích Outlook jsou kompatibilní se zásadami konfigurovaných pojmenování. Mobilní aplikace Outlook zatím nezobrazuje náhled vydaného názvu zásady pojmenování a nevrátí vlastní blokované chyby Wordu, když uživatel zadá název skupiny. Zásady pojmenování se ale automaticky aplikují při kliknutí na vytvořit nebo upravit a uživatelé uvidí chybové zprávy, pokud se v názvu skupiny nebo aliasu nacházejí vlastní blokovaná slova.
Mobilní aplikace skupiny | Skupiny vytvořené v mobilní aplikaci skupiny jsou kompatibilní se zásadami pojmenování. V mobilní aplikaci skupiny se nezobrazuje náhled zásady pojmenování a nevrátí vlastní blokované chyby Wordu, když uživatel zadá název skupiny. Zásady pojmenování se ale při vytváření nebo úpravách skupiny automaticky aplikují a uživatelům se zobrazí příslušné chyby, pokud se v názvu skupiny nebo aliasu nacházejí vlastní blokovaná slova.
Planner | Plánovač je kompatibilní se zásadami pojmenování. V Planneru se při zadávání názvu plánu zobrazí náhled zásady pojmenování. Když uživatel zadá vlastní blokované slovo, při vytváření plánu se zobrazí chybová zpráva.
Dynamics 365 for Customer Engagement | Dynamics 365 pro zapojení zákazníka je v souladu se zásadami pojmenování. V případě, že uživatel zadá název skupiny nebo e-mailový alias skupiny, zobrazí se v Dynamics 365 název vykonatelné zásady pojmenování. Když uživatel zadá vlastní blokované slovo, zobrazí se chybová zpráva s blokovaným slovem, aby ho uživatel mohl odebrat.
Synchronizace školních dat (SDS) | Skupiny vytvořené prostřednictvím SDS dodržují zásady pojmenování, ale zásady pojmenování se neaplikují automaticky. Správci SDS musí připojit předpony a přípony k názvům tříd, pro které se musí vytvořit skupiny a pak se nahrají do SDS. Vytvoření nebo úprava skupiny by jinak nedošlo k chybě.
Aplikace učeben | Skupiny vytvořené v aplikaci učeben vyhovují zásadám pojmenování, ale zásady pojmenování se neaplikují automaticky a verze Preview se uživatelům při zadávání názvu skupiny učeben nezobrazuje. Uživatelé musí zadat název skupiny vynutilého učebny s předponami a příponami. V takovém případě se operace vytvoření nebo úpravy skupiny učeben nepovede s chybami.
Power BI | Power BI pracovní prostory jsou kompatibilní se zásadami pojmenování.    
Yammer | Když se uživatel přihlásil ke službě Yammer pomocí svého Azure Active Directory účtu vytvoří skupinu nebo upraví název skupiny, bude název skupiny odpovídat zásadám pojmenování. To platí pro Microsoft 365 propojených skupin i všech ostatních skupin Yammeru.<br>Pokud byla před vytvořením zásady pojmenování vytvořena připojená skupina Microsoft 365, název skupiny nebude automaticky dodržovat zásady pojmenování. Když uživatel upraví název skupiny, zobrazí se výzva k přidání předpony a přípony.
Směn staffhubu  | Směn staffhubu týmy nedodržují zásady pojmenování, ale podkladová skupina Microsoft 365. Název týmu směn staffhubu nepoužívá předpony a přípony a nekontroluje vlastní blokovaná slova. Ale směn staffhubu použije předpony a přípony a odstraní blokovaná slova z podkladové skupiny Microsoft 365.
Prostředí Exchange PowerShell | Rutiny prostředí Exchange PowerShell jsou kompatibilní se zásadami pojmenování. Uživatelé dostanou vhodné chybové zprávy s navrhovanými předponami a příponami a u vlastních blokovaných slov, pokud nedodržují zásady pojmenování v názvu skupiny a aliasu skupiny (mailNickname).
Rutiny Azure Active Directory PowerShellu | Rutiny Azure Active Directory PowerShellu jsou kompatibilní se zásadami pojmenování. Uživatelé dostanou příslušné chybové zprávy s navrhovanými předponami a příponami a vlastní blokovaná slova, pokud nedodržují zásady vytváření názvů v názvech skupin a aliasu skupiny.
Centrum pro správu Exchange | Centrum pro správu Exchange je kompatibilní se zásadami vytváření názvů. Uživatelé dostanou vhodné chybové zprávy s navrhovanými předponami a příponami a vlastní blokovaná slova, pokud nedodržují zásady vytváření názvů v názvu skupiny a aliasu skupiny.
Centrum pro správu služby Microsoft 365 | Centrum pro správu Microsoft 365 je kompatibilní se zásadami vytváření názvů. Když uživatel vytvoří nebo upraví názvy skupin, automaticky se použije zásada pro pojmenování a uživatelé dostanou při zadávání vlastních blokovaných slov příslušné chyby. Centrum pro správu Microsoft 365 ještě nezobrazuje náhled zásad pojmenování a nevrátí vlastní blokované chyby Wordu, když uživatel zadá název skupiny.

## <a name="next-steps"></a>Další kroky

Tyto články poskytují další informace o skupinách Azure AD.

- [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Zásady vypršení platnosti pro skupiny Microsoft 365](groups-lifecycle.md)
- [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Správa členů skupiny](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
