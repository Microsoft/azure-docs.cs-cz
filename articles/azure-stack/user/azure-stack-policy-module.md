---
title: Použití modulu zásad služby Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak omezit předplatné Azure, se chovají jako předplatnému Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: mabrigg
ms.openlocfilehash: 105991296629e04addab33a0611736b379b11688
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281860"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Správa Azure policy použití modulu zásad Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Modul zásad Azure Stack umožňuje konfigurovat předplatné Azure s stejné správy verzí a dostupnost služeb jako Azure Stack.  Modul používá **New-AzureRMPolicyAssignment** rutina pro vytvoření zásady služby Azure, který omezuje typy prostředků a služeb dostupných v předplatném.  Po konfiguraci zásad, můžete použít své předplatné Azure pro vývoj aplikací určená pro Azure Stack.

## <a name="install-the-module"></a>Instalace modulu

1. Nainstalujte požadovanou verzi modul AzureRM Powershellu, jak je popsáno v krok 1 z [instalace Powershellu pro Azure Stack](azure-stack-powershell-install.md).
2. [Stáhněte si nástroje Azure Stack z Githubu](azure-stack-powershell-download.md)
3. [Konfigurace PowerShellu pro použití s Azure Stackem](azure-stack-powershell-configure-user.md)

4. Importujte modul AzureStack.Policy.psm1:

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Použití zásad k předplatnému Azure

Použít výchozí zásady služby Azure Stack ve vašem předplatném Azure můžete použít následující příkaz. Před spuštěním tohoto příkazu nahraďte *název předplatného Azure* ve vašem předplatném Azure.

```PowerShell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>Použít zásady skupiny prostředků

Můžete chtít použít zásady, které jsou přesnější. Například můžete mít další prostředky spuštěné ve stejném předplatném. Použití zásad pro konkrétní skupině prostředků, které umožňuje testování aplikací pro Azure Stack pomocí prostředků Azure můžete omezit rozsah. Před spuštěním následujícího příkazu, nahraďte *název předplatného Azure* názvem vašeho předplatného Azure.

```PowerShell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>Zásady v akci

Po nasazení zásady Azure, se zobrazí chybová zpráva při pokusu o nasazení prostředku, který zakázané zásadami.

![Výsledek nasazení prostředků se nezdařilo z důvodu omezení zásad](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Další postup

* [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
* [Nasazení šablon pomocí Azure CLI](azure-stack-deploy-template-command-line.md)
* [Nasazení šablon pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
