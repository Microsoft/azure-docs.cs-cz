---
title: Konfigurace nastavení skupiny v Azure Active Directory pomocí Powershellu | Dokumentace Microsoftu
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
ms.date: 06/13/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 9e065b04083cce958bc42f2efade0038bf137f8a
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145107"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Rutiny Azure Active Directory pro konfiguraci nastavení skupiny
Tento článek obsahuje pokyny, jak pomocí rutin Powershellu pro Azure Active Directory (Azure AD) k vytvoření a aktualizaci skupiny. Tento obsah platí jenom pro skupiny Office 365 (říká se jim sjednocené skupiny). 

> [!IMPORTANT]
> Některá nastavení vyžadují licenci Azure Active Directory Premium P1. Další informace najdete v tématu [nastavení šablony](#template-settings) tabulky.

Další informace o tom, jak zabránit uživatelům bez oprávnění správce k vytvoření *zabezpečení* nastavit skupiny, `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` jak je popsáno v [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0). 

Nastavení skupiny Office 365 konfigurují pomocí nastavení objektu a SettingsTemplate objektu. Na začátku nevidíte žádné objekty nastavení v adresáři, protože váš adresář se konfiguruje s výchozím nastavením. Chcete-li změnit výchozí nastavení, musíte vytvořit nový objekt nastavení pomocí nastavení šablony. Nastavení šablony jsou definovány společností Microsoft. Existuje několik různých nastavení šablon. Ke konfiguraci nastavení skupiny Office 365 pro svůj adresář použijete šablonu s názvem "Group.Unified". Ke konfiguraci nastavení skupiny Office 365 na jednu skupinu, použijte šablonu s názvem "Group.Unified.Guest". Tato šablona se používá ke správě přístup hosta ke službě skupiny Office 365. 

Rutiny jsou součástí daného modulu Azure Active Directory PowerShell V2. Pokyny k tom, jak stáhnout a nainstalovat modul ve vašem počítači, najdete v článku [Azure Active Directory PowerShell verze 2](https://docs.microsoft.com/powershell/azuread/). Můžete nainstalovat verzi 2 verze modulu z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="retrieve-a-specific-settings-value"></a>Načtení hodnoty konkrétní nastavení
Pokud znáte název nastavení, které chcete načíst, můžete použít následující rutinu k získání aktuální hodnoty nastavení. V tomto příkladu jsme při načítání hodnoty pro nastavení s názvem "UsageGuidelinesUrl." Si můžete přečíst, že informace o nastavení adresáře a jejich názvy níže v tomto článku.

```powershell
(Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
```

## <a name="create-settings-at-the-directory-level"></a>Vytvoření nastavení na úrovni adresáře
Tyto kroky vytvoří nastavení na úrovni adresáře, které platí pro všechny skupiny Office 365 v adresáři. Rutina Get-AzureADDirectorySettingTemplate je k dispozici pouze v [modul Azure AD Powershellu ve verzi Preview pro graf](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

1. V rutinách DirectorySettings musíte zadat ID SettingsTemplate, kterou chcete použít. Pokud toto ID si nejste jisti, tato rutina vrátí seznam všech nastavení šablon:
  
  ```
  PS C:> Get-AzureADDirectorySettingTemplate
  ```
  Toto volání rutina vrátí všechny šablony, které jsou k dispozici:
  
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
2. Můžete přidat adresu URL použití obecných zásad, nejprve je nutné získat SettingsTemplate objektu, který definuje hodnotu adresy URL použití obecných zásad; To znamená, Group.Unified šablony:
  
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
  ```
3. Dále vytvořte nový objekt nastavení založené na této šabloně:
  
  ```
  $Setting = $template.CreateDirectorySetting()
  ```  
4. Pak aktualizujte hodnotu použití obecných zásad:
  
  ```
  $setting["UsageGuidelinesUrl"] = "https://guideline.example.com"

  ```  
5. Nakonec použijte nastavení:
  
  ```
  New-AzureADDirectorySetting -DirectorySetting $setting
  ```

Po úspěšném dokončení rutina vrací ID nový objekt nastavení:
  ```
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```
## <a name="template-settings"></a>Nastavení šablony
Tady je nastavení definované v Group.Unified SettingsTemplate. Pokud není uvedeno jinak, tyto funkce vyžadují licenci Azure Active Directory Premium P1. 

| **Nastavení** | **Popis** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Typ: Boolean<li>Výchozí: True |Příznak označující, zda je vytvoření skupiny Office 365 povoleno v adresáři uživatelé bez oprávnění správce. Toto nastavení nevyžaduje licenci pro Azure Active Directory Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Typ: Řetězec<li>Výchozí hodnota: "" |Identifikátor GUID skupiny zabezpečení, pro které členové můžou vytvářet skupiny Office 365 i v případě EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Typ: Řetězec<li>Výchozí hodnota: "" |Odkaz na pokyny k používání skupin. |
|  <ul><li>ClassificationDescriptions<li>Typ: Řetězec<li>Výchozí hodnota: "" | Čárkami oddělený seznam popisů klasifikace. Hodnota ClassificationDescriptions je platná jenom v tomto formátu:
  $setting ["ClassificationDescriptions"] = "Klasifikace: popisu, klasifikace: popis", kde klasifikace odpovídá řetězcům v ClassificationList.|
|  <ul><li>DefaultClassification<li>Typ: Řetězec<li>Výchozí hodnota: "" | Klasifikace, která má být použita jako výchozí klasifikace pro skupinu Pokud nebyla zadána žádná.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Typ: Řetězec<li>Výchozí hodnota: "" | Řetězec s maximální délku 64 znaků, který definuje zásady vytváření názvů nakonfigurovaný pro skupiny Office 365. Další informace najdete v tématu [vynucení zásad pojmenování pro skupiny Office 365](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Typ: Řetězec<li>Výchozí hodnota: "" | Čárkou oddělený řetězec fráze, které uživatelé nebudou moci používat ve skupině názvy nebo aliasy. Další informace najdete v tématu [vynucení zásad pojmenování pro skupiny Office 365](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Typ: Boolean<li>Výchozí hodnota: "False" | Nepoužívejte
|  <ul><li>AllowGuestsToBeGroupOwner<li>Typ: Boolean<li>Výchozí: False | Logická hodnota označující, zda uživatel typu Host může být vlastníkem skupiny. |
|  <ul><li>AllowGuestsToAccessGroups<li>Typ: Boolean<li>Výchozí: True | Logická hodnota označující, zda uživatel typu Host může mít přístup k obsahu skupiny Office 365.  Toto nastavení nevyžaduje licenci pro Azure Active Directory Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Typ: Řetězec<li>Výchozí hodnota: "" | Adresa url odkaz na pokyny k používání hosta. |
|  <ul><li>AllowToAddGuests<li>Typ: Boolean<li>Výchozí: True | Typu boolean označující, zda je povoleno Přidat hosty do tohoto adresáře nebo ne.|
|  <ul><li>ClassificationList<li>Typ: Řetězec<li>Výchozí hodnota: "" |Čárkami oddělený seznam hodnot platnou klasifikace, které lze použít u skupin Office 365. |

## <a name="read-settings-at-the-directory-level"></a>Čtení nastavení na úrovni adresáře
Tyto kroky číst nastavení na úrovni adresáře, které platí pro všechny skupiny Office v adresáři.

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

2. Čtení všech nastavení pro konkrétní skupinu:
  ```
  Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
  ```

3. Číst všechny hodnoty nastavení adresáře objektu nastavení konkrétního adresáře, pomocí nastavení identifikátor GUID:
  ```
  (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
  ```
  Tato rutina vrátí názvy a hodnoty v tomto nastavení objektu pro tuto konkrétní skupinu:
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

1. Vyhledejte požadovanou šablonu nastavení s názvem "Groups.Unified.Guest"
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
2. Načtěte objekt šablony pro šablonu Groups.Unified.Guest:
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
5. Vytvořte nové nastavení pro požadované skupiny v adresáři:
  ```
  New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
  ```

## <a name="update-settings-at-the-directory-level"></a>Aktualizovat nastavení na úrovni adresáře

Postup aktualizace nastavení na úrovni adresáře, které platí pro všechny skupiny Office 365 v adresáři. Tyto příklady předpokládají, že již není objekt nastavení ve vašem adresáři.

1. Najdete stávající objekt nastavení:
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

## <a name="remove-settings-at-the-directory-level"></a>Odebrání nastavení na úrovni adresáře
Tento krok odstraní nastavení na úrovni adresáře, které platí pro všechny skupiny Office v adresáři.
  ```
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>Reference k rutinám syntaxe
Můžete najít další dokumentaci k Azure Active Directory PowerShell na [rutiny Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Další čtení

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Integrování místních identit do služby Azure Active Directory](../connect/active-directory-aadconnect.md)
