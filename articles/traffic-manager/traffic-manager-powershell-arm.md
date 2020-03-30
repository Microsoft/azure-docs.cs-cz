---
title: Správa Traffic Manageru v Azure pomocí PowerShellu
description: S tímto studijním programem můžete začít používat Azure PowerShell pro Traffic Manager.
services: traffic-manager
documentationcenter: na
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 7886764a69eefa68be071a801bea65ae995fbdc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938500"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Správa Traffic Manageru pomocí Prostředí PowerShell

Azure Resource Manager je upřednostňované rozhraní pro správu služeb v Azure. Profily Azure Traffic Manageru se můžou spravit pomocí api a nástrojů založených na Azure Resource Manageru.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>Model prostředků

Azure Traffic Manager je nakonfigurovaný pomocí kolekce nastavení nazývaných profil Traffic Manageru. Tento profil obsahuje nastavení DNS, nastavení směrování provozu, nastavení monitorování koncového bodu a seznam koncových bodů služby, do kterých je provoz směrován.

Každý profil traffic manageru je reprezentován zdrojem typu TrafficManagerProfiles. Na úrovni rozhraní REST API je identifikátor URI pro každý profil následující:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Nastavení Azure PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tyto pokyny používají Microsoft Azure PowerShell. V následujícím článku je vysvětleno, jak nainstalovat a nakonfigurovat Azure PowerShell.

* [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview)

Příklady v tomto článku předpokládají, že máte existující skupinu prostředků. Skupinu prostředků můžete vytvořit pomocí následujícího příkazu:

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager vyžaduje, aby všechny skupiny prostředků mít umístění. Toto umístění se používá jako výchozí pro prostředky vytvořené v této skupině prostředků. Protože však prostředky profilu Traffic Manageru jsou globální, nikoli místní, výběr umístění skupiny prostředků nemá žádný vliv na Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu traffic managera

Chcete-li vytvořit profil Traffic `New-AzTrafficManagerProfile` Manageru, použijte rutinu:

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Následující tabulka popisuje parametry:

| Parametr | Popis |
| --- | --- |
| Name (Název) |Název prostředku pro prostředek profilu traffic manageru. Profily ve stejné skupině prostředků musí mít jedinečné názvy. Tento název je oddělen od názvu DNS používaného pro dotazy DNS. |
| ResourceGroupName |Název skupiny prostředků obsahující prostředek profilu. |
| TrafficRoutingMethod |Určuje metodu směrování provozu použitou k určení, který koncový bod je vrácen v odpovědi na dotaz DNS. Možné hodnoty jsou "Výkon", "Vážený" nebo "Priorita". |
| RelativníNázev_její jakoby |Určuje část názvu_hostitele názvu DNS poskytnutou tímto profilem traffic manageru. Tato hodnota je kombinována s názvem domény DNS, který používá Azure Traffic Manager k vytvoření plně kvalifikovaného názvu domény (FQDN) profilu. Například nastavení hodnoty 'contoso' se změní na "contoso.trafficmanager.net". |
| Hodnota TTL |Určuje aktivní čas DNS (TTL) v sekundách. Tato ttl informuje místní překladače DNS a klienty DNS o tom, jak dlouho bude pro tento profil traffic manageru uloženy do mezipaměti. |
| MonitorProtocol |Určuje protokol, který se má použít ke sledování stavu koncového bodu. Možné hodnoty jsou 'HTTP' a 'HTTPS'. |
| MonitorPort |Určuje port TCP používaný ke sledování stavu koncového bodu. |
| Cesta monitoru |Určuje cestu vzhledem k názvu domény koncového bodu, který se používá ke sondování stavu koncového bodu. |

Rutina vytvoří profil Traffic Manager v Azure a vrátí odpovídající objekt profilu powershellu. V tomto okamžiku profil neobsahuje žádné koncové body. Další informace o přidávání koncových bodů do profilu traffic manageru najdete v tématu Přidání koncových bodů traffic manageru.

## <a name="get-a-traffic-manager-profile"></a>Získejte profil traffic managera

