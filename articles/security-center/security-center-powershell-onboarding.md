---
title: Palubní řešení Azure Security Center s PowerShellem
description: Tento dokument vás provede procesem registrace Centra zabezpečení Azure pomocí rutin prostředí PowerShell.
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
ms.openlocfilehash: 5aaaf539c07a7ba2c2463d5bfd1f452853f52379
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603689"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatizace registrace Azure Security Center pomocí PowerShellu

Úlohy Azure můžete zabezpečit programově pomocí modulu PowerShell Centra zabezpečení Azure.
Použití prostředí PowerShell umožňuje automatizovat úlohy a vyhnout se lidské chybě vlastní ruční úlohy. To je užitečné zejména ve velkých nasazeních, která zahrnují desítky předplatných se stovkami a tisíci prostředků – které musí být zabezpečeny od začátku.

Registrace Azure Security Center pomocí PowerShellu umožňuje programově automatizovat registrace a správu vašich prostředků Azure a přidat potřebné ovládací prvky zabezpečení.

Tento článek obsahuje ukázkový skript prostředí PowerShell, který lze upravit a použít ve vašem prostředí k zavedení Centra zabezpečení v rámci vašich předplatných. 

V tomto příkladu povolíme Security Center na předplatné s ID: d07c0080-170c-4c24-861d-9c817742786c a použít doporučená nastavení, které poskytují vysokou úroveň ochrany, implementací standardní úrovně Security Center, která poskytuje pokročilé možnosti ochrany před hrozbami a detekce:

1. Nastavte [standardní úroveň ochrany centra zabezpečení](https://azure.microsoft.com/pricing/details/security-center/). 
 
2. Nastavte pracovní prostor Log Analytics, do kterého bude agent monitorování Microsoftu odesílat data, která shromažďuje na virtuálních počítačích přidružených k předplatnému – v tomto příkladu existující pracovní prostor definovaný uživatelem (myWorkspace).

3. Aktivujte automatické zřizování agentů centra zabezpečení, které [nasazuje agenta Microsoft Monitoring Agent](security-center-enable-data-collection.md#auto-provision-mma).

5. Nastavte ciso organizace [jako bezpečnostní kontakt pro výstrahy Centra zabezpečení a významné události](security-center-provide-security-contact-details.md).

6. Přiřaďte [výchozí zásady zabezpečení](tutorial-security-policy.md)Centra zabezpečení .

## <a name="prerequisites"></a>Požadavky

Před spuštěním rutin Centra zabezpečení je třeba provést tyto kroky:

1.  Spusťte PowerShell jako správce.
2.  Spusťte v PowerShellu následující příkazy:
      
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Install-Module -Name Az.Security -Force

## <a name="onboard-security-center-using-powershell"></a>Centrum zabezpečení na palubě pomocí PowerShellu

1.  Zaregistrujte svá předplatná u poskytovatele prostředků Centra zabezpečení:

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  Volitelné: Nastavte úroveň disponibility (cenovou úroveň) předplatných (Pokud není definována, cenová úroveň je nastavena na zdarma):

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzSecurityPricing -Name "default" -PricingTier "Standard"

3.  Nakonfigurujte pracovní prostor Analýzy protokolů, do kterého budou agenti vykazovat. Musíte mít pracovní prostor Analýzy protokolů, který jste už vytvořili, který bude virtuální chod předplatného vykazovat. Můžete definovat více odběrů pro vykazování do stejného pracovního prostoru. Pokud není definována, bude použit výchozí pracovní prostor.

        Set-AzSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  Instalace automatického zřizování agenta monitorování Microsoftu na virtuálních počítačích Azure:
    
        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > Doporučujeme povolit automatické zřizování a ujistěte se, že vaše virtuální počítače Azure jsou automaticky chráněné Azure Security Center.
    >

5.  Volitelné: Důrazně doporučujeme definovat kontaktní údaje zabezpečení pro předplatná, která jste na palubě, která budou použita jako příjemci výstrah a oznámení generovaných centrem zabezpečení:

        Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert 

6.  Přiřazení výchozí iniciativy zásad Centra zabezpečení:

        Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ '[Preview]: Enable Monitoring in Azure Security Center'}
        New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

Nyní jste úspěšně na palubě Azure Security Center s PowerShell!

Nyní můžete použít tyto rutiny prostředí PowerShell s automatizačními skripty k programové iterátu napříč předplatnými a prostředky. To šetří čas a snižuje pravděpodobnost lidské chyby. Tento [ukázkový skript](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) můžete použít jako odkaz.






## <a name="see-also"></a>Viz také
Další informace o tom, jak pomocí PowerShellu automatizovat přiregistraci do Centra zabezpečení, najdete v následujícím článku:

* [Az.Security](https://docs.microsoft.com/powershell/module/az.security).

Další informace o Centru zabezpečení naleznete v následujícím článku:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.