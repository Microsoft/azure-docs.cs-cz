---
title: Konfigurace nastavení skupiny pomocí prostředí PowerShell ve službě Azure Active Directory | Microsoft Docs
description: Jak spravovat nastavení pro skupiny pomocí rutiny služby Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 02/20/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro;
ms.openlocfilehash: 5da5bd920becdc86e133cb1b4e67fb3125c2638e
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260528"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Rutiny Azure Active Directory pro konfiguraci nastavení skupiny
Tento článek obsahuje pokyny pro použití rutin prostředí PowerShell pro Azure Active Directory (Azure AD) k vytvoření a aktualizovat skupiny. Tento obsah platí pouze pro skupiny Office 365 (někdy se označuje jako jednotné skupiny). 

> [!IMPORTANT]
> Některá nastavení vyžadují licenci Azure Active Directory Premium P1. Další informace najdete v tématu [nastavení šablony](#template-settings) tabulky.

Další informace o tom, jak zabránit uživatelům bez oprávnění správce k vytvoření *zabezpečení* nastavit skupiny, `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` jak je popsáno v [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0). 

Nastavení skupiny Office 365 se konfigurují pomocí nastavení objektu a objekt SettingsTemplate. Na začátku nevidíte žádné objekty nastavení v adresáři, protože adresáře je konfigurován s výchozím nastavením. Chcete-li změnit výchozí nastavení, musíte vytvořit nový objekt nastavení pomocí nastavení šablony. Nastavení šablony jsou definovány společností Microsoft. Existuje několik různých nastavení šablon. Ke konfiguraci nastavení skupiny Office 365 pro váš adresář, použijete šablonu s názvem "Group.Unified". Na jedné skupiny, nakonfigurovat nastavení skupiny Office 365, použijte šablonu s názvem "Group.Unified.Guest". Tato šablona se používá ke správě hostů přístup ke skupině Office 365. 

Rutiny jsou součástí modulu Azure Active Directory PowerShell V2. Pokyny k tom, jak stáhnout a nainstalovat modul v počítači, najdete v článku [Azure Active Directory PowerShell verze 2](https://docs.microsoft.com/powershell/azuread/). Můžete nainstalovat verze 2 verzi modulu z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="retrieve-a-specific-settings-value"></a>Načíst hodnotu konkrétní nastavení
Pokud znáte název nastavení, můžete obnovit, můžete použít níže rutiny se načíst aktuální hodnotu nastavení. V tomto příkladu jsme se načítání hodnoty pro nastavení s názvem "UsageGuidelinesUrl." Další informace, že informace o nastavení adresářových služeb a jejich názvy další dolů v tomto článku.

```powershell
(Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
```

## <a name="create-settings-at-the-directory-level"></a>Vytvořit nastavení na úrovni adresáře
Pomocí těchto kroků vytvoříte nastavení na úrovni adresáře, které platí pro všechny skupiny Office 365 v adresáři.

1. Rutiny DirectorySettings musíte určit ID SettingsTemplate, kterou chcete použít. Pokud toto ID si nejste jisti, tato rutina vrátí seznam všech nastavení šablon:
  
  ```
  PS C:> Get-AzureADDirectorySettingTemplate
  ```
  Toto volání rutiny vrátí všechny šablony, které jsou k dispozici:
  
  ```
  Id                                   DisplayName         Description
  --                                   -----------         -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
  16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
  ```
2. Můžete přidat adresu URL obecných zásad použití, nejdřív je potřeba získat SettingsTemplate objekt, který definuje hodnotu adresy URL použití obecných zásad; To znamená, Group.Unified šablony:
  
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
  ```
3. Dále vytvořte nový objekt nastavení na základě této šablony:
  
  ```
  $Setting = $template.CreateDirectorySetting()
  ```  
4. Potom aktualizujte hodnotu využití obecné zásady:
  
  ```
  $setting["UsageGuidelinesUrl"] = "https://guideline.example.com"

  ```  
5. Nakonec použijte nastavení:
  
  ```
  New-AzureADDirectorySetting -DirectorySetting $setting
  ```

Po úspěšném rutina vrátí ID nového nastavení objektu:
  ```
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```
## <a name="template-settings"></a>Nastavení šablony
Tady jsou definované v Group.Unified SettingsTemplate nastavení. Pokud není uvedeno jinak, tyto funkce vyžadují licenci Azure Active Directory Premium P1. 

| **Nastavení** | **Popis** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Typ: logická hodnota<li>Výchozí: True |Příznak, který udává, zda je vytvoření skupiny Office 365 povolen v adresáři uživatelé bez oprávnění správce. Toto nastavení nevyžaduje licenci Azure Active Directory Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Typ: Řetězec<li>Výchozí hodnota: "" |Identifikátor GUID skupiny zabezpečení, které jsou členy nepodporuje vytvoření skupiny Office 365 i v případě EnableGroupCreation hodnotu false. |
|  <ul><li>UsageGuidelinesUrl<li>Typ: Řetězec<li>Výchozí hodnota: "" |Odkaz na pokyny využití skupiny. |
|  <ul><li>ClassificationDescriptions<li>Typ: Řetězec<li>Výchozí hodnota: "" | Čárkami oddělený seznam popisů klasifikace. |
|  <ul><li>DefaultClassification<li>Typ: Řetězec<li>Výchozí hodnota: "" | Klasifikace, který má být použita jako výchozí klasifikace pro skupinu. Pokud nebyl zadán.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Typ: Řetězec<li>Výchozí hodnota: "" | Řetězec s maximální délku 64 znaků, který definuje zásady vytváření názvů nakonfigurované pro skupiny Office 365. Další informace najdete v tématu [vynutit zásady pojmenování pro skupiny Office 365 (preview)](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Typ: Řetězec<li>Výchozí hodnota: "" | Textový soubor s oddělovači řetězec frází, které uživatelé nebudou moci používat v názvů skupin nebo aliasy. Další informace najdete v tématu [vynutit zásady pojmenování pro skupiny Office 365 (preview)](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Typ: logická hodnota<li>Výchozí hodnota: "False" | Nepoužívejte
|  <ul><li>AllowGuestsToBeGroupOwner<li>Typ: logická hodnota<li>Výchozí: False | Logická hodnota, která určuje, zda uživatel typu Host může být vlastníkem skupiny. |
|  <ul><li>AllowGuestsToAccessGroups<li>Typ: logická hodnota<li>Výchozí: True | Logická hodnota, která určuje, zda uživatel typu Host může mít přístup k obsahu skupiny Office 365.  Toto nastavení nevyžaduje licenci Azure Active Directory Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Typ: Řetězec<li>Výchozí hodnota: "" | Adresa url odkaz na pokyny využití hosta. |
|  <ul><li>AllowToAddGuests<li>Typ: logická hodnota<li>Výchozí: True | Logická hodnota označující, zda je povoleno Přidat hosty do tohoto adresáře.|
|  <ul><li>ClassificationList<li>Typ: Řetězec<li>Výchozí hodnota: "" |Čárkami oddělený seznam hodnot platný klasifikace, které lze použít pro skupiny Office 365. |

## <a name="read-settings-at-the-directory-level"></a>Číst nastavení na úrovni adresáře
Tyto kroky nastavení na úrovni adresáře, které platí pro všechny skupiny Office v adresáři přečíst.

1. Přečtěte si všechna existující nastavení adresáře:
  ```
  Get-AzureADDirectorySetting -All $True
  ```
  Tato rutina vrátí seznam všech nastavení adresáře:
  ```
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

2. Přečtěte si všechna nastavení pro konkrétní skupinu:
  ```
  Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
  ```

3. Načíst všechny hodnoty nastavení directory nastavení objektu konkrétního adresáře, pomocí Id GUID nastavení:
  ```
  (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
  ```
  Tato rutina vrací názvy a hodnoty v tomto objektu nastavení pro konkrétní skupiny:
  ```
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

## <a name="update-settings-for-a-specific-group"></a>Aktualizovat nastavení pro konkrétní skupinu

1. Vyhledejte nastavení šablony s názvem "Groups.Unified.Guest"
  ```
  Get-AzureADDirectorySettingTemplate
  
  Id                                   DisplayName            Description
  --                                   -----------            -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
  ```
2. Načtení objektu šablony Groups.Unified.Guest šablony:
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
  ```
3. Vytvořte nový objekt nastavení ze šablony:
  ```
  $Setting = $Template.CreateDirectorySetting()
  ```

4. Nastavení pro požadovaná hodnota:
  ```
  $Setting["AllowToAddGuests"]=$False
  ```
5. Vytvořte nové nastavení pro požadovanou skupinu v adresáři:
  ```
  New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
  ```

## <a name="update-settings-at-the-directory-level"></a>Aktualizujte nastavení na úrovni adresáře

Tyto kroky aktualizujte nastavení na úrovni adresáře, které platí pro všechny skupiny Office 365 v adresáři. Těchto příkladech se předpokládá, že již existuje objekt nastavení ve vašem adresáři.

1. Najít stávající objekt nastavení:
  ```
  Get-AzureADDirectorySetting | Where-object -Property Displayname -Value "Group.Unified" -EQ
  
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  
  $setting = Get-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323
  ```
2. Aktualizujte hodnotu:
  
  ```
  $Setting["AllowToAddGuests"] = "false"
  ```
3. Aktualizace nastavení:
  
  ```
  Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting
  ```

## <a name="remove-settings-at-the-directory-level"></a>Odeberte nastavení na úrovni adresáře
Tento krok odstraní nastavení na úrovni adresáře, které platí pro všechny skupiny Office v adresáři.
  ```
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>Referenční informace o rutinách syntaxe
Můžete najít další dokumentaci k Azure Active Directory PowerShell na [rutiny služby Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Další čtení

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](active-directory-manage-groups.md)
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)
