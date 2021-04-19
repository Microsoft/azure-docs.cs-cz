---
title: Připojení k Azure Security Center pomocí PowerShellu
description: Tento dokument vás provede procesem povolení Azure Security Center s rutinami prostředí PowerShell.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 04/19/2021
ms.author: memildin
ms.openlocfilehash: f9ab258f59279112d0b90c5d460e6761ac911a2d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713349"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatizace připojování Azure Security Center pomocí prostředí PowerShell

Své úlohy Azure můžete zabezpečit programově pomocí modulu Azure Security Center PowerShellu.
Použití PowerShellu vám umožňuje automatizovat úlohy a vyhnout se lidské chybě na základě ručních úkolů. To je užitečné hlavně v rozsáhlých nasazeních, která zahrnují desítky předplatných se stovkami a tisíci prostředků – všechny z nich musí být zabezpečené od začátku.

Připojování Azure Security Center pomocí prostředí PowerShell umožňuje programově automatizovat připojování a správu prostředků Azure a přidat nezbytné bezpečnostní prvky.

Tento článek poskytuje vzorový skript PowerShellu, který se dá upravit a použít ve vašem prostředí k uvedení Security Center v rámci předplatných. 

V tomto příkladu povolíme Security Center v předplatném s ID: d07c0080-170c-4c24-861d-9c817742786c a použijete Doporučené nastavení, které poskytuje vysokou úroveň ochrany, povolením Azure Defenderu, který poskytuje rozšířenou ochranu před internetovými útoky a možnosti detekce:

1. Povolte [Azure Defender](azure-defender.md). 
 
2. Nastavte pracovní prostor Log Analytics, do kterého bude agent Log Analytics odesílat data, která shromažďuje na virtuálních počítačích přidružených k předplatnému – v tomto příkladu je to stávající uživatelem definovaný pracovní prostor (myWorkspace).

3. Aktivujte Automatické zřizování agentů Security Center, které [nasadí agenta Log Analytics](security-center-enable-data-collection.md#auto-provision-mma).

5. Nastavte [ředitelka zabezpečení informací organizace jako kontakt zabezpečení pro Security Center výstrahy a významné události](security-center-provide-security-contact-details.md).

6. Přiřaďte [výchozí zásady zabezpečení](tutorial-security-policy.md)Security Center.

## <a name="prerequisites"></a>Požadavky

Tyto kroky je potřeba provést před spuštěním rutin Security Center:

1. Spusťte PowerShell jako správce.

1. V prostředí PowerShell spusťte následující příkazy:
      
    ```Set-ExecutionPolicy -ExecutionPolicy AllSigned```

    ```Install-Module -Name Az.Security -Force```

## <a name="onboard-security-center-using-powershell"></a>Připojení Security Center s využitím PowerShellu

1. Zaregistrujte své odběry do poskytovatele prostředků Security Center:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'```

1. Volitelné: Nastavte úroveň pokrytí (v/v programu Azure Defender) předplatných. Pokud není definován, Defender je vypnutý:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Set-AzSecurityPricing -Name "default" -PricingTier "Standard"```

1. Nakonfigurujte Log Analytics pracovní prostor, do kterého budou agenti hlásit. Musíte mít Log Analytics pracovní prostor, který jste už vytvořili, aby se virtuální počítače pro odběr nahlásily. Můžete definovat více předplatných pro sestavu do stejného pracovního prostoru. Pokud není definován, použije se výchozí pracovní prostor.

    ```Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"```

1. Automatické zřizování instalace agenta Log Analytics na virtuálních počítačích Azure:
    
    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```
    
    ```Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision```

    > [!NOTE]
    > Doporučuje se povolit Automatické zřizování, aby se zajistilo, že se virtuální počítače Azure automaticky chrání pomocí Azure Security Center.
    >

1. Volitelné: Důrazně doporučujeme, abyste [definovali informace o kontaktu zabezpečení](security-center-provide-security-contact-details.md) pro předplatná, která se budou používat jako příjemci výstrah a oznámení vygenerovaných Security Center:

    ```Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -AlertAdmin -NotifyOnAlert```

1. Přiřaďte výchozí iniciativu zásad Security Center:

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'```

    ```$Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Azure Security Benchmark'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'```

Úspěšně jste připojili Azure Security Center k PowerShellu.

Tyto rutiny PowerShellu teď můžete používat se skripty pro automatizaci k programové iteraci mezi předplatnými a prostředky. Tím ušetříte čas a snížíte pravděpodobnost lidské chyby. Tento [ukázkový skript](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) můžete použít jako referenci.




## <a name="see-also"></a>Viz také
Další informace o tom, jak můžete pomocí PowerShellu automatizovat připojování k Security Center, najdete v následujícím článku:

* [AZ. Security](/powershell/module/az.security)

Další informace o Security Center najdete v následujícím článku:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Přečtěte si, jak spravovat výstrahy zabezpečení a reagovat na ně.