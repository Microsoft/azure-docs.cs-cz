---
title: Přidružení zásady firewallu webových aplikací k existujícímu Application Gateway Azure
description: Přečtěte si, jak přidružit zásady firewallu webových aplikací k existujícímu Application Gateway Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "74083910"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>Přidružení zásady WAF k existujícímu Application Gateway

Azure PowerShell můžete použít k [Vytvoření zásady WAF](create-waf-policy-ag.md), ale možná už máte Application Gateway a chcete k ní přidružit jenom zásadu WAF. V tomto článku jste to právě provedete; vytvoříte zásadu WAF a přidružíte ji k již existujícímu Application Gateway. 

1. Získejte Application Gateway a zásady brány firewall. Pokud nemáte existující zásady brány firewall, přečtěte si krok 2. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. Volitelné Vytvořte zásady brány firewall.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Pokud vytváříte tuto zásadu WAF k přechodu z WAF konfigurace na zásadu WAF, pak musí být tato zásada přesnou kopii staré konfigurace. To znamená, že každé vyloučení, vlastní pravidlo, skupina zakázaných pravidel atd. musí být přesně stejné, jako je v konfiguraci WAF.
3. Volitelné Zásady WAF můžete nakonfigurovat tak, aby vyhovovaly vašim potřebám. Patří sem vlastní pravidla, zákaz skupin pravidel, vyloučení, nastavení limitů nahrávání souborů atd. Pokud tento krok přeskočíte, budou vybrány všechny výchozí hodnoty. 
   
4. Uložte zásadu a připojte ji k vašemu Application Gateway. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>Další kroky
[Přečtěte si o vlastních pravidlech.](configure-waf-custom-rules.md)
