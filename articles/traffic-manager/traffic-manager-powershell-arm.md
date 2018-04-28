---
title: Použití Powershellu ke správě Traffic Manageru v Azure | Microsoft Docs
description: Pomocí prostředí PowerShell pro správce provozu pomocí Azure Resource Manageru
services: traffic-manager
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: bc247448-1d2e-4104-ac03-42b59ebde065
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: 951e845e23a1ed0cbdc83fc24a97a545f00c52ad
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Použití Powershellu ke správě Traffic Manager

Azure Resource Manager je rozhraní pro správu upřednostňované pro služby v Azure. Profilů Azure Traffic Manager můžete spravovat pomocí rozhraní API založené na Azure Resource Manager a nástroje.

## <a name="resource-model"></a>Model prostředků

Azure Traffic Manager je konfigurovat pomocí kolekce nastavení nazývá profil Traffic Manageru. Tento profil obsahuje nastavení DNS, nastavení směrování provozu, nastavení monitorování koncového bodu a seznam koncových bodů služby, na které se směruje provoz.

Každý profil služby Traffic Manager je reprezentována prostředek typu 'TrafficManagerProfiles'. Na úrovni rozhraní API REST identifikátor URI pro každý profil je následující:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Nastavení prostředí Azure PowerShell

Tyto pokyny použijte Microsoft Azure PowerShell. V následujícím článku vysvětluje, jak nainstalovat a nakonfigurovat Azure PowerShell.

* [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview)

V příkladech v tomto článku předpokládá, že máte existující skupinu prostředků. Můžete vytvořit skupinu prostředků. pomocí následujícího příkazu:

