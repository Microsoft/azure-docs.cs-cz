---
title: Použití Powershellu ke správě Traffic Manageru v Azure | Dokumentace Microsoftu
description: Pomocí prostředí PowerShell pro Traffic Manager pomocí Azure Resource Manageru
services: traffic-manager
documentationcenter: na
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: 93d52101569e911c90377f26a9773d61eeaaf229
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653675"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Použití Powershellu ke správě Traffic Manageru

Azure Resource Manager je upřednostňovaný správy rozhraní pro služby v Azure. Profilů Azure Traffic Manageru můžete spravovat pomocí nástroje a rozhraní API založená na Azure Resource Manageru.

## <a name="resource-model"></a>Model prostředků

Azure Traffic Manager je nakonfigurován pomocí kolekce nastavení nazývaný jako profil Traffic Manageru. Tento profil obsahuje nastavení DNS, nastavení směrování provozu, nastavení monitorování koncového bodu a seznam koncových bodů služby, ke kterým se směruje provoz.

Každý profil služby Traffic Manager je reprezentován prostředek typu "TrafficManagerProfiles". Na úrovni rozhraní REST API je identifikátor URI pro každý profil:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Zřízení prostředí Azure PowerShell

Tyto pokyny použijte prostředí Azure PowerShell. V následujícím článku vysvětluje, jak nainstalovat a nakonfigurovat Azure PowerShell.

* [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview)

V příkladech v tomto článku se předpokládá, že máte existující skupinu prostředků. Můžete vytvořit skupinu prostředků pomocí následujícího příkazu:

