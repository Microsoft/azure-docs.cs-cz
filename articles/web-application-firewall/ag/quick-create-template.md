---
title: 'Rychlý Start: vytvoření služby Azure WAF V2 v šabloně Application Gateway-Azure Resource Manager'
titleSuffix: Azure Application Gateway
description: Naučte se používat šablonu pro rychlý Start Azure Resource Manager (šablonu ARM) k vytvoření služby Firewall webových aplikací V2 v Azure Application Gateway.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 09/16/2020
ms.author: victorh
ms.openlocfilehash: 036503f95a7dc1aaa5690bacc01d07034e6d162f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91266932"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway-using-an-arm-template"></a>Rychlý Start: vytvoření služby Azure WAF v2 na Application Gateway pomocí šablony ARM

V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k vytvoření brány firewall webových aplikací Azure V2 v Application Gateway.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Kontrola šablony

Tato šablona vytvoří jednoduchou službu Firewall webových aplikací V2 v Azure Application Gateway. To zahrnuje IP adresu front-endu s veřejnou IP adresou, nastavení HTTP, pravidlo se základním naslouchacím procesem na portu 80 a back-endového fondu. Vytvoří se zásada WAF s vlastním pravidlem pro blokování provozu do back-endu fondu na základě typu shody IP adresy.

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/ag-docs-wafv2/).

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json":::

V šabloně je definováno víc prostředků Azure:

- [**Microsoft. Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses) : jedna pro aplikační bránu a dvě pro virtuální počítače.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : dva virtuální počítače
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : dvě pro virtuální počítače
- [**Microsoft. COMPUTE/VirtualMachine/Extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : Konfigurace služby IIS a webových stránek

## <a name="deploy-the-template"></a>Nasazení šablony

Nasazení šablony ARM do Azure:

1. Vyberte **nasadit do Azure** a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří Aplikační bránu, síťovou infrastrukturu a dva virtuální počítače ve fondu back-end se službou IIS.

   [![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

2. Vyberte nebo vytvořte skupinu prostředků.
3. Vyberte Souhlasím **s podmínkami a ujednáními uvedenými nahoře** a pak vyberte **koupit**. Dokončení nasazení může trvat 10 minut nebo i déle.

## <a name="validate-the-deployment"></a>Ověření nasazení

I když služba IIS není nutná k vytvoření aplikační brány, nainstaluje se na servery back-end a ověří, jestli Azure na aplikační bráně úspěšně vytvořil WAF v2.

Použijte službu IIS k otestování služby Application Gateway:

1. Na stránce s **přehledem** vyhledejte veřejnou IP adresu pro aplikační bránu. ![ Zaznamenejte si veřejnou IP adresu aplikační brány ](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) nebo vyberte **všechny prostředky**, do vyhledávacího pole zadejte *myAGPublicIPAddress* a potom ho vyberte ve výsledcích hledání. Azure zobrazí veřejnou IP adresu na stránce **Přehled** .
2. Zkopírujte veřejnou IP adresu a vložte ji do adresního řádku prohlížeče, abyste mohli procházet tuto IP adresu.
3. Ověřte odpověď. **403 zakázaná** odpověď ověřuje, že se WAF úspěšně vytvořil a blokuje připojení k back-end fondu.
4. Změňte vlastní pravidlo tak, aby **umožňovalo provoz**.
  Spusťte následující skript Azure PowerShell a nahraďte název skupiny prostředků:
   ```azurepowershell
   $rg = "<your resource group name>"
   $AppGW = Get-AzApplicationGateway -Name myAppGateway -ResourceGroupName $rg
   $pol = Get-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg
   $pol[0].customrules[0].action = "allow"
   $rule = $pol.CustomRules
   Set-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg -CustomRule $rule
   $AppGW.FirewallPolicy = $pol
   Set-AzApplicationGateway -ApplicationGateway $AppGW
   ```

   Aktualizujte si prohlížeč několikrát a měli byste vidět připojení k myVM1 i myVM2.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete prostředky, které jste vytvořili v rámci služby Application Gateway, odstraňte skupinu prostředků. Tím odeberete Aplikační bránu a všechny související prostředky.

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: vytvoření aplikační brány pomocí brány firewall webových aplikací pomocí Azure Portal](application-gateway-web-application-firewall-portal.md)