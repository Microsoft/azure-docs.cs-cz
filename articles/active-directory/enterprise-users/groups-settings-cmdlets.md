---
title: Konfigurace nastavení skupiny pomocí prostředí PowerShell – Azure AD | Microsoft Docs
description: Jak spravovat nastavení pro skupiny pomocí rutin Azure Active Directory
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
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c48e23de6832999b262283c0bf6664b4dfe88ee7
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881561"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Rutiny Azure Active Directory pro konfiguraci nastavení skupiny

Tento článek obsahuje pokyny k vytvoření a aktualizaci skupin pomocí rutin prostředí PowerShell pro Azure Active Directory (Azure AD). Tento obsah se vztahuje pouze na skupiny Microsoft 365 (někdy označované jako sjednocené skupiny).

> [!IMPORTANT]
> Některá nastavení vyžadují licenci Azure Active Directory Premium P1. Další informace najdete v tabulce [Nastavení šablon](#template-settings) .

Další informace o tom, jak zabránit uživatelům, kteří nejsou správci, aby vytvářeli skupiny zabezpečení, nastavte `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` podle popisu v [set-MSOLCompanySettings](/powershell/module/msonline/set-msolcompanysettings).

Nastavení skupin Microsoft 365 se konfigurují pomocí objektu nastavení a objektu SettingsTemplate. Zpočátku se ve vašem adresáři nezobrazí žádné objekty nastavení, protože váš adresář je nakonfigurovaný s výchozím nastavením. Chcete-li změnit výchozí nastavení, je nutné vytvořit nový objekt nastavení pomocí šablony nastavení. Šablony nastavení jsou definovány společností Microsoft. Existuje několik různých šablon nastavení. Ke konfiguraci nastavení Microsoft 365 skupiny pro adresář použijte šablonu s názvem "Group. Unified". Pokud chcete nakonfigurovat Microsoft 365 nastavení skupiny pro jednu skupinu, použijte šablonu s názvem "Group. Unified. host". Tato šablona se používá ke správě přístupu hosta ke skupině Microsoft 365. 

Rutiny jsou součástí modulu Azure Active Directory PowerShell v2. Pokyny ke stažení a instalaci modulu do počítače najdete v článku [Azure Active Directory PowerShell verze 2](/powershell/azure/active-directory/overview). Verzi 2 tohoto modulu můžete nainstalovat z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-powershell-cmdlets"></a>Instalace rutin PowerShellu

Před spuštěním příkazů PowerShellu si nezapomeňte odinstalovat všechny starší verze modulu Azure Active Directory PowerShellu pro modul Graph pro prostředí Windows PowerShell a nainstalovat [Azure Active Directory prostředí PowerShell pro Public Preview verzi graphu (novější než 2.0.0.137)](https://www.powershellgallery.com/packages/AzureADPreview) .

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
Tyto kroky vytvoří nastavení na úrovni adresáře, které platí pro všechny Microsoft 365 skupiny v adresáři. Rutina Get-AzureADDirectorySettingTemplate je k dispozici pouze v [modulu Azure AD PowerShell Preview pro graf](https://www.powershellgallery.com/packages/AzureADPreview).

1. V rutinách DirectorySettings je nutné zadat ID SettingsTemplate, které chcete použít. Pokud toto ID neznáte, vrátí tato rutina seznam všech šablon nastavení:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   Toto volání rutiny vrátí všechny dostupné šablony:
  
   ``` PowerShell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Microsoft 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Chcete-li přidat adresu URL pokynů pro použití, nejprve potřebujete získat objekt SettingsTemplate, který definuje hodnotu adresy URL směrnice o použití. To znamená, že šablona Group. Unified:
  
   ```powershell
   $TemplateId = (Get-AzureADDirectorySettingTemplate | where { $_.DisplayName -eq "Group.Unified" }).Id
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value $TemplateId -EQ
   ```
3. Dále vytvořte nový objekt nastavení na základě této šablony:
  
   ```powershell
   $Setting = $Template.CreateDirectorySetting()
   ```  
4. Pak aktualizujte objekt nastavení novou hodnotou. V následujících dvou příkladech se mění hodnota směrnice o využití a povolují se popisky citlivosti. V šabloně nastavte tyto nebo jakékoli jiné nastavení podle potřeby:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   $Setting["EnableMIPLabels"] = "True"
   ```  
5. Pak použijte nastavení:
  
   ```powershell
   New-AzureADDirectorySetting -DirectorySetting $Setting
   ```
6. Hodnoty můžete číst pomocí:

   ```powershell
   $Setting.Values
   ```
   
## <a name="update-settings-at-the-directory-level"></a>Aktualizovat nastavení na úrovni adresáře
Pokud chcete aktualizovat hodnotu UsageGuideLinesUrl v šabloně nastavení, přečtěte si aktuální nastavení z Azure AD, jinak jsme mohli vymezit přepsání stávajících nastavení kromě UsageGuideLinesUrl.

1. Získá aktuální nastavení ze skupiny. Unified SettingsTemplate:
   
   ```powershell
   $Setting = Get-AzureADDirectorySetting | ? { $_.DisplayName -eq "Group.Unified"}
   ```  
2. Ověřte aktuální nastavení:
   
   ```powershell
   $Setting.Values
   ```
   
   Výstup:
   ```powershell
    Name                          Value
    ----                          -----
    EnableMIPLabels               True
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
3. Pokud chcete hodnotu UsageGuideLinesUrl odebrat, upravte adresu URL tak, aby byla prázdným řetězcem:
   
   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
4. Uložit aktualizaci do adresáře:
   
   ```powershell
   Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
   ```  

## <a name="template-settings"></a>Nastavení šablony
Tady jsou nastavení definovaná ve skupině. Unified SettingsTemplate. Pokud není uvedeno jinak, tyto funkce vyžadují licenci Azure Active Directory Premium P1. 

| **Nastavení** | **Popis** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Typ: Boolean<li>Výchozí: true |Příznak označující, zda je povoleno vytváření skupin Microsoft 365 v adresáři pomocí uživatelů bez oprávnění správce. Toto nastavení nevyžaduje licenci Azure Active Directory Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Typ: Řetězec<li>Výchozí: "" |Identifikátor GUID skupiny zabezpečení, pro kterou můžou členové vytvářet Microsoft 365 skupin i v případě, že EnableGroupCreation = = false |
|  <ul><li>UsageGuidelinesUrl<li>Typ: Řetězec<li>Výchozí: "" |Odkaz na pokyny pro použití skupiny |
|  <ul><li>ClassificationDescriptions<li>Typ: Řetězec<li>Výchozí: "" | Seznam popisů klasifikace oddělený čárkami. Hodnota ClassificationDescriptions je platná pouze v tomto formátu:<br>$setting ["ClassificationDescriptions"] = "klasifikace: Popis, klasifikace: Popis"<br>kde klasifikace odpovídá položce v ClassificationList.<br>Toto nastavení se nevztahuje na EnableMIPLabels = = true.|
|  <ul><li>DefaultClassification<li>Typ: Řetězec<li>Výchozí: "" | Klasifikace, která má být použita jako výchozí klasifikace pro skupinu, pokud nebyla zadána žádná.<br>Toto nastavení se nevztahuje na EnableMIPLabels = = true.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Typ: Řetězec<li>Výchozí: "" | Řetězec o maximální délce 64 znaků definující konvence pojmenování nakonfigurovaných pro Microsoft 365 skupiny. Další informace najdete v tématu [vymáhání zásady pojmenování pro skupiny Microsoft 365](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Typ: Řetězec<li>Výchozí: "" | Textový řetězec oddělený čárkami, který uživatelé nebudou moct používat ve skupinových názvech nebo aliasech. Další informace najdete v tématu [vymáhání zásady pojmenování pro skupiny Microsoft 365](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Typ: Boolean<li>Výchozí: false | Nepoužívat
|  <ul><li>AllowGuestsToBeGroupOwner<li>Typ: Boolean<li>Výchozí: false | Logická hodnota označující, zda uživatel typu Host může být vlastníkem skupin. |
|  <ul><li>AllowGuestsToAccessGroups<li>Typ: Boolean<li>Výchozí: true | Logická hodnota označující, jestli uživatel typu Host může mít přístup k obsahu Microsoft 365 skupin.  Toto nastavení nevyžaduje licenci Azure Active Directory Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Typ: Řetězec<li>Výchozí: "" | Adresa URL odkazu na pokyny pro použití hostů |
|  <ul><li>AllowToAddGuests<li>Typ: Boolean<li>Výchozí: true | Logická hodnota označující, zda je povoleno přidávání hostů do tohoto adresáře. <br>Toto nastavení může být přepsáno a lze ho nastavit jen pro čtení, pokud je *EnableMIPLabels* nastaveno na *hodnotu true* a zásada hosta je přidružena k popisku citlivosti přiřazenému ke skupině.<br>Pokud je nastavení AllowToAddGuests na úrovni organizace nastaveno na false, všechna nastavení AllowToAddGuests na úrovni skupiny se ignorují. Pokud chcete povolit přístup hosta jenom pro několik skupin, musíte nastavit AllowToAddGuests na hodnotu true na úrovni organizace a pak ho selektivně zakázat pro konkrétní skupiny. |
|  <ul><li>ClassificationList<li>Typ: Řetězec<li>Výchozí: "" | Seznam platných hodnot klasifikace, které lze použít pro Microsoft 365 skupiny, oddělený čárkami. <br>Toto nastavení se nevztahuje na EnableMIPLabels = = true.|
|  <ul><li>EnableMIPLabels<li>Typ: Boolean<li>Výchozí: false |Příznak označující, zda lze pro Microsoft 365 skupiny použít popisky citlivosti publikované v centru kompatibility Microsoft 365. Další informace najdete v tématu [přiřazení popisků citlivosti pro Microsoft 365 skupiny](groups-assign-sensitivity-labels.md). |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Příklad: Konfigurace zásady hosta pro skupiny na úrovni adresáře
1. Získání všech šablon nastavení:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Pokud chcete nastavit zásady hosta pro skupiny na úrovni adresáře, potřebujete Group. Unified Template.
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "62375ab9-6b52-47ed-826b-58e47e0e304b" -EQ
   ```
3. Dále vytvořte nový objekt nastavení na základě této šablony:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Pak aktualizujte nastavení AllowToAddGuests
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. Pak použijte nastavení:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Hodnoty můžete číst pomocí:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Číst nastavení na úrovni adresáře

Pokud znáte název nastavení, které chcete načíst, můžete k načtení aktuální hodnoty nastavení použít následující rutinu. V tomto příkladu načítáme hodnotu pro nastavení s názvem "UsageGuidelinesUrl". 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
Tyto kroky načtou nastavení na úrovni adresáře, které platí pro všechny skupiny Office v adresáři.

1. Číst všechna existující nastavení adresáře:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   Tato rutina vrátí seznam všech nastavení adresáře:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Číst všechna nastavení pro konkrétní skupinu:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Přečtěte si všechny hodnoty nastavení adresáře určitého objektu nastavení adresáře, a to pomocí nastavení identifikátor GUID ID:
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

## <a name="remove-settings-at-the-directory-level"></a>Odebrat nastavení na úrovni adresáře
Tento krok odebere nastavení na úrovni adresáře, která platí pro všechny skupiny Office v adresáři.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Vytvoření nastavení pro určitou skupinu

1. Vyhledejte šablonu nastavení s názvem groups. Unified. Host.
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Microsoft 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. Načíst objekt šablony pro šablonu groups. Unified. Host:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "08d542b9-071f-4e16-94b0-74abb372e3d9" -EQ
   ```
3. Vytvořit nový objekt nastavení ze šablony:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Nastavte nastavení na požadovanou hodnotu:
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. Získejte ID skupiny, na kterou chcete toto nastavení použít:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Vytvořte nové nastavení pro požadovanou skupinu v adresáři:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. Nastavení ověříte spuštěním tohoto příkazu:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Aktualizovat nastavení pro konkrétní skupinu
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
4. Pak Získejte ID nastavení pro tuto konkrétní skupinu:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   Dostanete odpověď podobnou této:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Pak můžete nastavit novou hodnotu pro toto nastavení:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. Pokud se chcete ujistit, že se správně aktualizovala, můžete si přečíst hodnotu nastavení:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Reference k syntaxi rutin
Další Azure Active Directory dokumentaci PowerShellu najdete v [Azure Active Directory rutinách](/powershell/azure/active-directory/install-adv2).

## <a name="additional-reading"></a>Další materiály ke čtení

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