Chcete-li načíst existující objekt `Get-AzTrafficManagerProfle` profilu Traffic Manageru, použijte rutinu:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Tato rutina vrátí objekt profilu Traffic Manageru.

## <a name="update-a-traffic-manager-profile"></a>Aktualizace profilu traffic managera

Úprava profilů traffic manageru se řídí třífázovým procesem:

1. Načíst profil `Get-AzTrafficManagerProfile` pomocí nebo použít `New-AzTrafficManagerProfile`profil vrácený .
2. Upravte profil. Můžete přidat a odebrat koncové body nebo změnit parametry koncového bodu nebo profilu. Tyto změny jsou off-line operace. Měníte pouze místní objekt v paměti, který představuje profil.
3. Potvrďte změny `Set-AzTrafficManagerProfile` pomocí rutiny.

Všechny vlastnosti profilu lze změnit s výjimkou relativednsname profilu. Chcete-li změnit relativednsname, musíte odstranit profil a nový profil s novým názvem.

Následující příklad ukazuje, jak změnit ttl profilu:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

Existují tři typy koncových bodů Traffic Manageru:

1. **Koncové body Azure** jsou služby hostované v Azure
2. **Externí koncové body** jsou služby hostované mimo Azure
3. **Vnořené koncové body** se používají k vytvoření vnořených hierarchií profilů Traffic Manageru. Vnořené koncové body umožňují pokročilé konfigurace směrování provozu pro složité aplikace.

Ve všech třech případech mohou být koncové body přidány dvěma způsoby:

1. Použití 3krokového procesu popsaného výše. Výhodou této metody je, že několik změn koncového bodu lze provést v jedné aktualizaci.
2. Použití rutiny New-AzTrafficManagerEndpoint. Tato rutina přidá koncový bod do existujícího profilu Traffic Manager v jedné operaci.

## <a name="adding-azure-endpoints"></a>Přidání koncových bodů Azure

Referenční služby azure koncových bodů hostované v Azure. Jsou podporované dva typy koncových bodů Azure:

1. Azure App Service
2. Prostředky Azure PublicIpAddress (které lze připojit k nástrojovi pro vyrovnávání zatížení nebo k nic virtuálního počítače). Adresa PublicIpAddress musí mít přiřazen ý název DNS pro použití ve Správci provozu.

V každém případě:

* Služba je zadána pomocí parametru 'targetResourceId' `Add-AzTrafficManagerEndpointConfig` nebo `New-AzTrafficManagerEndpoint`.
* 'Target' a 'EndpointLocation' jsou implikovány TargetResourceId.
* Zadání "Hmotnost" je volitelné. Závaží se používají pouze v případě, že je profil nakonfigurován tak, aby používal metodu směrování provozu "Vážený". V opačném případě jsou ignorovány. Pokud je zadán, hodnota musí být číslo mezi 1 a 1000. Výchozí hodnota je "1".
* Zadání "Priorita" je volitelné. Priority se používají pouze v případě, že je profil nakonfigurován tak, aby používal metodu směrování provozu "Priorita". V opačném případě jsou ignorovány. Platné hodnoty jsou od 1 do 1000 s nižšími hodnotami označujícími vyšší prioritu. Pokud je zadán pro jeden koncový bod, musí být určen y pro všechny koncové body. Pokud je vynecháno, výchozí hodnoty začínající od "1" jsou použity v pořadí, ve které jsou uvedeny koncové body.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>Příklad 1: Přidání koncových bodů služby App Service pomocí`Add-AzTrafficManagerEndpointConfig`

V tomto příkladu vytvoříme profil Traffic Manager a přidáme dva koncové body služby App Service pomocí rutiny. `Add-AzTrafficManagerEndpointConfig`

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>Příklad 2: Přidání koncového bodu publicIpAddress pomocí`New-AzTrafficManagerEndpoint`

V tomto příkladu je prostředek veřejné IP adresy přidán do profilu Traffic Manageru. Veřejná IP adresa musí mít nakonfigurovaný název DNS a může být vázána na nic virtuálního počítače nebo na zařízení pro vyrovnávání zatížení.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Přidání externích koncových bodů

