---
title: 'Rychlý Start: nastavení vysoké dostupnosti s využitím služby Azure front-Azure PowerShell'
description: V tomto rychlém startu se dozvíte, jak pomocí Azure PowerShell vytvořit vysokou dostupnost a vysoce výkonné globální webové aplikace pomocí služby Azure.
services: front-door
documentationcenter: na
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: 8ccc50dfc4c53f1ee207a04d7d7775775f13cb65
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91348902"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-powershell"></a>Rychlý Start: vytvoření přední dveře pro globální webovou aplikaci s vysokou dostupností pomocí Azure PowerShell

Začněte s předními dveřmi Azure pomocí Azure PowerShell k vytvoření vysoce dostupné a vysoce výkonné globální webové aplikace.

Přední dveře přesměrují webový provoz na konkrétní prostředky v back-end fondu. Definovali jste doménu front-end, přidáte prostředky do back-endu fondu a vytvoříte pravidlo směrování. Tento článek používá jednoduchou konfiguraci jednoho back-end fondu se dvěma prostředky webové aplikace a jedno pravidlo směrování s použitím výchozí cesty, která odpovídá "/*".

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell lokálně nainstalované nebo Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

V Azure přidělíte související prostředky skupině prostředků. Můžete použít buď existující skupinu prostředků, nebo vytvořit novou.

Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupFD -Location centralus
```

## <a name="create-two-instances-of-a-web-app"></a>Vytvoření dvou instancí webové aplikace

Tento rychlý Start vyžaduje dvě instance webové aplikace, která běží v různých oblastech Azure. Instance webové aplikace běží v režimu aktivní/aktivní, takže jedna z nich může přijímat přenosy. Tato konfigurace se liší od aktivní/úsporné konfigurace, kdy jedna funguje jako převzetí služeb při selhání.

Pokud ještě nemáte webovou aplikaci, použijte následující skript k nastavení dvou ukázkových webových aplikací.

```azurepowershell-interactive
# Create first web app in Central US region.
$webapp1 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location centralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanCentralUS

# Create second web app in South Central US region.
$webapp2 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location southcentralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanSouthCentralUS
```

## <a name="create-a-front-door"></a>Vytvoření služby Front Door

Tato část podrobně popisuje, jak můžete vytvořit a nakonfigurovat následující komponenty front-dveří:
    
* Front-end objekt obsahuje výchozí doménu front-dveří.
* Back-end fond je sada ekvivalentních back-endu, do kterých se zarovnává zatížení na základě požadavků klienta.
* Pravidlo směrování namapuje hostitele front-endu a shodný vzor cesty URL na konkrétní back-end fond.

### <a name="create-a-frontend-object"></a>Vytvoření objektu front-endu

Objekt front-end nakonfiguruje název hostitele pro přední dveře. Ve výchozím nastavení bude mít název hostitele příponu **. azurefd.NET*.

```azurepowershell-interactive
# Create a unique name
$fdname = "contoso-frontend-$(Get-Random)"

#Create the frontend object
$FrontendEndObject = New-AzFrontDoorFrontendEndpointObject `
-Name "frontendEndpoint1" `
-HostName $fdname".azurefd.net"
```

### <a name="create-the-backend-pool"></a>Vytvoření back-endového fondu

Back-end fond je tvořen dvěma webovými aplikacemi vytvořenými na začátku tohoto rychlého startu. Nastavení sondy stavu a vyrovnávání zatížení definované v tomto kroku používá výchozí hodnoty.

```azurepowershell-interactive
# Create backend objects that points to the hostname of the web apps
$backendObject1 = New-AzFrontDoorBackendObject `
-Address $webapp1.DefaultHostName
$backendObject2 = New-AzFrontDoorBackendObject `
-Address $webapp2.DefaultHostName

# Create a health probe object
$HealthProbeObject = New-AzFrontDoorHealthProbeSettingObject `
-Name "HealthProbeSetting"

# Create the load balancing setting object
$LoadBalancingSettingObject = New-AzFrontDoorLoadBalancingSettingObject `
-Name "Loadbalancingsetting" `
-SampleSize "4" `
-SuccessfulSamplesRequired "2" `
-AdditionalLatencyInMilliseconds "0"

# Create a backend pool using the backend objects, health probe, and load balancing settings
$BackendPoolObject = New-AzFrontDoorBackendPoolObject `
-Name "myBackendPool" `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-Backend $backendObject1,$backendObject2 `
-HealthProbeSettingsName "HealthProbeSetting" `
-LoadBalancingSettingsName "Loadbalancingsetting"
```

### <a name="create-a-routing-rule"></a>Přidejte pravidlo směrování

Pravidlo směrování namapuje back-end fond do domény front-end a nastaví výchozí cestu, která odpovídá hodnotě "/*".

```azurepowershell-interactive
# Create a routing rule mapping the frontend host to the backend pool
$RoutingRuleObject = New-AzFrontDoorRoutingRuleObject `
-Name LocationRule `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-FrontendEndpointName "frontendEndpoint1" `
-BackendPoolName "myBackendPool" `
-PatternToMatch "/*"
```
### <a name="create-the-front-door"></a>Vytvoření front-dveří

Teď, když jste vytvořili potřebné objekty, vytvořte si přední dveře:

```azurepowershell-interactive
# Creates the Front Door
New-AzFrontDoor `
-Name $fdname `
-ResourceGroupName myResourceGroupFD `
-RoutingRule $RoutingRuleObject `
-BackendPool $BackendPoolObject `
-FrontendEndpoint $FrontendEndObject `
-LoadBalancingSetting $LoadBalancingSettingObject `
-HealthProbeSetting $HealthProbeObject
```

Po úspěšném nasazení ho můžete otestovat podle kroků v následující části.

## <a name="test-the-front-door"></a>Test předních dveří

Spusťte následující příkazy, abyste získali název hostitele pro přední dveře.

```azurepowershell-interactive
# Gets Front Door in resource group and output the hostname of the frontend domain.
$fd = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD
$fd.FrontendEndpoints[0].Hostname
```

Otevřete webový prohlížeč a zadejte název hostitele, který se získá z příkazů. Přední dveře budou směrovat váš požadavek na jeden ze zdrojů back-endu. 

:::image type="content" source="./media/quickstart-create-front-door-powershell/front-door-test-page.png" alt-text="Zkušební stránka pro přední dvířka":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete prostředky, které jste vytvořili přes dvířka, odstraňte skupinu prostředků. Když odstraníte skupinu prostředků, odstraníte také přední dveře a všechny související prostředky. 

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupFD
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili:
* Front Door
* Dvě webové aplikace

Další informace o tom, jak přidat vlastní doménu do předních dveří, najdete v výukových kurzech pro přední dveře.

> [!div class="nextstepaction"]
> [Přidání vlastní domény](front-door-custom-domain.md)
