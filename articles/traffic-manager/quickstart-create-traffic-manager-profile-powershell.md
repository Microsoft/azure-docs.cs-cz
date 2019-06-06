---
title: Rychlý start – vytvoření profilu Traffic Manageru k zajištění vysoké dostupnosti aplikací pomocí Azure Powershellu
description: Tento článek Rychlý start popisuje, jak vytvořit profil Traffic Manageru k vytvoření vysoce dostupných webových aplikací.
services: traffic-manager
author: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2019
ms.author: kumud
ms.openlocfilehash: f6ed2e03352a335022d99cf703240552fa34e732
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729014"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Rychlý start: Vytvořit profil Traffic Manageru s vysokou dostupností webové aplikace s využitím Azure Powershellu

Tento rychlý start popisuje, jak vytvořit profil služby Traffic Manager, který poskytuje vysokou dostupnost pro vaši webovou aplikaci.

V tomto rychlém startu vytvoříte dvě instance webové aplikace. Každý z nich je spuštěna v jiné oblasti Azure. Vytvoříte profil Traffic Manageru na základě [Priorita koncového bodu](traffic-manager-routing-methods.md#priority). Profil, který bude směrovat uživatelský provoz do primární lokality spuštění webové aplikace. Traffic Manager neustále monitoruje webové aplikace. V případě, že primární lokalita není k dispozici, poskytuje automatické převzetí služeb při selhání zálohování lokality.

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Abyste vytvořili skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive


# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Vytvoření profilu Traffic Manageru pomocí [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) , který směruje provoz uživatelů na základě priority koncových bodů.

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

## <a name="create-web-apps"></a>Vytvoření webové aplikace

Pro účely tohoto rychlého startu budete potřebovat dvě instance webové aplikace nasazené ve dvou různých oblastech Azure (*USA – západ* a *USA – východ*). Každá bude sloužit jako primární server a převzetí služeb při selhání koncových bodů Traffic Manageru.

### <a name="create-web-app-service-plans"></a>Vytvoření plánů služby App Service Web
Vytvoření webové aplikace služby plány pomocí [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) pro dvě instance webové aplikace, kterou nasadíte ve dvou různých oblastech Azure.

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
Vytvořte dvě instance webové aplikace pomocí [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) ve službě App Service plány v *USA – západ* a *USA – východ* oblastí Azure.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager
Přidání dvou webových aplikací jako koncových bodů Traffic Manageru pomocí [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) profilu Traffic Manageru následujícím způsobem:
- Přidání webové aplikace v *USA – západ* oblasti Azure jako primární koncový bod ke směrování uživatelského provozu. 
- Přidání webové aplikace v *USA – východ* oblasti Azure jako koncový bod převzetí služeb při selhání. Primární koncový bod není k dispozici, provoz automaticky směruje do koncového bodu převzetí služeb při selhání.

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

V této části budete zkontrolovat název domény vašeho profilu Traffic Manageru. Pokud nakonfigurujete primární koncový bod do nedostupný. Nakonec získáte v tématu, že je webová aplikace stále k dispozici. Je to proto, že Traffic Manager odesílá provoz do koncového bodu převzetí služeb při selhání.

### <a name="determine-the-dns-name"></a>Určení názvu DNS

Určení názvu DNS na profil Traffic Manageru pomocí [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Kopírovat **RelativeDnsName** hodnotu. Je název DNS pro váš profil Traffic Manageru *http://<* relativednsname *>. trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci
1. Ve webovém prohlížeči, zadejte název DNS vašeho profilu Traffic Manageru (*http://<* relativednsname *>. trafficmanager.net*) Chcete-li zobrazit výchozího webu webové aplikace.

    > [!NOTE]
    > V tomto rychlém startu scénáři směrovat všechny požadavky na primární koncový bod. Je nastavený na **Priority 1**.
2. Chcete-li zobrazit Traffic Manageru převzetí služeb při selhání v akci, zakažte primární lokalitě pomocí [zakázat AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint).

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Zkopírujte název DNS vašeho profilu Traffic Manageru (*http://<* relativednsname *>. trafficmanager.net*) Chcete-li zobrazit na webu v nové relaci webového prohlížeče.
4. Ověřte, že je webová aplikace stále k dispozici.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi, odstranění skupiny prostředků, webové aplikace a všech souvisejících prostředků pomocí [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili profil služby Traffic Manager, který poskytuje vysokou dostupnost pro vaši webovou aplikaci. Další informace o směrování provozu, nadále v kurzech Traffic Manageru.

> [!div class="nextstepaction"]
> [Kurzy pro službu Traffic Manager](tutorial-traffic-manager-improve-website-response.md)