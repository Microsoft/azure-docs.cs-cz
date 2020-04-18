---
title: 'Úvodní příručka: Vytvoření Azure WAF v2 v application gateway – šablona Správce prostředků'
titleSuffix: Azure Application Gateway
description: Přečtěte si, jak pomocí šablony Správce prostředků vytvořit bránu firewall webové aplikace v2 v bráně aplikace Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 04/02/2020
ms.author: victorh
ms.openlocfilehash: 6759071e73adfd3af4ac780da6db3a0e6e967ea1
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617985"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway---resource-manager-template"></a>Úvodní příručka: Vytvoření Azure WAF v2 v application gateway – šablona Správce prostředků

V tomto rychlém startu použijete šablonu Správce prostředků k vytvoření brány firewall webových aplikací Azure v2 v application gateway.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-web-application-firewall"></a>Vytvoření brány firewall webové aplikace

Tato šablona vytvoří jednoduchou webovou aplikační bránu firewall v2 v bráně aplikace Azure. To zahrnuje veřejnou ip front-endovou IP adresu, nastavení HTTP, pravidlo se základním naslouchací proces na portu 80 a back-endový fond. Zásada WAF s vlastním pravidlem je vytvořena k blokování provozu do back-endového fondu na základě typu shody IP adresy.

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-wafv2/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json" range="001-404" highlight="314-358":::

V šabloně je definováno více prostředků Azure:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses) : jeden pro aplikační bránu a dva pro virtuální počítače.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : dva virtuální počítače
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : dva pro virtuální počítače
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : konfigurace služby IIS a webových stránek

### <a name="deploy-the-template"></a>Nasazení šablony

Nasazení šablony Správce prostředků do Azure:

1. Vyberte **Nasazení do Azure,** abyste se přihlásili do Azure a otevřeli šablonu. Šablona vytvoří aplikační bránu, síťovou infrastrukturu a dva virtuální počítače v back-endovém fondu se službou IIS.

   [![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

2. Vyberte nebo vytvořte skupinu prostředků.
3. Vyberte **Souhlasím s výše uvedenými podmínkami** a pak vyberte **možnost Koupit**. Dokončení nasazení může trvat 10 minut nebo déle.

## <a name="validate-the-deployment"></a>Ověření nasazení

I když služba IIS není nutné k vytvoření aplikační brány, je nainstalována na serverech back-end, aby ověřila, jestli Azure úspěšně vytvořil WAF v2 na aplikační bráně. 

Pomocí služby IIS otestujte bránu aplikace:

1. Nastránce **Přehled** najděte veřejnou IP adresu aplikační brány. ![Zaznamenejte](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) veřejnou IP adresu aplikační brány Nebo můžete do vyhledávacího pole vybrat **všechny prostředky**, zadat *adresu myAGPublicIPAddress* a poté ji vybrat ve výsledcích hledání. Azure zobrazí veřejnou IP adresu na stránce **Přehled.**
2. Zkopírujte veřejnou IP adresu a vložte ji do adresního řádku prohlížeče a projděte tuto IP adresu.
3. Zkontrolujte odpověď. **A 403 Zakázaná** odpověď ověří, že WAF byl úspěšně vytvořen a blokuje připojení k back-endového fondu.
4. Změňte vlastní pravidlo na **Povolit provoz**.
  Spusťte následující skript Azure PowerShell, který nahradí název skupiny prostředků:
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

   Aktualizujte svůj prohlížeč vícekrát a měli byste vidět připojení k myVM1 a myVM2.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili pomocí aplikační brány, odstraňte skupinu prostředků. Tím odeberete bránu aplikace a všechny související prostředky.

Chcete-li odstranit skupinu `Remove-AzResourceGroup` prostředků, zavolejte rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Vytvoření aplikační brány s bránou webové aplikace pomocí portálu Azure](application-gateway-web-application-firewall-portal.md)