```powershell
New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager vyžaduje, zda mají všechny skupiny prostředků do umístění. Toto umístění se používá jako výchozí pro prostředky vytvořené v příslušné skupině prostředků. Ale vzhledem k tomu, že globální, a ne místní prostředky profil správce provozu se volba umístění skupiny prostředků nemá žádný vliv na Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Správce provozu

Chcete-li vytvořit profil Traffic Manageru, použijte `New-AzureRmTrafficManagerProfile` rutiny:

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Následující tabulka popisuje parametry:

| Parametr | Popis |
| --- | --- |
| Název |Název prostředků pro prostředek profil Traffic Manageru. Profily ve stejné skupině prostředků musí mít jedinečné názvy. Tento název se liší od názvu DNS pro dotazy DNS. |
| ResourceGroupName |Název skupiny prostředků obsahující profil prostředků. |
| TrafficRoutingMethod |Určuje metodu směrování provozu umožňuje určit, kterému koncovému bodu je vrácený v odpovědi dotaz DNS. Možné hodnoty jsou 'Výkonu', "Vážená" nebo "Priority". |
| RelativeDnsName |Určuje název hostitele část názvu DNS, který poskytuje tento profil služby Traffic Manager. Tato hodnota je v kombinaci s názvem domény DNS, který se používá k vytvoření plně kvalifikovaný název domény (FQDN) profilu Azure Traffic Managerem. Například nastavení hodnoty "contoso" bude "contoso.trafficmanager.net." |
| TTL |DNS Time-to-Live (TTL), určuje v sekundách. Tato hodnota TTL informuje překladače místní DNS a klienty DNS, jak dlouho chcete mezipaměť odpovědí DNS pro tento profil služby Traffic Manager. |
| MonitorProtocol |Určuje protokol, který se použít k monitorování stavu koncový bod. Možné hodnoty jsou "HTTP" a "HTTPS". |
| MonitorPort |Určuje port TCP používá k monitorování stavu koncový bod. |
| MonitorPath |Určuje cestu k názvu domény koncový bod, který je použít k testu pro koncový bod stavu. |

Rutina vytvoří profil Traffic Manageru v Azure a vrátí objekt odpovídající profil prostředí PowerShell. V tomto okamžiku profil neobsahuje žádné koncové body. Další informace o přidávání koncové body profilu Správce provozu najdete v tématu [přidání koncové body Traffic Manager](#adding-traffic-manager-endpoints).

## <a name="get-a-traffic-manager-profile"></a>Získat profil správce provozu

Chcete-li načíst objekt existující profil Traffic Manageru, použijte `Get-AzureRmTrafficManagerProfle` rutiny:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Tato rutina vrátí objekt profilu Traffic Manageru.

## <a name="update-a-traffic-manager-profile"></a>Aktualizovat profil správce provozu

Úprava profily Traffic Manager zahrnuje proces krok 3:

1. Načíst profil pomocí `Get-AzureRmTrafficManagerProfile` nebo použijte profil vrácený `New-AzureRmTrafficManagerProfile`.
2. Upravte profil. Můžete přidat a odebrat koncových bodů nebo změňte parametry koncového bodu nebo profilu. Tyto změny jsou offline operace. Měníte jenom místní objekt v paměti, která představuje profil.
3. Potvrdit změny pomocí `Set-AzureRmTrafficManagerProfile` rutiny.

S výjimkou RelativeDnsName profilu lze změnit všechny vlastnosti profilu. Chcete-li změnit RelativeDnsName, je nutné odstranit profil a nový profil s novým názvem.

Následující příklad ukazuje, jak změnit profilu TTL:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$profile.Ttl = 300
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Existují tři typy koncových bodů Traffic Manager:

1. **Koncové body Azure** jsou služby hostované v Azure
2. **Externí koncové body** jsou služby hostované mimo Azure
3. **Vnořené koncové body** se používají k vytvoření vnořené hierarchií profily Traffic Manageru. Vnořené koncové body povolit rozšířené konfigurace směrování provozu pro komplexní aplikace.

Ve všech případech tři koncové body můžete přidat dvěma způsoby:

1. Pomocí kroku 3 postup popsaný výše. Výhodou této metody je, že lze provést několik změn koncového bodu v jednu aktualizaci.
2. Pomocí rutiny New-AzureRmTrafficManagerEndpoint. Tato rutina přidá koncový bod do existujícího profilu Správce provozu v rámci jedné operace.

## <a name="adding-azure-endpoints"></a>Přidání koncové body Azure

Koncové body Azure odkazovat služby hostované v Azure. Podporuje dva typy koncové body Azure:

1. Azure Web Apps
2. Azure prostředky PublicIpAddress (které lze připojit k vyrovnávání zatížení nebo virtuální počítač síťový adaptér). PublicIpAddress musí mít název DNS přiřazené k použití v Traffic Manageru.

V každém případě:

* Služba je zadán pomocí parametru 'targetResourceId' `Add-AzureRmTrafficManagerEndpointConfig` nebo `New-AzureRmTrafficManagerEndpoint`.
* "Target" a 'EndpointLocation' jsou implicitní parametrem TargetResourceId.
* Zadání 'Weight' je volitelný. Váhu používají jenom Pokud je profil je nakonfigurovaný na použití metodu směrování provozu 'Vážená'. Jinak jsou ignorovány. -Li zadána, hodnota musí být číslo v rozsahu od 1 do 1000. Výchozí hodnota je '1'.
* Zadání "Priority" je volitelný. Priority používají jenom Pokud je profil je nakonfigurovaný na použití metodu směrování provozu "Priority". Jinak jsou ignorovány. Platné hodnoty jsou od 1 do 1000 s nižšími hodnotami, která znamená vyšší prioritu. Pokud je zadán pro jeden koncový bod, musí být zadán pro všechny koncové body. Pokud tento parametr vynechán, se použijí výchozí hodnoty od '1' v pořadí, jsou uvedeny koncových bodů.

### <a name="example-1-adding-web-app-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>Příklad 1: Přidání koncových bodů webové aplikace pomocí `Add-AzureRmTrafficManagerEndpointConfig`

V tomto příkladu jsme vytvořte profil Traffic Manageru a přidejte dva koncové body webové aplikace pomocí `Add-AzureRmTrafficManagerEndpointConfig` rutiny.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzureRMWebApp -Name webapp1
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzureRMWebApp -Name webapp2
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Příklad 2: Přidání koncového bodu publicIpAddress pomocí `New-AzureRmTrafficManagerEndpoint`

V tomto příkladu je prostředek veřejné IP adresy do profil služby Traffic Manager. Veřejná IP adresa musí mít název DNS nakonfigurovaný a mohou být vázány na síťový adaptér virtuálního počítače nebo ke službě Vyrovnávání zatížení.

```powershell
$ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Přidání externí koncové body

