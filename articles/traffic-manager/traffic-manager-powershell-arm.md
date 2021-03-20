---
title: Použití PowerShellu ke správě Traffic Manager v Azure
description: Pomocí této cesty výukového programu můžete začít používat Azure PowerShell pro Traffic Manager.
services: traffic-manager
documentationcenter: na
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 21076fe23301c189d9987f78706cc81691ce7a4f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89400530"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Použití PowerShellu ke správě Traffic Manager

Azure Resource Manager je preferované rozhraní pro správu služeb v Azure. Profily Azure Traffic Manager je možné spravovat pomocí rozhraní API a nástrojů založených na Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>Model prostředků

Azure Traffic Manager se konfiguruje pomocí kolekce nastavení označované jako Traffic Manager profil. Tento profil obsahuje nastavení DNS, nastavení směrování provozu, nastavení monitorování koncových bodů a seznam koncových bodů služby, na které se směruje provoz.

Každý profil Traffic Manager představuje prostředek typu "TrafficManagerProfiles". Na úrovni REST API je identifikátor URI pro každý profil následující:

`https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}`

## <a name="setting-up-azure-powershell"></a>Nastavení Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tyto pokyny používají Microsoft Azure PowerShell. Následující článek vysvětluje, jak nainstalovat a nakonfigurovat Azure PowerShell.

* [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/)

V příkladech v tomto článku se předpokládá, že máte existující skupinu prostředků. Skupinu prostředků můžete vytvořit pomocí následujícího příkazu:

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager vyžaduje, aby měly všechny skupiny prostředků umístění. Toto umístění se používá jako výchozí pro prostředky vytvořené v dané skupině prostředků. Vzhledem k tomu, že prostředky Traffic Manager profilů jsou globální, nikoli regionální, Volba umístění skupiny prostředků nemá žádný vliv na Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Vytvořit profil Traffic Manager

Chcete-li vytvořit profil Traffic Manager, použijte `New-AzTrafficManagerProfile` rutinu:

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Parametry jsou popsány v následující tabulce:

| Parametr | Popis |
| --- | --- |
| Název |Název prostředku profilu Traffic Manager. Profily ve stejné skupině prostředků musí mít jedinečné názvy. Tento název je oddělený od názvu DNS, který se používá pro dotazy DNS. |
| ResourceGroupName |Název skupiny prostředků, která obsahuje prostředek profilu. |
| TrafficRoutingMethod |Určuje metodu směrování provozu, pomocí které se určí, který koncový bod se vrátí v odpovědi na dotaz DNS. Možné hodnoty jsou Performance, vážená nebo priorita. |
| RelativeDnsName |Určuje část názvu hostitele názvu DNS, kterou poskytuje tento Traffic Manager profil. Tato hodnota je kombinována s názvem domény DNS používaným službou Azure Traffic Manager k vytvoření plně kvalifikovaného názvu domény (FQDN) profilu. Například když nastavíte hodnotu "contoso", bude se jednat o "contoso.trafficmanager.net". |
| TTL |Určuje hodnotu TTL (Time to Live) služby DNS v sekundách. Tato hodnota TTL informuje místní překladače DNS a klienty DNS, jak dlouho ukládat odpovědi DNS na tento profil Traffic Manager. |
| MonitorProtocol |Určuje protokol, který se má použít k monitorování stavu koncového bodu. Možné hodnoty jsou "HTTP" a "HTTPS". |
| MonitorPort |Určuje port TCP, který se používá ke sledování stavu koncového bodu. |
| MonitorPath |Určuje cestu relativní k názvu domény koncového bodu, který se používá k testování stavu koncového bodu. |

Rutina vytvoří profil Traffic Manager v Azure a vrátí odpovídající objekt profilu do PowerShellu. V tomto okamžiku profil neobsahuje žádné koncové body. Další informace o přidání koncových bodů do profilu Traffic Manager najdete v tématu Přidání Traffic Manager koncových bodů.

## <a name="get-a-traffic-manager-profile"></a>Získat profil Traffic Manager

Pokud chcete načíst existující objekt profilu Traffic Manager, použijte `Get-AzTrafficManagerProfle` rutinu:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Tato rutina vrací objekt Profile Traffic Manager.

