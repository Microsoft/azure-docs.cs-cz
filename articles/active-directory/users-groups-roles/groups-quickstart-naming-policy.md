---
title: Přidání názvů rychlý start zásady pro skupiny Office 365 – Azure Active Directory | Dokumentace Microsoftu
description: Tento článek vysvětluje, jak ve službě Azure Active Directory přidat nové uživatele nebo odstranit existující uživatele.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 43ce034f2960f31d614636c77e8f30f05e8aa41e
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507096"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Rychlý start: Pojmenování zásady pro skupiny ve službě Azure Active Directory

V tomto rychlém startu nastavíte ve svém tenantovi Azure Active Directory (Azure AD) zásady pojmenování pro skupiny Office 365 vytvořené uživatelem, které vám pomůžou řadit a prohledávat skupiny ve vašem tenantovi. Zásady pojmenování můžete použít například k následujícím účelům:

* Informování o funkci skupiny, členství, geografické oblasti a uživateli, který skupinu vytvořil.
* Pomoc s uspořádáním skupin do kategorií v adresáři.
* Blokování použití určitých slov v názvech a aliasech skupin.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="install-powershell-cmdlets"></a>Instalace rutin PowerShellu

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

## <a name="set-up-naming-policy"></a>Nastavení zásad pojmenování

### <a name="step-1-sign-in-using-powershell-cmdlets"></a>Krok 1: Přihlaste se pomocí rutin prostředí PowerShell

1. Otevřete aplikaci Windows PowerShell. Nepotřebujete zvýšená oprávnění.

2. Spuštěním následujících příkazů se připravte na spouštění rutin.
  
  ```
  Import-Module AzureADPreview
  Connect-AzureAD
  ```
  Na obrazovce **Přihlášení k účtu**, která se otevře, zadejte svůj účet a heslo správce pro připojení k vaší službě a vyberte **Přihlásit se**.

3. Podle pokynů v tématu [Rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](groups-settings-cmdlets.md) vytvořte nastavení skupiny pro tohoto tenanta.

### <a name="step-2-view-the-current-settings"></a>Krok 2: Zobrazit aktuální nastavení

1. Zobrazte aktuální nastavení zásad pojmenování.
  
  ```
  $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ```
  
2. Zobrazte aktuální nastavení skupiny.
  
  ```
  $Setting.Values
  ```
  
### <a name="step-3-set-the-naming-policy-and-any-custom-blocked-words"></a>Krok 3: Nastavte zásady pojmenování a jakékoli vlastní blokované slova

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
  
A to je vše. Nastavili jste zásady pojmenování a přidali jste vlastní blokovaná slova.

## <a name="clean-up-resources"></a>Vyčištění prostředků

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

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak pomocí rutin PowerShellu nastavit zásady pojmenování pro tenanta Azure AD.

Další informace o technických omezeních, přidání seznamu vlastních blokovaných slov nebo prostředích pro koncové uživatele v různých aplikacích Office 365 najdete v dalším článku, který obsahuje další podrobnosti o zásadách pojmenování.
> [!div class="nextstepaction"]
> [Všechny podrobnosti o zásadách pojmenování](groups-naming-policy.md)