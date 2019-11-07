---
title: Připojení k Azure Security Center pomocí PowerShellu
description: Tento dokument vás provede procesem připojování Azure Security Center pomocí rutin PowerShellu.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2018
ms.author: memildin
ms.openlocfilehash: b20b3c1e4216fe8065fbc8ac24c7d8097903fc5a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686363"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatizace připojování Azure Security Center pomocí prostředí PowerShell

Své úlohy Azure můžete zabezpečit programově pomocí modulu Azure Security Center PowerShellu.
Použití PowerShellu vám umožňuje automatizovat úlohy a vyhnout se lidské chybě na základě ručních úkolů. To je užitečné hlavně v rozsáhlých nasazeních, která zahrnují desítky předplatných se stovkami a tisíci prostředků – všechny z nich musí být zabezpečené od začátku.

Připojování Azure Security Center pomocí prostředí PowerShell umožňuje programově automatizovat připojování a správu prostředků Azure a přidat nezbytné bezpečnostní prvky.

Tento článek poskytuje vzorový skript PowerShellu, který se dá upravit a použít ve vašem prostředí k uvedení Security Center v rámci předplatných. 

V tomto příkladu povolíme Security Center v předplatném s ID: d07c0080-170c-4c24-861d-9c817742786c a použijete Doporučené nastavení, které poskytuje vysokou úroveň ochrany, implementací úrovně Standard Security Center, která poskytuje Rozšířené možnosti ochrany před internetovými útoky a detekce:

1. Nastavte [úroveň ochrany Security Center úrovně Standard](https://azure.microsoft.com/pricing/details/security-center/). 
 
2. Nastavte pracovní prostor Log Analytics, do kterého Microsoft Monitoring Agent odešle data shromážděná na virtuálních počítačích přidružených k předplatnému – v tomto příkladu je to stávající uživatelem definovaný pracovní prostor (myWorkspace).

3. Aktivujte Automatické zřizování agentů Security Center, které [nasadí Microsoft Monitoring Agent](security-center-enable-data-collection.md#auto-provision-mma).

5. Nastavte [ředitelka zabezpečení informací organizace jako kontakt zabezpečení pro Security Center výstrahy a významné události](security-center-provide-security-contact-details.md).

6. Přiřaďte [výchozí zásady zabezpečení](tutorial-security-policy.md)Security Center.

## <a name="prerequisites"></a>Požadavky

Tyto kroky je potřeba provést před spuštěním rutin Security Center:

1.  Spusťte PowerShell jako správce.
2.  V prostředí PowerShell spusťte následující příkazy:
      
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Install-Module -Name Az.Security -Force

## <a name="onboard-security-center-using-powershell"></a>Připojení Security Center s využitím PowerShellu

1.  Zaregistrujte své odběry do poskytovatele prostředků Security Center:

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  Volitelné: Nastavte úroveň pokrytí (cenová úroveň) předplatných (Pokud není definována, cenová úroveň je nastavená na volná):

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzSecurityPricing -Name "default" -PricingTier "Standard"

3.  Nakonfigurujte Log Analytics pracovní prostor, do kterého budou agenti hlásit. Musíte mít Log Analytics pracovní prostor, který jste už vytvořili, aby se virtuální počítače pro odběr nahlásily. Můžete definovat více předplatných pro sestavu do stejného pracovního prostoru. Pokud není definován, použije se výchozí pracovní prostor.

        Set-AzSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  Automatické zřizování instalace Microsoft Monitoring Agent na virtuálních počítačích Azure:
    
        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > Doporučuje se povolit Automatické zřizování, aby se zajistilo, že se virtuální počítače Azure automaticky chrání pomocí Azure Security Center.
    >

5.  Volitelné: Důrazně doporučujeme, abyste definovali informace o kontaktu zabezpečení pro předplatná, která se budou používat jako příjemci výstrah a oznámení vygenerovaných Security Center:

        Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert 

6.  Přiřaďte výchozí iniciativu zásad Security Center:

        Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ '[Preview]: Enable Monitoring in Azure Security Center'}
        New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

V prostředí PowerShell jste teď úspěšně připojili Azure Security Center.

Tyto rutiny PowerShellu teď můžete používat se skripty pro automatizaci k programové iteraci mezi předplatnými a prostředky. Tím ušetříte čas a snížíte pravděpodobnost lidské chyby. Tento [ukázkový skript](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) můžete použít jako referenci.






## <a name="see-also"></a>Viz také
Další informace o tom, jak můžete pomocí PowerShellu automatizovat připojování k Security Center, najdete v následujícím článku:

* [AZ. Security](https://docs.microsoft.com/powershell/module/az.security).

Další informace o Security Center najdete v následujícím článku:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