## <a name="update-a-traffic-manager-profile"></a>Aktualizace profilu Traffic Manager

Úprava Traffic Manager profilů se řídí procesem se 3 kroky:

1. Načtěte profil pomocí `Get-AzTrafficManagerProfile` nebo použijte profil vrácený funkcí `New-AzTrafficManagerProfile` .
2. Upravte profil. Můžete přidat nebo odebrat koncové body nebo změnit parametry koncového bodu nebo profilu. Tyto změny jsou offline operace. Měníte pouze místní objekt v paměti, která představuje profil.
3. Pomocí rutiny potvrďte provedené změny `Set-AzTrafficManagerProfile` .

Všechny vlastnosti profilu se dají změnit s výjimkou RelativeDnsName profilu. Chcete-li změnit RelativeDnsName, je nutné odstranit profil a nový profil s novým názvem.

Následující příklad ukazuje, jak změnit hodnotu TTL profilu:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

Existují tři typy koncových bodů Traffic Manager:

1. **Koncové body Azure** jsou služby hostované v Azure.
2. **Externí koncové body** jsou služby hostované mimo Azure.
3. **Vnořené koncové body** slouží k vytváření vnořených hierarchií Traffic Manager profilů. Vnořené koncové body umožňují pokročilou konfiguraci směrování provozu pro komplexní aplikace.

Ve všech třech případech lze koncové body přidat dvěma způsoby:

1. Použití procesu se třemi kroky popsanými výše. Výhodou této metody je, že v rámci jedné aktualizace je možné provést několik změn v koncovém bodu.
2. Pomocí rutiny New-AzTrafficManagerEndpoint. Tato rutina přidá koncový bod do existujícího profilu Traffic Manager v rámci jedné operace.

## <a name="adding-azure-endpoints"></a>Přidávání Koncové body Azure

Referenční služby Azure pro koncové body hostované v Azure. Podporují se dva typy koncových bodů Azure:

1. Azure App Service
2. Prostředky Azure PublicIpAddress (které se dají připojit k nástroji pro vyrovnávání zatížení nebo síťové kartě virtuálního počítače). PublicIpAddress musí mít přiřazený název DNS, aby se mohl použít v Traffic Manager.

V každém případě:

* Služba je určena pomocí parametru parametrem targetresourceid `Add-AzTrafficManagerEndpointConfig` nebo `New-AzTrafficManagerEndpoint` .
* Vlastnost target a EndpointLocation je odvozena v parametrem targetresourceid.
* Zadání ' Weight ' je volitelné. Váhy se používají jenom v případě, že je profil nakonfigurovaný tak, aby používal metodu "váženého směrování provozu". V opačném případě jsou ignorovány. Je-li tento parametr zadán, musí být číslo v rozsahu od 1 do 1000. Výchozí hodnota je 1.
* Zadání priority je volitelné. Priority se používají jenom v případě, že je profil nakonfigurovaný tak, aby používal metodu "Priorita" směrování provozu. V opačném případě jsou ignorovány. Platné hodnoty jsou od 1 do 1000 s nižšími hodnotami, které označují vyšší prioritu. Pokud je tato parametr zadána pro jeden koncový bod, musí být zadána pro všechny koncové body. Je-li tento parametr vynechán, výchozí hodnoty začínající znakem 1 jsou aplikovány v pořadí, v němž jsou uvedeny koncové body.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>Příklad 1: Přidání koncových bodů App Service pomocí `Add-AzTrafficManagerEndpointConfig`

V tomto příkladu vytvoříme profil Traffic Manager a přidáte dva koncové body App Service pomocí `Add-AzTrafficManagerEndpointConfig` rutiny.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>Příklad 2: Přidání koncového bodu publicIpAddress pomocí `New-AzTrafficManagerEndpoint`

V tomto příkladu je prostředek veřejné IP adresy přidán do profilu Traffic Manager. Veřejná IP adresa musí mít nakonfigurovaný název DNS a může být vázaná buď na síťový adaptér virtuálního počítače, nebo na nástroj pro vyrovnávání zatížení.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Přidávání Externí koncové body