Traffic Manager používá externí koncové body k přesměrování provozu na služby hostované mimo Azure. Stejně jako u koncových bodů Azure `Add-AzTrafficManagerEndpointConfig` externí `Set-AzTrafficManagerProfile`koncové `New-AzTrafficManagerEndpoint`body lze přidat buď pomocí následovaný , nebo .

Při zadávání externích koncových bodů:

* Název domény koncového bodu musí být zadán pomocí parametru Target.
* Pokud je použita metoda směrování provozu "Výkon", je vyžadováno "EndpointLocation". V opačném případě je volitelné. Hodnota musí být [platný název oblasti Azure](https://azure.microsoft.com/regions/).
* "Hmotnost" a "Priorita" jsou volitelné.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Příklad 1: Přidání externích koncových bodů pomocí `Add-AzTrafficManagerEndpointConfig` a`Set-AzTrafficManagerProfile`

V tomto příkladu vytvoříme profil Traffic Manageru, přidáme dva externí koncové body a potvrdíme změny.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>Příklad 2: Přidání externích koncových bodů pomocí`New-AzTrafficManagerEndpoint`

V tomto příkladu přidáme externí koncový bod do existujícího profilu. Profil je určen pomocí názvů profilů a skupin prostředků.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Přidání vnořených koncových bodů

Každý profil traffic manageru určuje jednu metodu směrování provozu. Existují však scénáře, které vyžadují složitější směrování provozu než směrování poskytované jedním profilem traffic manageru. Profily Traffic Manageru můžete vnořit a kombinovat tak výhody více než jedné metody směrování provozu. Vnořené profily umožňují přepsat výchozí chování Traffic Manageru pro podporu větších a složitějších nasazení aplikací. Podrobnější příklady naleznete v [tématu Vnořené profily traffic manageru](traffic-manager-nested-profiles.md).

Vnořené koncové body jsou konfigurovány v nadřazeném profilu pomocí konkrétního typu koncového bodu "NestedEndpoints". Při zadávání vnořených koncových bodů:

* Koncový bod musí být zadán pomocí parametru targetResourceId.
* Pokud je použita metoda směrování provozu "Výkon", je vyžadováno "EndpointLocation". V opačném případě je volitelné. Hodnota musí být [platný název oblasti Azure](https://azure.microsoft.com/regions/).
* "Hmotnost" a "Priorita" jsou volitelné, stejně jako pro koncové body Azure.
* Parametr MinChildEndpoints je volitelný. Výchozí hodnota je "1". Pokud počet dostupných koncových bodů klesne pod tuto prahovou hodnotu, nadřazený profil považuje podřízený profil "degradován" a přesměruje provoz na ostatní koncové body v nadřazeném profilu.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Příklad 1: Přidání vnořených koncových bodů pomocí `Add-AzTrafficManagerEndpointConfig` a`Set-AzTrafficManagerProfile`

V tomto příkladu vytvoříme nové podřízené a nadřazené profily Traffic Manageru, přidáme dítě jako vnořený koncový bod do nadřazeného a potvrdíme změny.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

Pro stručnost v tomto příkladu jsme nepřidali žádné další koncové body do podřízeného nebo nadřazeného profilu.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>Příklad 2: Přidání vnořených koncových bodů pomocí`New-AzTrafficManagerEndpoint`

V tomto příkladu přidáme existující podřízený profil jako vnořený koncový bod do existujícího nadřazeného profilu. Profil je určen pomocí názvů profilů a skupin prostředků.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Přidání koncových bodů z jiného předplatného

Traffic Manager může pracovat s koncovými body z různých předplatných. Musíte přepnout na předplatné s koncovým bodem, který chcete přidat, abyste načetli potřebný vstup do Traffic Manageru. Pak budete muset přepnout na odběry s profilem Traffic Manager a přidat koncový bod k němu. Níže uvedený příklad ukazuje, jak to provést s veřejnou IP adresou.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Aktualizace koncového bodu traffic manageru

Existující koncový bod Traffic Manageru lze aktualizovat dvěma způsoby:

1. Získejte profil Traffic `Get-AzTrafficManagerProfile`Managerpomocí , aktualizujte vlastnosti koncového `Set-AzTrafficManagerProfile`bodu v rámci profilu a potvrďte změny pomocí . Tato metoda má tu výhodu, že je možné aktualizovat více než jeden koncový bod v jedné operaci.
2. Získejte koncový bod `Get-AzTrafficManagerEndpoint`Traffic Manageru pomocí , aktualizujte `Set-AzTrafficManagerEndpoint`vlastnosti koncového bodu a potvrďte změny pomocí . Tato metoda je jednodušší, protože nevyžaduje indexování do pole Koncové body v profilu.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>Příklad 1: Aktualizace `Get-AzTrafficManagerProfile` koncových bodů pomocí a`Set-AzTrafficManagerProfile`

V tomto příkladu změníme prioritu na dva koncové body v rámci existujícího profilu.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>Příklad 2: Aktualizace koncového bodu pomocí `Get-AzTrafficManagerEndpoint` a`Set-AzTrafficManagerEndpoint`

V tomto příkladu upravíme hmotnost jednoho koncového bodu v existujícím profilu.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Povolení a zakázání koncových bodů a profilů

Traffic Manager umožňuje jednotlivé koncové body, které mají být povoleny a zakázány, stejně jako povolení a zakázání celé profily.
Tyto změny lze provést získáním/aktualizací/nastavením prostředků koncového bodu nebo profilu. Pro zjednodušení těchto běžných operací jsou také podporovány prostřednictvím vyhrazených rutin.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Příklad 1: Povolení a zakázání profilu Traffic Manageru

Chcete-li povolit profil `Enable-AzTrafficManagerProfile`traffic manageru, použijte . Profil lze zadat pomocí objektu profilu. Objekt profilu lze předat prostřednictvím kanálu nebo pomocí parametru '-TrafficManagerProfile'. V tomto příkladu určíme profil podle profilu a názvu skupiny prostředků.

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Zakázání profilu traffic manageru:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Rutina Disable-AzTrafficManagerProfile vyzve k potvrzení. Tuto výzvu lze potlačit pomocí parametru '-Force'.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Příklad 2: Povolení a zakázání koncového bodu Traffic Manageru

Chcete-li povolit koncový `Enable-AzTrafficManagerEndpoint`bod Traffic Manageru, použijte . Koncový bod lze zadat dvěma způsoby.

1. Použití objektu TrafficManagerEndpoint předané ho potrubím nebo pomocí parametru TrafficManagerEndpoint
2. Použití názvu koncového bodu, typu koncového bodu, názvu profilu a názvu skupiny prostředků:

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Podobně zakázání koncového bodu Traffic Manager:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Stejně `Disable-AzTrafficManagerProfile`jako `Disable-AzTrafficManagerEndpoint` u , rutina vyzve k potvrzení. Tuto výzvu lze potlačit pomocí parametru '-Force'.

## <a name="delete-a-traffic-manager-endpoint"></a>Odstranění koncového bodu traffic manageru

Chcete-li odebrat jednotlivé `Remove-AzTrafficManagerEndpoint` koncové body, použijte rutinu:

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Tato rutina vyzve k potvrzení. Tuto výzvu lze potlačit pomocí parametru '-Force'.

## <a name="delete-a-traffic-manager-profile"></a>Odstranění profilu traffic managera

Chcete-li odstranit profil traffic `Remove-AzTrafficManagerProfile` manageru, použijte rutinu a zadejte názvy profilů a skupin prostředků:

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Tato rutina vyzve k potvrzení. Tuto výzvu lze potlačit pomocí parametru '-Force'.

Profil, který má být odstraněn, lze také zadat pomocí objektu profilu:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

Tato sekvence může být také potrubím:

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Další kroky

[Sledování traffic manageru](traffic-manager-monitoring.md)

[Důležité informace o výkonu nástroje Traffic Manager](traffic-manager-performance-considerations.md)
