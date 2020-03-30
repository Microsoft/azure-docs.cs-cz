---
title: Úvodní příručka:Vytvoření profilu pro vysokou dostupnost aplikací – Azure PowerShell – Azure Traffic Manager
description: Tento článek rychlého startu popisuje, jak vytvořit profil Traffic Manager vytvořit vysoce dostupné webové aplikace.
services: traffic-manager
author: rohinkoul
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2019
ms.author: rohink
ms.openlocfilehash: 0ab7392b4fa6e248d51392706fedaed156344a99
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76934820"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Úvodní příručka: Vytvoření profilu Traffic Manageru pro vysoce dostupnou webovou aplikaci pomocí Azure PowerShellu

Tento rychlý start popisuje, jak vytvořit profil Traffic Manager, který poskytuje vysokou dostupnost pro webovou aplikaci.

V tomto rychlém startu vytvoříte dvě instance webové aplikace. Každý z nich běží v jiné oblasti Azure. Vytvoříte profil Traffic Manageru na základě [priority koncového bodu](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profil směruje uživatelský provoz na primární web se spuštěnou webovou aplikací. Traffic Manager průběžně monitoruje webovou aplikaci. Pokud primární lokalita není k dispozici, poskytuje automatické převzetí služeb při selhání pro záložní lokalitu.

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků pomocí [skupiny New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive


# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Vytvořte profil Traffic Manager pomocí [New-AzTrafficManagerProfile,](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) který řídí provoz uživatelů na základě priority koncového bodu.

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

## <a name="create-web-apps"></a>Vytvořit webové aplikace

Pro tento rychlý start budete potřebovat dvě instance webové aplikace nasazené ve dvou různých oblastech Azure *(Západní USA* a *Východní USA).* Každý bude sloužit jako primární a převzetí služeb při selhání koncové body pro Traffic Manager.

### <a name="create-web-app-service-plans"></a>Vytvoření plánů služby Web App Service
Vytvořte plány služby Web App pomocí [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) pro dvě instance webové aplikace, které budete nasadit ve dvou různých oblastech Azure.

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
### <a name="create-a-web-app-in-the-app-service-plan"></a>Vytvoření webové aplikace v plánu služby App Service
Vytvořte dvě instance webové aplikace pomocí [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) v plánech služby App Service v oblastech *Západní USA* a *Východní USA* Azure.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager
Přidejte dvě webové aplikace jako koncové body Traffic Manageru pomocí [new-aztrafficmanagerendpointu](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) do profilu Traffic Manageru následujícím způsobem:
- Přidejte webovou aplikaci umístěnou v oblasti *Azure v západní usa* jako primární koncový bod pro směrování veškerého provozu uživatelů. 
- Jako koncový bod pro převzetí služeb při selhání přidejte webovou aplikaci umístěnou v oblasti *East US* Azure. Pokud primární koncový bod není k dispozici, provoz automaticky směruje do koncového bodu převzetí služeb při selhání.

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

V této části zkontrolujete název domény vašeho profilu Traffic Manageru. Nakonfigurujete také primární koncový bod tak, aby nebyl k dispozici. Nakonec uvidíte, že webová aplikace je stále k dispozici. Je to proto, že Traffic Manager odesílá provoz do koncového bodu převzetí služeb při selhání.

### <a name="determine-the-dns-name"></a>Určení názvu DNS

Pomocí nástroje [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile)určete název DNS profilu traffic manageru .

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Zkopírujte hodnotu **RelativeDnsName.** Název DNS profilu traffic manageru je *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci
1. Ve webovém prohlížeči zadejte název DNS profilu Traffic Manageru (*http://<* relativednsname *>.trafficmanager.net)* a zobrazte výchozí web webové aplikace Web Appu.

    > [!NOTE]
    > V tomto scénáři rychlého startu všechny požadavky směrovat k primární koncový bod. Je nastavena na **prioritu 1**.
2. Chcete-li zobrazit převzetí služeb při selhání traffic manageru v akci, zakažte primární lokalitu pomocí [nástroje Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint).

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Zkopírujte název DNS profilu Traffic Manageru (*http://<* relativednsname *>.trafficmanager.net)* a prohlédněte si web v nové relaci webového prohlížeče.
4. Ověřte, zda je webová aplikace stále dostupná.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení odstraňte skupiny prostředků, webové aplikace a všechny související prostředky pomocí [skupiny Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili profil Traffic Manageru, který poskytuje vysokou dostupnost pro webovou aplikaci. Další informace o směrování provozu, pokračujte v kurzech Traffic Manager.

> [!div class="nextstepaction"]
> [Kurzy pro službu Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
