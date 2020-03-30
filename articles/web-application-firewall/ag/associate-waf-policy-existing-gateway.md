---
title: Přidružení zásad brány firewall webových aplikací k existující bráně aplikace Azure
description: Zjistěte, jak přidružit zásady brány firewall webových aplikací k existující bráně aplikací Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083910"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>Přidružení zásad waf k existující aplikační bráně

Azure PowerShell můžete použít k [vytvoření zásad WAF](create-waf-policy-ag.md), ale už možná máte aplikační bránu a chcete k ní přidružit zásady WAF. V tomto článku uděláte právě to; vytvoříte zásadu WAF a přidružíte ji k již existující bráně aplikace. 

1. Získejte zásady brány aplikace a brány firewall. Pokud nemáte existující zásady brány firewall, přečtěte si krok 2. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. (Nepovinné) Vytvořte zásady brány firewall.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Pokud vytváříte tuto zásadu WAF pro přechod z konfigurace WAF na zásadu WAF, musí být zásada přesnou kopií staré konfigurace. To znamená, že každé vyloučení, vlastní pravidlo, zakázané pravidlo skupiny, atd.
3. (Nepovinné) Zásady WAF můžete nakonfigurovat tak, aby vyhovovaly vašim potřebám. To zahrnuje vlastní pravidla, zakázání pravidel / skupin pravidel, vyloučení, nastavení limitů pro odesílání souborů atd. Pokud tento krok přeskočíte, budou vybrány všechny výchozí hodnoty. 
   
4. Uložte zásadu a připojte ji k bráně aplikace. 
   
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