Traffic Manager používá externí koncové body pro přesměrování provozu ke službám hostovaným mimo Azure. Jak se koncové body Azure externí koncové body přidáním buď pomocí `Add-AzureRmTrafficManagerEndpointConfig` následuje `Set-AzureRmTrafficManagerProfile`, nebo `New-AzureRMTrafficManagerEndpoint`.

Při zadání externí koncové body:

* Název domény koncového bodu musí být určen pomocí parametru 'Target'
* Pokud se používá metodu směrování provozu 'Výkonu', 'EndpointLocation' je povinný. V opačném případě je volitelný. Hodnota musí být [platný oblast Azure název](https://azure.microsoft.com/regions/).
* 'Weight' a 'prioritu, jsou volitelné.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Příklad 1: Přidání externí koncové body pomocí `Add-AzureRmTrafficManagerEndpointConfig` a `Set-AzureRmTrafficManagerProfile`

V tomto příkladu jsme vytvořit profil Traffic Manageru, přidejte dva externí koncové body a potvrďte změny.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Příklad 2: Přidání externí koncové body pomocí `New-AzureRmTrafficManagerEndpoint`

V tomto příkladu přidáme externí koncový bod do existujícího profilu. Profil je zadán pomocí názvy skupin profilu a prostředků.

```powershell
New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Přidání koncových bodů 'vnořené.

Každý profil služby Traffic Manager určuje jednu metodu směrování provozu. Existují však scénáře, které vyžadují složitější směrování provozu, než směrování poskytuje jeden profil Traffic Manageru. Lze vnořit profily Traffic Manageru se zkombinovat výhod více než jednu metodu směrování provozu. Vnořené profily umožňují přepsat výchozí chování Traffic Manager na podporu větší a složitější nasazení aplikací. Podrobné příklady, najdete v části [profily Traffic Manageru vnořené](traffic-manager-nested-profiles.md).

Vnořené koncových bodů jsou nakonfigurované v nadřazené profil, pomocí konkrétní koncový bod typu, 'NestedEndpoints'. Při zadávání vnořené koncové body:

* Koncový bod musí být určen pomocí parametru 'targetResourceId.
* Pokud se používá metodu směrování provozu 'Výkonu', 'EndpointLocation' je povinný. V opačném případě je volitelný. Hodnota musí být [platný oblast Azure název](http://azure.microsoft.com/regions/).
* 'Weight' a 'prioritu, jsou volitelné, jako koncové body Azure.
* Parametr 'MinChildEndpoints' je volitelné. Výchozí hodnota je '1'. Pokud počet dostupných koncových bodů klesne pod touto prahovou hodnotou, profil nadřazené zvažuje profilu podřízené 'snížený výkon a přesměruje přenosy na dalších koncových bodů v nadřazené profilu.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Příklad 1: Přidání vnořené koncové body pomocí `Add-AzureRmTrafficManagerEndpointConfig` a `Set-AzureRmTrafficManagerProfile`

V tomto příkladu jsme vytvoření nové Traffic Manager podřízenými a nadřazenými profilů, přidat podřízené jako koncový bod vnořené do nadřazené a potvrďte změny.

```powershell
$child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Jako stručný výtah v tomto příkladu jsme nepřidali žádné další koncové body nadřazená nebo podřízená profily.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Příklad 2: Přidání vnořené koncové body pomocí `New-AzureRmTrafficManagerEndpoint`

V tomto příkladu přidáme stávající profil podřízené jako vnořené koncový bod do existujícího profilu nadřazené. Profil je zadán pomocí názvy skupin profilu a prostředků.

```powershell
$child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Přidání koncových bodů z jiné předplatné

Správce provozu můžete pracovat s koncovými body z různých předplatných. Potřebujete přepnout do předplatného s koncovým bodem, který chcete přidat k načtení potřebné vstup do Traffic Manageru. Potom potřebujete přepnout předplatných s profilem správce provozu a encpoint přidejte do ní. Následující příklad ukazuje, jak to provést pomocí veřejnou IP adresu.

```powershell
Set-AzureRmContext -SubscriptionId $EndpointSubscription
$ip = Get-AzureRmPublicIpAddress -Name $IpAddresName -ResourceGroupName $EndpointRG

Set-AzureRmContext -SubscriptionId $trafficmanagerSubscription
New-AzureRmTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Aktualizovat koncový bod Traffic Manager

Existují dva způsoby, jak aktualizovat existující koncový bod Traffic Manager:

1. Získat pomocí profilu Traffic Manageru `Get-AzureRmTrafficManagerProfile`, aktualizovat vlastnosti koncového bodu v rámci profilu a provést změny v nástroji `Set-AzureRmTrafficManagerProfile`. Tato metoda má výhodu v podobě bude možné aktualizovat více než jeden koncový bod v rámci jedné operace.
2. Získat pomocí koncového bodu Traffic Manager `Get-AzureRmTrafficManagerEndpoint`, aktualizovat vlastnosti koncový bod a provést změny v nástroji `Set-AzureRmTrafficManagerEndpoint`. Tato metoda je jednodušší, protože nevyžaduje indexování do pole koncových bodů v profilu.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>Příklad 1: Aktualizace koncové body pomocí `Get-AzureRmTrafficManagerProfile` a `Set-AzureRmTrafficManagerProfile`

V tomto příkladu jsme změnit prioritu na dva koncové body v rámci stávající profil.

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$profile.Endpoints[0].Priority = 2
$profile.Endpoints[1].Priority = 1
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>Příklad 2: Aktualizace k koncový bod pomocí `Get-AzureRmTrafficManagerEndpoint` a `Set-AzureRmTrafficManagerEndpoint`

V tomto příkladu jsme změnit váhu jeden koncový bod v existující profil.

```powershell
$endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Povolování a zakazování koncových bodů a profily

Traffic Manager umožňuje jednotlivé koncové body pro povolené a zakázané, a také umožňuje zapnutí a vypnutí celý profilů.
Tyto změny provést získávání/aktualizace nebo nastavení koncového bodu nebo profil prostředků. Zefektivnění těchto běžných operací, podporovány jsou i přes vyhrazené rutiny.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Příklad 1: Povolení a zakázání profilu Traffic Manageru

Pokud chcete povolit profil Traffic Manageru, použijte `Enable-AzureRmTrafficManagerProfile`. Profil lze zadat pomocí objektu profilu. Objekt profilu se dá předat prostřednictvím kanálu nebo pomocí '-TrafficManagerProfile' parametr. V tomto příkladu určíme profil podle názvu skupiny profilu a prostředků.

```powershell
Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Chcete-li zakázat profil správce provozu:

```powershell
Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Rutina Disable-AzureRmTrafficManagerProfile vyzve k potvrzení. Tuto výzvu lze potlačit pomocí '-Force' parametr.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Příklad 2: Povolení a zakázání koncový bod Traffic Manager

Chcete-li povolit koncový bod Traffic Manager, použijte `Enable-AzureRmTrafficManagerEndpoint`. Existují dva způsoby, jak zadat koncový bod

1. Pomocí objekt TrafficManagerEndpoint předány prostřednictvím kanálu nebo pomocí '-TrafficManagerEndpoint' parametr
2. Pomocí název koncového bodu, typ koncového bodu, název profilu a název skupiny prostředků:

```powershell
Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Podobně chcete-li zakázat koncový bod Traffic Manager:

```powershell
Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Stejně jako u `Disable-AzureRmTrafficManagerProfile`, `Disable-AzureRmTrafficManagerEndpoint` rutiny vyzve k potvrzení. Tuto výzvu lze potlačit pomocí '-Force' parametr.

## <a name="delete-a-traffic-manager-endpoint"></a>Odstranit koncový bod Traffic Manager

Chcete-li odebrat jednotlivé koncové body, použijte `Remove-AzureRmTrafficManagerEndpoint` rutiny:

```powershell
Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Tato rutina vyzve k potvrzení. Tuto výzvu lze potlačit pomocí '-Force' parametr.

## <a name="delete-a-traffic-manager-profile"></a>Odstranit profil správce provozu

Pokud chcete odstranit profil správce provozu, použijte `Remove-AzureRmTrafficManagerProfile` rutiny zadání názvu skupiny profilu a prostředků:

```powershell
Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Tato rutina vyzve k potvrzení. Tuto výzvu lze potlačit pomocí '-Force' parametr.

Profil pro odstranění je možné zadat také pomocí objektu profilu:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Toto pořadí lze také přesměrovat:

```powershell
Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Další postup

[Monitorování správce provozu](traffic-manager-monitoring.md)

[Důležité informace o výkonu nástroje Traffic Manager](traffic-manager-performance-considerations.md)
