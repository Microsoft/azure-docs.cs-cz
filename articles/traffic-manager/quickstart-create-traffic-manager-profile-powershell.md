---
title: 'Rychlý Start: vytvoření profilu pro vysokou dostupnost aplikací – Azure PowerShell – Azure Traffic Manager'
description: Tento článek rychlý Start popisuje, jak vytvořit profil Traffic Manager pro sestavení webové aplikace s vysokou dostupností.
services: traffic-manager
author: duongau
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: duau
ms.openlocfilehash: 9b34a17cc9add0bed4bffb7677aa81bb17f3125b
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631558"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Rychlý Start: vytvoření profilu Traffic Manager pro webovou aplikaci s vysokou dostupností pomocí Azure PowerShell

V tomto rychlém startu se dozvíte, jak vytvořit profil Traffic Manager, který poskytuje vysokou dostupnost vaší webové aplikace.

V tomto rychlém startu vytvoříte dvě instance webové aplikace. Každý z nich je spuštěný v jiné oblasti Azure. Vytvoříte profil Traffic Manager na základě [priority koncového bodu](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profil směruje uživatelský provoz do primární lokality, na které běží webová aplikace. Traffic Manager nepřetržitě monitoruje webovou aplikaci. Pokud primární lokalita není k dispozici, poskytuje automatické převzetí služeb při selhání pro záložní lokalitu.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte prostředí PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive

# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Vytvořte profil Traffic Manager pomocí [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) , který směruje provoz uživatele na základě priority koncového bodu.

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>Vytvořit Web Apps

Pro účely tohoto rychlého startu budete potřebovat dvě instance webové aplikace nasazené ve dvou různých oblastech Azure (*západní USA* a *východní USA*). Každý bude sloužit jako primární koncová body a koncové body převzetí služeb při selhání pro Traffic Manager

### <a name="create-web-app-service-plans"></a>Vytváření plánů Web App Service
Pomocí [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) můžete vytvářet plány služby Web App Service pro dvě instance webové aplikace, které nasadíte ve dvou různých oblastech Azure.

```azurepowershell-interactive

# Variables
$App1Name="AppServiceTM1$Random"
$App2Name="AppServiceTM2$Random"
$Location1="WestUS"
$Location2="EastUS"

# Create an App service plan
New-AzAppservicePlan -Name "$App1Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "$App2Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>Vytvoření webové aplikace v plánu App Service
Vytvořte dvě instance webové aplikace pomocí [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) v plánech App Service ve *západní USA* a *východní USA* oblastech Azure.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager
Přidejte tyto dva Web Apps jako Traffic Manager koncových bodů pomocí příkazu [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) do profilu Traffic Manager následujícím způsobem:
- Přidejte webovou aplikaci umístěnou v *západní USA* oblasti Azure jako primární koncový bod pro směrování všech uživatelských přenosů. 
- Přidejte webovou aplikaci umístěnou v *východní USA* oblasti Azure jako koncový bod převzetí služeb při selhání. Když je primární koncový bod nedostupný, provoz se automaticky směruje na koncový bod převzetí služeb při selhání.

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "$App1Name-$Location1" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "$App2Name-$Location2" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>Test profilu služby Traffic Manager

V této části zkontrolujete název domény vašeho profilu Traffic Manager. Také nakonfigurujete primární koncový bod jako nedostupný. Nakonec se zobrazí, že je webová aplikace stále k dispozici. Je to proto, že Traffic Manager odesílá provoz do koncového bodu převzetí služeb při selhání.

### <a name="determine-the-dns-name"></a>Určení názvu DNS

Pomocí [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile)určete název DNS profilu Traffic Manager.

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Zkopírujte hodnotu **RelativeDnsName** . Název DNS vašeho profilu Traffic Manager je *http://<* relativednsname *>. trafficmanager.NET*. 

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci
1. Ve webovém prohlížeči zadejte název DNS profilu Traffic Manager (*http://<* relativednsname *>. trafficmanager.NET*), abyste si mohli zobrazit výchozí web vaší webové aplikace.

    > [!NOTE]
    > V tomto scénáři rychlého startu všechny požadavky směrují do primárního koncového bodu. Je nastavená na **priority 1**.
2. Pokud chcete zobrazit Traffic Manager převzetí služeb při selhání v akci, zakažte primární lokalitu pomocí [Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint).

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Zkopírujte název DNS profilu Traffic Manager (*http://<* relativednsname *>. trafficmanager.NET*), abyste si web mohli zobrazit v nové relaci webového prohlížeče.
4. Ověřte, zda je webová aplikace stále k dispozici.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi, odstraňte skupiny prostředků, webové aplikace a všechny související prostředky pomocí [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili profil Traffic Manager, který poskytuje vysokou dostupnost vaší webové aplikace. Pokud se chcete dozvědět víc o provozu směrování, přejděte k Traffic Manager kurzům.

> [!div class="nextstepaction"]
> [Kurzy pro službu Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