```powershell
New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manageru vyžaduje, že všechny skupiny prostředků umístění. Toto umístění se používá jako výchozí pro prostředky vytvořené v této skupině prostředků. Ale protože prostředky profilu Traffic Manageru jsou globální, a ne místní, volba umístění skupiny prostředků nemá žádný vliv na Azure Traffic Manageru.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Chcete-li vytvořit profil služby Traffic Manager, použijte `New-AzureRmTrafficManagerProfile` rutiny:

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Následující tabulka popisuje parametry:

| Parametr | Popis |
| --- | --- |
| Název |Název prostředku pro prostředek profilu Traffic Manageru. Profily ve stejné skupině prostředků musí mít jedinečné názvy. Tento název se liší od názvu DNS pro dotazy DNS. |
| ResourceGroupName |Název skupiny prostředků obsahující profil prostředek. |
| Trafficroutingmethod funkce |Určuje metodu směrování provozu umožňuje určit, který koncový bod je vrácený v odpovědi dotazu DNS. Možné hodnoty jsou "Výkonu", "Vážená" nebo "Priority". |
| RelativeDnsName |Určuje název hostitele část názvu DNS, který poskytuje tento profil služby Traffic Manager. Tato hodnota je v kombinaci s názvem domény DNS, použitá pro zformování plně kvalifikovaný název domény (FQDN) profilu Azure Traffic Manager. Například nastavení hodnoty "contoso" stane "contoso.trafficmanager.net." |
| TTL |DNS Time-to-Live (TTL), určuje v sekundách. Tato hodnota TTL informuje překladače místního DNS a klienty DNS, jak dlouho se má mezipaměti odpovědí DNS pro tento profil služby Traffic Manager. |
| MonitorProtocol |Určuje protokol, který slouží k monitorování stavu koncového bodu. Možné hodnoty jsou 'HTTP' a 'HTTPS'. |
| MonitorPort |Určuje port TCP používá k monitorování koncových bodů. |
| MonitorPath |Určuje cestu relativní vzhledem k názvu domény koncový bod pro sběr dat pro koncový bod stavu. |

Rutina vytvoří profil služby Traffic Manager v Azure a vrátí odpovídající objekt profilu do prostředí PowerShell. V tomto okamžiku profil neobsahuje žádné koncové body. Další informace o přidání koncových bodů do profilu Traffic Manageru najdete v tématu [přidat koncové body Traffic Manageru](#adding-traffic-manager-endpoints).

## <a name="get-a-traffic-manager-profile"></a>Získat profil Traffic Manageru

Pokud chcete načíst existující objekt profilu Traffic Manageru, použijte `Get-AzureRmTrafficManagerProfle` rutiny:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Tato rutina vrátí objekt profilu Traffic Manageru.

## <a name="update-a-traffic-manager-profile"></a>Aktualizovat profil Traffic Manageru

Úprava profily Traffic Manageru následující 3 kroků:

1. Načíst profil pomocí `Get-AzureRmTrafficManagerProfile` nebo použít profil vrácený `New-AzureRmTrafficManagerProfile`.
2. Změna profilu. Můžete přidat a odebrat koncové body nebo změňte parametry koncového bodu nebo profilu. Tyto změny jsou offline operace. Měníte pouze místní objekt v paměti, která představuje profilu.
3. Potvrzení provedených změn `Set-AzureRmTrafficManagerProfile` rutiny.

S výjimkou RelativeDnsName profil, který je možné změnit všechny vlastnosti profilu. Chcete-li změnit RelativeDnsName, je nutné odstranit profil a nový profil s novým názvem.

Následující příklad ukazuje, jak změnit hodnoty TTL profilu:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$profile.Ttl = 300
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Existují tři typy koncových bodů Traffic Manageru:

1. **Koncové body Azure** jsou služby hostované v Azure
2. **Externí koncové body** jsou služby hostované mimo Azure
3. **Vnořené koncové body** se používají k vytvoření hierarchií vnořené profily Traffic Manageru. Vnořené koncové body povolit pokročilé konfigurace směrování provozu pro komplexní aplikace.

Ve všech třech případech můžete koncové body přidat dvěma způsoby:

1. Pomocí kroku 3 procesu je popsáno výše. Výhodou této metody je, že lze provést několik změn koncový bod v jedné aktualizace.
2. Pomocí rutiny New-AzureRmTrafficManagerEndpoint. Tato rutina přidá koncový bod do existujícího profilu Traffic Manageru v rámci jedné operace.

## <a name="adding-azure-endpoints"></a>Přidání koncových bodů Azure

Koncové body Azure odkazovat služeb hostovaných v Azure. Podporuje dva typy koncových bodů Azure:

1. Azure App Service
2. Azure prostředků PublicIpAddress (které můžou být připojené k vyrovnávání zatížení nebo virtuální počítač síťovou kartu). PublicIpAddress musí mít název DNS přiřadit má být použit v Traffic Manageru.

V každém případě:

* Služba je určena pomocí parametru "targetResourceId" `Add-AzureRmTrafficManagerEndpointConfig` nebo `New-AzureRmTrafficManagerEndpoint`.
* Parametrem TargetResourceId jsou implicitní 'Target' a "EndpointLocation".
* Zadání "váhu' je volitelný. Váhy používají pouze pokud profil, který je konfigurován pro použití metody směrování provozu "Vážená". V opačném případě se ignorují. Je-li zadána, hodnota musí být číslo v rozsahu od 1 do 1000. Výchozí hodnota je '1'.
* Zadání "Priority" je volitelný. Priority se používají pouze pokud je profil nakonfigurovaný na použití metody směrování provozu "Priority". V opačném případě se ignorují. Platné hodnoty jsou od 1 do 1000 s nižšími hodnotami označující s vyšší prioritou. Pokud zadaný pro jeden koncový bod, musí být zadán pro všechny koncové body. Pokud tento parametr vynechán, výchozí hodnotami s počáteční hodnotou '1' jsou použita popořadě, že jsou uvedené koncové body.

### <a name="example-1-adding-app-service-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>Příklad 1: Přidání koncových bodů služby App Service pomocí `Add-AzureRmTrafficManagerEndpointConfig`

V tomto příkladu jsme vytvořit profil Traffic Manageru a přidejte dva koncové body služby App Service pomocí `Add-AzureRmTrafficManagerEndpointConfig` rutiny.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzureRMWebApp -Name webapp1
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzureRMWebApp -Name webapp2
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Příklad 2: Přidání koncového bodu pomocí publicIpAddress `New-AzureRmTrafficManagerEndpoint`

Prostředek veřejné IP adresy se v tomto příkladu přidá do profilu služby Traffic Manager. Veřejnou IP adresu musí mít název DNS nakonfigurovaný a může být vázána na síťový adaptér virtuálního počítače nebo ke službě Vyrovnávání zatížení.

```powershell
$ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Přidání externí koncové body

Externí koncové body Traffic Manageru používá pro směrování provozu do služby hostované mimo Azure. S koncovými body Azure, externích koncových bodů můžete přidat buď pomocí `Add-AzureRmTrafficManagerEndpointConfig` následovaný `Set-AzureRmTrafficManagerProfile`, nebo `New-AzureRMTrafficManagerEndpoint`.

