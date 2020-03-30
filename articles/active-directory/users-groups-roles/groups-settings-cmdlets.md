---
title: Konfigurace nastavení skupiny pomocí PowerShellu – Azure AD | Dokumenty společnosti Microsoft
description: Správa nastavení skupin pomocí rutin služby Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/20/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d56bb7c30a8289fe7f261979dca6a4ffe2bfe99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048144"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Rutiny Azure Active Directory pro konfiguraci nastavení skupiny

Tento článek obsahuje pokyny pro použití rutin prostředí PowerShell azure active directory (Azure AD) k vytváření a aktualizaci skupin. Tento obsah se vztahuje jenom pro skupiny Office 365 (někdy nazývané sjednocené skupiny).

> [!IMPORTANT]
> Některá nastavení vyžadují licenci Azure Active Directory Premium P1. Další informace naleznete v tabulce [Nastavení šablony.](#template-settings)

Další informace o tom, jak zabránit uživatelům, kteří `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` nejsou správci, vytvářet skupiny zabezpečení, nastavte tak, jak je popsáno v [nastavení Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

Nastavení skupin Office 365 se nakonfiguruje pomocí objektu Settings a objektu SettingsTemplate. Zpočátku se v adresáři nezobrazují žádné objekty Nastavení, protože adresář je nakonfigurován s výchozím nastavením. Chcete-li změnit výchozí nastavení, musíte vytvořit nový objekt nastavení pomocí šablony nastavení. Šablony nastavení jsou definovány společností Microsoft. Existuje několik různých šablon nastavení. Chcete-li nakonfigurovat nastavení skupiny Office 365 pro váš adresář, použijte šablonu s názvem "Group.Unified". Chcete-li nakonfigurovat nastavení skupiny Office 365 v jedné skupině, použijte šablonu s názvem Group.Unified.Guest. Tato šablona se používá ke správě přístupu hosta ke skupině Office 365. 

Rutiny jsou součástí modulu Azure Active Directory PowerShell V2. Pokyny ke stažení a instalaci modulu do počítače naleznete v článku [Azure Active Directory PowerShell verze 2](https://docs.microsoft.com/powershell/azuread/). Verzi modulu verze 2 můžete nainstalovat z [galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-powershell-cmdlets"></a>Instalace rutin PowerShellu

Nezapomeňte před spuštěním příkazů Prostředí PowerShell u prostředí PowerShell azure active directory pro graf pro Windows PowerShell odinstalovat všechny starší verze modulu Azure Active Directory PowerShell pro Windows PowerShell a nainstalovat [Azure Active Directory PowerShell pro graf – verzi Public Preview (novější než 2.0.0.137).](https://www.powershellgallery.com/packages/AzureADPreview)

1. Otevřete aplikaci Windows PowerShell jako správce.
2. Odinstalujte všechny předchozí verze AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   Uninstall-Module azuread
   ```

3. Nainstalujte nejnovější verzi AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```
   
## <a name="create-settings-at-the-directory-level"></a>Vytvoření nastavení na úrovni adresáře
Tyto kroky vytvoří nastavení na úrovni adresáře, která platí pro všechny skupiny Office 365 v adresáři. Rutina Get-AzureADDirectorySettingTemplate je k dispozici jenom v [modulu Azure AD PowerShell Preview pro graph](https://www.powershellgallery.com/packages/AzureADPreview).

1. V rutinách DirectorySettings je nutné zadat ID šablony Nastavení, kterou chcete použít. Pokud toto ID neznáte, tato rutina vrátí seznam všech šablon nastavení:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   Toto volání rutiny vrátí všechny šablony, které jsou k dispozici:
  
   ``` PowerShell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Chcete-li přidat adresu URL pokynů k použití, musíte nejprve získat objekt SettingsTemplate, který definuje hodnotu adresy URL pokynů pro použití; to znamená šablonu Group.Unified:
  
   ```powershell
   $TemplateId = (Get-AzureADDirectorySettingTemplate | where { $_.DisplayName -eq "Group.Unified" }).Id
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value $TemplateId -EQ
   ```
3. Dále vytvořte nový objekt nastavení založený na této šabloně:
  
   ```powershell
   $Setting = $Template.CreateDirectorySetting()
   ```  
4. Potom aktualizujte hodnotu pokynu pro použití:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Poté použijte nastavení:
  
   ```powershell
   New-AzureADDirectorySetting -DirectorySetting $Setting
   ```
6. Hodnoty si můžete přečíst pomocí:

   ```powershell
   $Setting.Values
   ```
   
## <a name="update-settings-at-the-directory-level"></a>Aktualizovat nastavení na úrovni adresáře
Chcete-li aktualizovat hodnotu UsageGuideLinesUrl v šabloně nastavení, přečtěte si aktuální nastavení z Azure AD, jinak bychom mohli skončit přepsání existující nastavení než UsageGuideLinesUrl.

1. Získejte aktuální nastavení ze skupiny.Unified SettingsTemplate:
   
   ```powershell
   $Setting = Get-AzureADDirectorySetting | ? { $_.DisplayName -eq "Group.Unified"}
   ```  
2. Zkontrolujte aktuální nastavení:
   
   ```powershell
   $Setting.Values
   ```
   
   Výstup:
   ```powershell
    Name                          Value
    ----                          -----
    EnableMIPLabels               false
    CustomBlockedWordsList
    EnableMSStandardBlockedWords  False
    ClassificationDescriptions
    DefaultClassification
    PrefixSuffixNamingRequirement
    AllowGuestsToBeGroupOwner     False
    AllowGuestsToAccessGroups     True
    GuestUsageGuidelinesUrl
    GroupCreationAllowedGroupId
    AllowToAddGuests              True
    UsageGuidelinesUrl            https://guideline.example.com
    ClassificationList
    EnableGroupCreation           True
    ```
3. Chcete-li odebrat hodnotu UsageGuideLinesUrl, upravte adresu URL tak, aby byla prázdný řetězec:
   
   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
4. Uložit aktualizaci do adresáře:
   
   ```powershell
   Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
   ```  

## <a name="template-settings"></a>Nastavení šablony
Zde jsou nastavení definovaná v šabloně Group.Unified SettingsTemplate. Pokud není uvedeno jinak, vyžadují tyto funkce licenci Azure Active Directory Premium P1. 

| **Nastavení** | **Popis** |
| --- | --- |
|  <ul><li>Povolit vytváření skupin<li>Typ: Logická hodnota<li>Výchozí: True |Příznak označující, zda je vytváření skupin Office 365 povoleno v adresáři uživateli, kteří nejsou správci. Toto nastavení nevyžaduje licenci Azure Active Directory Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Typ: Řetězec<li>Výchozí: "" |Identifikátor GUID skupiny zabezpečení, pro které mohou členové vytvářet skupiny Office 365 i v případě, že povolitgroupcreation == false. |
|  <ul><li>PoužitíGuidelinesUrl<li>Typ: Řetězec<li>Výchozí: "" |Odkaz na pokyny k používání skupiny. |
|  <ul><li>KlasifikacePopisy<li>Typ: Řetězec<li>Výchozí: "" | Seznam popisů klasifikace oddělených čárkami. Hodnota ClassificationDescriptions je platná pouze v tomto formátu:<br>$setting["ClassificationDescriptions"] ="Klasifikace:Popis,Klasifikace:Popis"<br>kde klasifikace odpovídá položce v seznamu klasifikací.<br>Toto nastavení neplatí, pokud EnableMIPLabels == True.|
|  <ul><li>Výchozí klasifikace<li>Typ: Řetězec<li>Výchozí: "" | Klasifikace, která má být použita jako výchozí klasifikace pro skupinu, pokud nebyla zadána žádná.<br>Toto nastavení neplatí, pokud EnableMIPLabels == True.|
|  <ul><li>PředpouSuffixNamingRequirement<li>Typ: Řetězec<li>Výchozí: "" | Řetězec o maximální délce 64 znaků, který definuje konvenci pojmenování nakonfigurovanou pro skupiny Office 365. Další informace najdete [v tématu Vynucení zásad pojmenování pro skupiny Office 365](groups-naming-policy.md). |
| <ul><li>Vlastní BlockedWordsList<li>Typ: Řetězec<li>Výchozí: "" | Řetězec frází oddělených čárkami, které uživatelé nebudou moci používat v názvech skupin nebo aliasech. Další informace najdete [v tématu Vynucení zásad pojmenování pro skupiny Office 365](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Typ: Logická hodnota<li>Výchozí: "False" | Nepoužívejte
|  <ul><li>AllowGuestsToBeGroupOwner<li>Typ: Logická hodnota<li>Výchozí: False | Logická hodnota označující, zda může být vlastníkem skupin uživatel typu Host. |
|  <ul><li>Povolitguestsaccessgroups<li>Typ: Logická hodnota<li>Výchozí: True | Logická hodnota označující, zda může mít uživatel typu Host přístup k obsahu skupin Office 365.  Toto nastavení nevyžaduje licenci Azure Active Directory Premium P1.|
|  <ul><li>Adresa GuestUsageGuidelinesUrl<li>Typ: Řetězec<li>Výchozí: "" | Adresa URL odkazu na pokyny pro použití hosta. |
|  <ul><li>AllowToAddGuests<li>Typ: Logická hodnota<li>Výchozí: True | Logická hodnota označující, zda je povoleno přidávat hosty do tohoto adresáře. <br>Toto nastavení může být přepsáno a stát se jen pro čtení, pokud *enableMIPLabels* je nastavena na *True* a zásady hosta je spojena s popisek citlivosti přiřazené ke skupině.<br>Pokud allowToAddGuests nastavení je nastavena na False na úrovni klienta, jakékoli AllowToAddGuests nastavení na úrovni skupiny je ignorována. Pokud chcete povolit přístup hosta pouze pro několik skupin, musíte nastavit AllowToAddGuests, aby byla pravdivá na úrovni klienta, a pak ji selektivně zakázat pro určité skupiny. |
|  <ul><li>Seznam klasifikace<li>Typ: Řetězec<li>Výchozí: "" | Seznam platných klasifikačních hodnot oddělených čárkami, který lze použít pro skupiny Office 365. <br>Toto nastavení neplatí, pokud EnableMIPLabels == True.|
|  <ul><li>EnableMIPLabels<li>Typ: Logická hodnota<li>Výchozí: "False" |Příznak označující, zda popisky citlivosti publikované v Centru dodržování předpisů Microsoft 365 lze použít pro skupiny Office 365. Další informace najdete [v tématu Přiřazení popisků citlivosti pro skupiny Office 365](groups-assign-sensitivity-labels.md). |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Příklad: Konfigurace zásad hosta pro skupiny na úrovni adresáře
1. Získejte všechny šablony nastavení:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Chcete-li nastavit zásady hosta pro skupiny na úrovni adresáře, potřebujete šablonu Group.Unified
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "62375ab9-6b52-47ed-826b-58e47e0e304b" -EQ
   ```
3. Dále vytvořte nový objekt nastavení založený na této šabloně:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Potom aktualizujte nastavení AllowToAddGuests
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. Poté použijte nastavení:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Hodnoty si můžete přečíst pomocí:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Čtení nastavení na úrovni adresáře

Pokud znáte název nastavení, které chcete načíst, můžete pomocí níže uvedené rutiny načíst aktuální hodnotu nastavení. V tomto příkladu načítáme hodnotu pro nastavení s názvem UsageGuidelinesUrl. 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
Tyto kroky načtou nastavení na úrovni adresáře, která platí pro všechny skupiny Office v adresáři.

1. Přečtěte si všechna existující nastavení adresáře:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   Tato rutina vrátí seznam všech nastavení adresáře:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Přečtěte si všechna nastavení pro určitou skupinu:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Přečtěte si všechny hodnoty nastavení adresáře konkrétního objektu nastavení adresáře pomocí identifikátoru GUID Nastavení ID:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   Tato rutina vrátí názvy a hodnoty v tomto objektu nastavení pro tuto konkrétní skupinu:
   ```powershell
   Name                          Value
   ----                          -----
   ClassificationDescriptions
   DefaultClassification
   PrefixSuffixNamingRequirement
   CustomBlockedWordsList        
   AllowGuestsToBeGroupOwner     False 
   AllowGuestsToAccessGroups     True
   GuestUsageGuidelinesUrl
   GroupCreationAllowedGroupId
   AllowToAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>Odebrání nastavení na úrovni adresáře
Tento krok odebere nastavení na úrovni adresáře, která platí pro všechny skupiny Office v adresáři.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Vytvoření nastavení pro určitou skupinu

1. Vyhledejte šablonu nastavení s názvem Groups.Unified.Guest.
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. Načtěte objekt šablony pro šablonu Groups.Unified.Guest:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "08d542b9-071f-4e16-94b0-74abb372e3d9" -EQ
   ```
3. Vytvořte nový objekt nastavení ze šablony:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Nastavte nastavení na požadovanou hodnotu:
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. Získejte ID skupiny, u které chcete toto nastavení použít:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Vytvořte nové nastavení pro požadovanou skupinu v adresáři:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. Chcete-li ověřit nastavení, spusťte tento příkaz:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Aktualizace nastavení pro určitou skupinu
1. Získejte ID skupiny, jejíž nastavení chcete aktualizovat:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. Načíst nastavení skupiny:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Aktualizujte nastavení skupiny podle potřeby, např.
   ```powershell
   $Setting["AllowToAddGuests"] = $True
   ```
4. Potom získejte ID nastavení pro tuto konkrétní skupinu:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   Obdržíte odpověď podobnou této:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Pak můžete nastavit novou hodnotu pro toto nastavení:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. Můžete si přečíst hodnotu nastavení a ujistěte se, že bylo správně aktualizováno:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Odkaz na syntaxi rutiny
Další dokumentaci k prostředí Azure Active Directory PowerShell najdete na [rutinách služby Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Dodatečné čtení

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