Traffic Manager používá externí koncové body pro směrování provozu do služeb hostovaných mimo Azure. Stejně jako u koncových bodů Azure je možné přidat externí koncové body pomocí `Add-AzTrafficManagerEndpointConfig` operátoru `Set-AzTrafficManagerProfile` , nebo `New-AzTrafficManagerEndpoint` .

Při určování externích koncových bodů:

* Název domény koncového bodu musí být zadaný pomocí parametru Target.
* Pokud se používá metoda směrování provozu "Performance", je vyžadováno klíčové slovo ' EndpointLocation '. V opačném případě je to volitelné. Hodnota musí být [platný název oblasti Azure](https://azure.microsoft.com/regions/).
* Hodnota ' Weight ' a ' priorita ' je volitelná.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Příklad 1: přidání externích koncových bodů pomocí `Add-AzTrafficManagerEndpointConfig` a `Set-AzTrafficManagerProfile`

V tomto příkladu vytvoříme profil Traffic Manager, přidáte dva externí koncové body a změny potvrdíte.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>Příklad 2: přidání externích koncových bodů pomocí `New-AzTrafficManagerEndpoint`

V tomto příkladu přidáme externí koncový bod do existujícího profilu. Profil se zadává pomocí názvů profilů a skupin prostředků.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Přidávání koncových bodů "Nested"

Každý profil Traffic Manager Určuje jednu metodu směrování provozu. Existují však scénáře, které vyžadují výkonnější směrování provozu než směrování poskytované jedním Traffic Manager profilem. Můžete vnořovat profily Traffic Manager, abyste mohli kombinovat výhody více než jedné metody směrování provozu. Vnořené profily umožňují přepsat výchozí chování Traffic Manager pro podporu většího a složitějšího nasazení aplikací. Podrobnější příklady najdete v tématu [vnořené Traffic Manager profily](traffic-manager-nested-profiles.md).

Vnořené koncové body jsou nakonfigurovány v nadřazeném profilu pomocí konkrétního typu koncového bodu "NestedEndpoints". Při zadávání vnořených koncových bodů:

* Koncový bod musí být zadaný pomocí parametru parametrem targetresourceid.
* Pokud se používá metoda směrování provozu "Performance", je vyžadováno klíčové slovo ' EndpointLocation '. V opačném případě je to volitelné. Hodnota musí být [platný název oblasti Azure](https://azure.microsoft.com/regions/).
* Možnost váha a Priorita je volitelná, jako u koncových bodů Azure.
* Parametr MinChildEndpoints je nepovinný. Výchozí hodnota je 1. Pokud počet dostupných koncových bodů klesne pod tuto prahovou hodnotu, nadřazený profil považuje podřízený profil za Degradováno a přesměruje provoz do ostatních koncových bodů v nadřazeném profilu.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Příklad 1: Přidání vnořených koncových bodů pomocí `Add-AzTrafficManagerEndpointConfig` a `Set-AzTrafficManagerProfile`

V tomto příkladu vytvoříme nové Traffic Manager podřízené a nadřazené profily, přidáte podřízenou položku jako vnořený koncový bod nadřazenému objektu a potvrdíte změny.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

Pro zkrácení v tomto příkladu jsme do podřízených nebo nadřazených profilů nepřidali žádné jiné koncové body.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>Příklad 2: Přidání vnořených koncových bodů pomocí `New-AzTrafficManagerEndpoint`

V tomto příkladu přidáme existující podřízený profil jako vnořený koncový bod do existujícího nadřazeného profilu. Profil se zadává pomocí názvů profilů a skupin prostředků.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Přidávání koncových bodů z jiného předplatného

Traffic Manager může pracovat s koncovými body z různých předplatných. Musíte přepnout na předplatné s koncovým bodem, který chcete přidat, abyste načetli potřebný vstup pro Traffic Manager. Pak je nutné přepnout na odběry s profilem Traffic Manager a přidat do něj koncový bod. Následující příklad ukazuje, jak to provést s veřejnou IP adresou.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Aktualizace koncového bodu Traffic Manager

Existují dva způsoby, jak aktualizovat existující koncový bod Traffic Manager:

1. Získejte Profil Traffic Manager pomocí `Get-AzTrafficManagerProfile` , aktualizujte vlastnosti koncového bodu v rámci profilu a potvrďte změny pomocí `Set-AzTrafficManagerProfile` . Tato metoda má schopnost aktualizovat více než jeden koncový bod v rámci jedné operace.
2. Získejte Traffic Manager koncový bod pomocí `Get-AzTrafficManagerEndpoint` , aktualizujte vlastnosti koncového bodu a potvrďte změny pomocí `Set-AzTrafficManagerEndpoint` . Tato metoda je jednodušší, protože nevyžaduje indexování do pole koncových bodů v profilu.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>Příklad 1: aktualizace koncových bodů pomocí `Get-AzTrafficManagerProfile` a `Set-AzTrafficManagerProfile`

V tomto příkladu upravujeme prioritu dvou koncových bodů v rámci existujícího profilu.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>Příklad 2: aktualizace koncového bodu pomocí `Get-AzTrafficManagerEndpoint` a `Set-AzTrafficManagerEndpoint`

V tomto příkladu upravujeme váhu jednoho koncového bodu v existujícím profilu.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Povolování a zakazování koncových bodů a profilů

Traffic Manager umožňuje povolit a zakázat jednotlivé koncové body a zároveň povolit a zakázat celé profily.
Tyto změny se dají udělat tak, že se načítají a aktualizují nebo nastavují prostředky koncového bodu nebo profilu. Pro zjednodušení těchto běžných operací jsou také podporovány prostřednictvím vyhrazených rutin.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Příklad 1: povolení a zakázání profilu Traffic Manager

Pokud chcete povolit profil Traffic Manager, použijte `Enable-AzTrafficManagerProfile` . Profil lze zadat pomocí objektu Profile. Objekt profilu lze předat prostřednictvím kanálu nebo pomocí parametru-TrafficManagerProfile. V tomto příkladu určíme profil podle názvu profilu a skupiny prostředků.

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Postup zakázání profilu Traffic Manager:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Rutina Disable-AzTrafficManagerProfile zobrazí výzvu k potvrzení. Tato výzva se dá potlačit pomocí parametru-Force.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Příklad 2: povolení a zákaz Traffic Managerho koncového bodu

Pokud chcete povolit Traffic Manager koncový bod, použijte `Enable-AzTrafficManagerEndpoint` . Existují dva způsoby, jak zadat koncový bod.

1. Použití objektu TrafficManagerEndpoint, který byl předán prostřednictvím kanálu nebo pomocí parametru-TrafficManagerEndpoint
2. Pomocí názvu koncového bodu, typu koncového bodu, názvu profilu a názvu skupiny prostředků:

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Podobně zakážete Traffic Manager koncový bod:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Stejně jako u `Disable-AzTrafficManagerProfile` , `Disable-AzTrafficManagerEndpoint` rutina vyzve k potvrzení. Tato výzva se dá potlačit pomocí parametru-Force.

## <a name="delete-a-traffic-manager-endpoint"></a>Odstranění Traffic Managerho koncového bodu

Chcete-li odebrat jednotlivé koncové body, použijte `Remove-AzTrafficManagerEndpoint` rutinu:

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Tato rutina vyzve k potvrzení. Tato výzva se dá potlačit pomocí parametru-Force.

## <a name="delete-a-traffic-manager-profile"></a>Odstranit profil Traffic Manager

Pokud chcete odstranit profil Traffic Manager, použijte `Remove-AzTrafficManagerProfile` rutinu a zadejte názvy profilů a skupin prostředků:

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Tato rutina vyzve k potvrzení. Tato výzva se dá potlačit pomocí parametru-Force.

Profil, který se má odstranit, se dá zadat taky pomocí objektu Profile:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

Tuto sekvenci lze také přesměrovat na kanál:

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Další kroky

[Monitorování Traffic Manager](traffic-manager-monitoring.md)

[Důležité informace o výkonu nástroje Traffic Manager](traffic-manager-performance-considerations.md)