Při zadání externí koncové body:

* Musí být zadán název domény koncový bod pomocí parametru 'Target'
* Pokud se používá metodu směrování provozu "Výkonu", 'EndpointLocation' je vyžadována. V opačném případě je volitelný. Hodnota musí být [název platný oblasti Azure](https://azure.microsoft.com/regions/).
* "Váhy" a "Priority" jsou volitelné.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Příklad 1: Přidání externí koncové body pomocí `Add-AzureRmTrafficManagerEndpointConfig` a `Set-AzureRmTrafficManagerProfile`

V tomto příkladu jsme vytvořit profil služby Traffic Manager, přidejte dvě externí koncové body a potvrďte změny.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Příklad 2: Přidání externí koncové body pomocí `New-AzureRmTrafficManagerEndpoint`

V tomto příkladu přidáme externí koncový bod do existujícího profilu. Profil, který je určen pomocí názvů skupin profilu a prostředků.

```powershell
New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Přidání koncových bodů "Vnořené"

Každý profil služby Traffic Manager určuje jedinou metodu směrování provozu. Existují ale scénáře, které vyžadují složitější směrování provozu, než směrování poskytuje jeden profil Traffic Manageru. Je možné vnořovat profily Traffic Manageru zkombinovat výhod více než jednu metodu směrování provozu. Vnořené profily umožňují potlačit výchozí chování Traffic Managerem pro podporu větší a složitější nasazení aplikací. Podrobnější příklady najdete v článku [profily Traffic Manageru vnořené](traffic-manager-nested-profiles.md).

Vnořené koncových bodů jsou nakonfigurované v nadřazené profilu, s použitím typu určitého koncového bodu "NestedEndpoints". Při zadávání vnořených koncových bodů:

* Koncový bod musí být zadán pomocí parametru 'targetResourceId.
* Pokud se používá metodu směrování provozu "Výkonu", 'EndpointLocation' je vyžadována. V opačném případě je volitelný. Hodnota musí být [název platný oblasti Azure](https://azure.microsoft.com/regions/).
* "Váhy" a "Priority" jsou volitelné, jako u koncových bodů Azure.
* "MinChildEndpoints" parametr je nepovinný. Výchozí hodnota je '1'. Pokud počet dostupných koncových bodů klesne pod touto prahovou hodnotou, považuje profil nadřazené podřízené profilu 'snížený výkon' a přesměruje provoz do dalších koncových bodů v nadřazené profilu.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Příklad 1: Přidání vnořených koncových bodů pomocí `Add-AzureRmTrafficManagerEndpointConfig` a `Set-AzureRmTrafficManagerProfile`

V tomto příkladu jsme vytvořit nový Traffic Manageru podřízenými a nadřazenými profily, přidat podřízenou položku jako vnořený koncový bod s nadřazeným a potvrďte změny.

```powershell
$child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Pro zkrácení v tomto příkladu jsme ostatní koncové body nebyl přidán do podřízeného nebo nadřazeného profilů.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Příklad 2: Přidání vnořených koncových bodů pomocí `New-AzureRmTrafficManagerEndpoint`

V tomto příkladu přidáme existující profil podřízené jako vnořený koncový bod do existujícího profilu nadřazené. Profil, který je určen pomocí názvů skupin profilu a prostředků.

```powershell
$child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Přidání koncových bodů z jiného předplatného

Traffic Manager můžete pracovat s koncovými body z různých předplatných. Je potřeba přepnout na předplatné s koncovým bodem, který chcete přidat k načtení potřebné vstup do Traffic Manageru. Potom potřebujete přepnout na předplatné s profilem Traffic Manageru a přidejte encpoint do ní. Následujícím příkladu ukazuje, jak to provést s použitím veřejné IP adresy.

```powershell
Set-AzureRmContext -SubscriptionId $EndpointSubscription
$ip = Get-AzureRmPublicIpAddress -Name $IpAddresName -ResourceGroupName $EndpointRG

Set-AzureRmContext -SubscriptionId $trafficmanagerSubscription
New-AzureRmTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Aktualizovat koncový bod služby Traffic Manager

Existují dva způsoby, jak aktualizovat stávající koncových bodů Traffic Manageru:

1. Získat pomocí profilu Traffic Manageru `Get-AzureRmTrafficManagerProfile`, aktualizovat vlastnosti koncového bodu v rámci profilu a potvrďte změny v `Set-AzureRmTrafficManagerProfile`. Tato metoda nabízí výhodu v podobě nebudou moct aktualizovat více než jeden koncový bod v rámci jedné operace.
2. Získat pomocí koncových bodů Traffic Manageru `Get-AzureRmTrafficManagerEndpoint`, aktualizovat vlastnosti koncového bodu a potvrďte změny v `Set-AzureRmTrafficManagerEndpoint`. Tato metoda je jednodušší, protože nevyžaduje indexování pole koncových bodů v profilu.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>Příklad 1: Aktualizuje se koncové body pomocí `Get-AzureRmTrafficManagerProfile` a `Set-AzureRmTrafficManagerProfile`

V tomto příkladu upravíme prioritu na dva koncové body v rámci existující profil.

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$profile.Endpoints[0].Priority = 2
$profile.Endpoints[1].Priority = 1
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>Příklad 2: Aktualizuje se koncový bod pomocí `Get-AzureRmTrafficManagerEndpoint` a `Set-AzureRmTrafficManagerEndpoint`

V tomto příkladu upravíme váhu jeden koncový bod v existující profil.

```powershell
$endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Povolení a zakázání koncových bodů a profilů

Traffic Manager umožňuje jednotlivých koncových bodů pro povolené a zakázané, a také umožňuje povolení a zakázání celé profilů.
Tyto změny, můžete provést načítání/aktualizace/nastavení koncového bodu nebo profil prostředků. Pro zjednodušení tyto běžné operace, podporovány jsou i přes jednoúčelové rutiny určené.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Příklad 1: Povolení a zakázání profilu služby Traffic Manager

Chcete-li povolit profilu Traffic Manageru, použijte `Enable-AzureRmTrafficManagerProfile`. Profil, který se dá nastavit pomocí objektu profilu. Objekt profilu je možné předat pomocí kanálu nebo pomocí "-TrafficManagerProfile" parametr. V tomto příkladu zadáme profil podle názvu skupiny profilu a prostředků.

```powershell
Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Chcete-li zakázat profil služby Traffic Manager:

```powershell
Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Rutina Disable-AzureRmTrafficManagerProfile zobrazí výzvu k potvrzení. Tuto výzvu jde potlačit pomocí "-Force" parametr.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Příklad 2: Povolení a zakázání koncových bodů Traffic Manageru

Chcete-li povolit koncový bod služby Traffic Manager, použijte `Enable-AzureRmTrafficManagerEndpoint`. Existují dva způsoby, jak zadat koncový bod

1. Pomocí objektu TrafficManagerEndpoint předány prostřednictvím kanálu nebo "-TrafficManagerEndpoint" parametr
2. Pomocí názvu koncového bodu, typ koncového bodu, název profilu a název skupiny prostředků:

```powershell
Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Podobně chcete-li zakázat koncový bod služby Traffic Manager:

```powershell
Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Stejně jako u `Disable-AzureRmTrafficManagerProfile`, `Disable-AzureRmTrafficManagerEndpoint` rutiny zobrazí výzvu k potvrzení. Tuto výzvu jde potlačit pomocí "-Force" parametr.

## <a name="delete-a-traffic-manager-endpoint"></a>Odstranit koncový bod služby Traffic Manager

Chcete-li odebrat jednotlivé koncové body, použijte `Remove-AzureRmTrafficManagerEndpoint` rutiny:

```powershell
Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Tato rutina výzvu k potvrzení. Tuto výzvu jde potlačit pomocí "-Force" parametr.

## <a name="delete-a-traffic-manager-profile"></a>Odstranit profil služby Traffic Manager

Pokud chcete odstranit profil služby Traffic Manager, použijte `Remove-AzureRmTrafficManagerProfile` rutiny, zadáte název skupiny profilu a prostředků:

```powershell
Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Tato rutina výzvu k potvrzení. Tuto výzvu jde potlačit pomocí "-Force" parametr.

Profil, který chcete odstranit, je taky možné specifikovat pomocí objektu profilu:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Můžete předat také rourou toto pořadí:

```powershell
Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Další postup

[Monitorování Traffic Manageru](traffic-manager-monitoring.md)

[Důležité informace o výkonu nástroje Traffic Manager](traffic-manager-performance-considerations.md)
