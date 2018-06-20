---
title: Použít Microsoft Azure Traffic Manageru o zvýšení kvóty koncového bodu v jazyk Principy (LEOŠ) - Azure | Microsoft Docs
description: Koncový bod kvóty rozloženy několik odběrů v jazyk Principy (LEOŠ) o zvýšení kvóty koncový bod pomocí Microsoft Azure Traffic Manager
author: v-geberr
manager: kaiqb
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/07/2018
ms.author: v-geberr
ms.openlocfilehash: 513d4395b1d3e631855c2f6e132d54331b3ddf8d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266341"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Použít Microsoft Azure Traffic Manager ke správě koncový bod kvóty napříč klíče
Principy jazyka (LEOŠ) nabízí možnost o zvýšení kvóty požadavku koncového bodu nad rámec kvóty jeden klíč. K tomu je potřeba vytváření více klíčů pro LEOŠ a jejich přidáním do aplikace LEOŠ na **publikovat** stránku **prostředky a klíče** části. 

Klientská aplikace má ke správě přenosů mezi klíče. LEOŠ není udělat. 

Tento článek vysvětluje, jak spravovat provoz v rámci klíčů s Azure [Traffic Manager][traffic-manager-marketing]. Musíte už mít vyškolení a publikované aplikace LEOŠ. Pokud nemáte jeden, postupujte podle předem domény [rychlý Start](luis-get-started-create-app.md). 

## <a name="connect-to-powershell-in-the-azure-portal"></a>Připojení k prostředí PowerShell na portálu Azure
V [Azure] [ azure-portal] portál, otevřete okno PowerShell. Ikona pro okno prostředí PowerShell je **> _** v horním navigačním panelu. Pomocí prostředí PowerShell z portálu získat nejnovější verzi prostředí PowerShell a jsou ověřeni. Vyžaduje prostředí PowerShell na portálu [Azure Storage](https://azure.microsoft.com/services/storage/) účtu. 

![Otevřete portál snímek Azure, okno prostředí Powershell](./media/traffic-manager/azure-portal-powershell.png)

V následujících částech použijte [rutiny prostředí PowerShell Traffic Manager](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/?view=azurermps-6.2.0#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Vytvoření skupiny prostředků Azure pomocí prostředí PowerShell
Před vytvořením prostředky Azure, vytvořte skupinu prostředků tak, aby obsahovala všechny prostředky. Název skupiny prostředků `luis-traffic-manager` a použít je oblast `West US`. Oblast skupiny prostředků ukládá metadata o skupině. Pokud jsou v jiné oblasti ho nebude zpomalit vaše prostředky. 

Vytvořte skupinu prostředků s **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-6.2.0)** rutiny:

```PowerShell
New-AzureRmResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Vytvoření LEOŠ klíčů o zvýšení kvóty celkový koncový bod
1. Na portálu Azure vytvořit dva **znalosti jazyka** klíče, jeden v `West US` a po jednom v `East US`. Použít existující skupinu prostředků, vytvořili v předchozí části s názvem `luis-traffic-manager`. 

    ![Snímek obrazovky Azure portál dva klíče LEOŠ ve skupině prostředků Leoš správce provozu](./media/traffic-manager/luis-keys.png)

2. V [LEOŠ] [ LUIS] webu na **publikovat** stránky, přidání klíče do aplikace a publikovat aplikace. 

    ![Snímek obrazovky LEOŠ portál dva klíče LEOŠ na stránky publikování](./media/traffic-manager/luis-keys-in-luis.png)

    Příklad adresy URL v **koncový bod** sloupec používá požadavek GET s klíč předplatného jako parametr dotazu. Zkopírujte adres URL koncového bodu dva nové klíče. Používají se jako součást konfigurace Traffic Manager později v tomto článku.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Spravovat požadavky na koncový bod LEOŠ napříč klíče pomocí nástroje Traffic Manager
Traffic Manager se vytvoří nový bod přístup DNS pro koncové body. Nejedná se jako brána nebo proxy, ale výhradně na úrovni DNS. Tento příklad nezmění žádné záznamy DNS. Knihovna DNS používá ke komunikaci s nástrojem Traffic Manager získat správný koncový bod pro tuto konkrétní žádost. _Každý_ žádost určená pro LEOŠ vyžaduje nejprve Traffic Manager požadavku k určení kterému koncovému bodu LEOŠ používat. 

### <a name="polling-uses-luis-endpoint"></a>Dotazování používá LEOŠ koncový bod
Traffic Manager dotazovat koncové body pravidelně a ujistěte se, že koncový bod je stále k dispozici. Adresa URL správce provozu dotazování musí být přístupné pomocí požadavek GET a vrátíte se 200. Adresu URL koncového bodu na **publikovat** stránky k tomu. Vzhledem k tomu, že každý klíč k odběru má jinou cestu a parametrů řetězce dotazu, musí každý klíč předplatného cestu k jiné dotazování. Pokaždé, když Traffic Manager dotazovat, ho nákladů žádost o kvótu. Parametr řetězce dotazu **q** LEOŠ koncový bod je utterance posílá LEOŠ. Tento parametr, místo abyste odesílali utterance, slouží k přidat Traffic Manager dotazování na koncový bod protokolu LEOŠ jako technika ladění při získávání Traffic Manager nakonfigurován.

Protože každý koncový bod LEOŠ vyžaduje vlastní cestu, musí vlastní profil služby Traffic Manager. Abyste mohli spravovat mezi profily, vytvoření [ _vnořené_ Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) architektura. Jeden profil nadřazené odkazuje na podřízené objekty profily a správě provozu mezi nimi.

Po nakonfigurování Traffic Manager nezapomeňte změnit cestu k na protokolování = parametr řetězce dotazu false, takže váš protokol není naplňování s dotazování.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Nakonfigurovat vnořených profilů Traffic Manager
Následující části vytvořit dva profily podřízené, jeden pro klíč LEOŠ – východ a jeden pro klíč LEOŠ – západ. Potom nadřazené profil je vytvořen a dva podřízené profily jsou přidány do profilu nadřazené. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Vytvořit profil služby Traffic Manager východní USA pomocí prostředí PowerShell
Pokud chcete vytvořit profil služby Traffic Manager Východ USA, existuje několik kroků: vytvoření profilu, přidání koncového bodu a nastavte koncový bod. Profil správce provozu může mít velký počet koncových bodů, ale každý koncový bod má stejné cesty k ověření. Vzhledem k adres URL koncového bodu LEOŠ odběrů east a west jsou různé kvůli oblasti a předplatné klíče, každý koncový bod LEOŠ musí být jeden koncový bod v profilu. 

1. Vytvoření profilu s **[New-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerprofile?view=azurermps-6.2.0)** rutiny

    Pomocí následující rutiny můžete vytvořit profil. Ujistěte se, chcete-li změnit `appIdLuis` a `subscriptionKeyLuis`. SubscriptionKey je pro klíč LEOŠ USA – východ. Pokud cesta není správný, včetně LEOŠ app ID a předplatné klíče, dotazování Traffic Manager je ve stavu `degraded` protože spravovat přenosy nelze úspěšně koncový bod LEOŠ vyžádat. Ujistěte se, hodnota `q` je `traffic-manager-east` , zobrazí se tato hodnota v protokolech LEOŠ koncový bod.

    ```PowerShell
    $eastprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    Tato tabulka vysvětluje každou proměnnou v rutinu:
    
    |Parametr konfigurace|Název proměnné nebo hodnota|Účel|
    |--|--|--|
    |-Název|Leoš. profil eastus|Název Traffic Manageru na portálu Azure|
    |-ResourceGroupName|Leoš správce provozu|Vytvořené v předchozí části|
    |-TrafficRoutingMethod|Výkon|Další informace najdete v tématu [metody směrování Traffic Manageru][routing-methods]. Pokud používáte výkonu, požadavek adresy URL do Traffic Manageru musí pocházet z oblasti uživatele. Pokud projít chatbot nebo jiné aplikace, je chatbot odpovědnost tak, aby napodoboval oblasti ve volání do Traffic Manageru. |
    |-RelativeDnsName|Leoš. dns eastus|Toto je subdoménou pro službu: Leoš. dns eastus.trafficmanager.net|
    |-Hodnota Ttl|30|Interval dotazování, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port a protokol pro LEOŠ je HTTPS nebo 443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Nahraďte <appIdLuis> a <subscriptionKeyLuis> vlastními hodnotami.|
    
    V případě úspěšné žádosti nemá žádná odpověď.

2. Přidání koncového bodu východní USA s **[přidat AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/add-azurermtrafficmanagerendpointconfig?view=azurermps-6.2.0)** rutiny

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Tato tabulka vysvětluje každou proměnnou v rutinu:

    |Parametr konfigurace|Název proměnné nebo hodnota|Účel|
    |--|--|--|
    |-EndpointName|koncový bod Leoš – východ|Název koncového bodu se zobrazí v části profil|
    |-TrafficManagerProfile|$eastprofile|Použít profil objekt vytvořený v kroku 1|
    |– Typ|ExternalEndpoints|Další informace najdete v tématu [koncový bod Traffic Manager][traffic-manager-endpoints] |
    |-Cíl|eastus.API.cognitive.microsoft.com|Jedná se o doménu pro koncový bod LEOŠ.|
    |-EndpointLocation|"eastus"|Oblast koncového bodu|
    |-EndpointStatus|Povoleno|Povolení koncového bodu, když je vytvořeno|

    Úspěšná odpověď bude vypadat takto:

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. Nastavit koncový bod východní USA s **[Set-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/set-azurermtrafficmanagerprofile?view=azurermps-6.2.0)** rutiny

    ```PowerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Úspěšná odpověď bude stejné jako krok 2 odpověď.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Vytvořit profil služby Traffic Manager západní USA pomocí prostředí PowerShell
Pokud chcete vytvořit profil služby Traffic Manager západní USA, použít stejný postup: vytvoření profilu, přidání koncového bodu a nastavte koncový bod.

1. Vytvoření profilu s **[New-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/New-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** rutiny

    Pomocí následující rutiny můžete vytvořit profil. Ujistěte se, chcete-li změnit `appIdLuis` a `subscriptionKeyLuis`. SubscriptionKey je pro klíč LEOŠ USA – východ. Pokud cesta není správný, včetně aplikace LEOŠ klíče ID a předplatné, dotazování Traffic Manager je ve stavu `degraded` protože spravovat přenosy nelze úspěšně koncový bod LEOŠ vyžádat. Ujistěte se, hodnota `q` je `traffic-manager-west` , zobrazí se tato hodnota v protokolech LEOŠ koncový bod.

    ```PowerShell
    $westprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    Tato tabulka vysvětluje každou proměnnou v rutinu:
    
    |Parametr konfigurace|Název proměnné nebo hodnota|Účel|
    |--|--|--|
    |-Název|Leoš. profil westus|Název Traffic Manageru na portálu Azure|
    |-ResourceGroupName|Leoš správce provozu|Vytvořené v předchozí části|
    |-TrafficRoutingMethod|Výkon|Další informace najdete v tématu [metody směrování Traffic Manageru][routing-methods]. Pokud používáte výkonu, požadavek adresy URL do Traffic Manageru musí pocházet z oblasti uživatele. Pokud projít chatbot nebo jiné aplikace, je chatbot odpovědnost tak, aby napodoboval oblasti ve volání do Traffic Manageru. |
    |-RelativeDnsName|Leoš. dns westus|Toto je subdoménou pro službu: Leoš. dns westus.trafficmanager.net|
    |-Hodnota Ttl|30|Interval dotazování, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port a protokol pro LEOŠ je HTTPS nebo 443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Nahraďte <appId> a <subscriptionKey> vlastními hodnotami. Mějte na paměti, že tento klíč předplatného se liší od klíč předplatného – východ|
    
    V případě úspěšné žádosti nemá žádná odpověď.

2. Přidání koncového bodu západní USA s **[přidat AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** rutiny

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Tato tabulka vysvětluje každou proměnnou v rutinu:

    |Parametr konfigurace|Název proměnné nebo hodnota|Účel|
    |--|--|--|
    |-EndpointName|koncový bod Leoš – západ|Název koncového bodu se zobrazí v části profil|
    |-TrafficManagerProfile|$westprofile|Použít profil objekt vytvořený v kroku 1|
    |– Typ|ExternalEndpoints|Další informace najdete v tématu [koncový bod Traffic Manager][traffic-manager-endpoints] |
    |-Cíl|westus.API.cognitive.microsoft.com|Jedná se o doménu pro koncový bod LEOŠ.|
    |-EndpointLocation|"westus"|Oblast koncového bodu|
    |-EndpointStatus|Povoleno|Povolení koncového bodu, když je vytvořeno|

    Úspěšná odpověď bude vypadat takto:

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. Nastavit koncový bod západní USA s **[Set-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Set-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** rutiny

    ```PowerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Úspěšná odpověď je stejný jako krok 2 odpověď.

### <a name="create-parent-traffic-manager-profile"></a>Vytvořit profil služby Traffic Manager nadřazené
Vytvořte nadřazenou profil služby Traffic Manager a propojit s nadřazeným dva profily Traffic Manager podřízené.

1. Vytvoření profilu nadřazené s **[New-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/New-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** rutiny

    ```PowerShell
    $parentprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Tato tabulka vysvětluje každou proměnnou v rutinu:

    |Parametr konfigurace|Název proměnné nebo hodnota|Účel|
    |--|--|--|
    |-Název|Nadřazený profil Leoš-|Název Traffic Manageru na portálu Azure|
    |-ResourceGroupName|Leoš správce provozu|Vytvořené v předchozí části|
    |-TrafficRoutingMethod|Výkon|Další informace najdete v tématu [metody směrování Traffic Manageru][routing-methods]. Pokud používáte výkonu, požadavek adresy URL do Traffic Manageru musí pocházet z oblasti uživatele. Pokud projít chatbot nebo jiné aplikace, je chatbot odpovědnost tak, aby napodoboval oblasti ve volání do Traffic Manageru. |
    |-RelativeDnsName|Nadřazený dns Leoš-|Toto je subdoménou pro službu: Leoš. dns parent.trafficmanager.net|
    |-Hodnota Ttl|30|Interval dotazování, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port a protokol pro LEOŠ je HTTPS nebo 443|
    |-MonitorPath|`/`|Tato cesta není důležité, protože místo toho používá cesty podřízené koncový bod.|

    V případě úspěšné žádosti nemá žádná odpověď.

2. Přidání profilu podřízené východní USA s nadřazenou položkou s **[přidat AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** a **NestedEndpoints** typu

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Tato tabulka vysvětluje každou proměnnou v rutinu:

    |Parametr konfigurace|Název proměnné nebo hodnota|Účel|
    |--|--|--|
    |-EndpointName|podřízené. koncový bod useast|Profil – východ|
    |-TrafficManagerProfile|$parentprofile|Profil přiřadit tento koncový bod|
    |– Typ|NestedEndpoints|Další informace najdete v tématu [přidat AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0). |
    |-TargetResourceId|$eastprofile. ID|ID podřízené profilu|
    |-EndpointStatus|Povoleno|Stav koncového bodu po přidání do nadřazené|
    |-EndpointLocation|"eastus"|[Název oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/) prostředku|
    |-MinChildEndpoints|1|Minimální počet podřízených koncových bodů|

    Úspěšná odpověď vypadá podobně jako následující a zahrnuje nové `child-endpoint-useast` koncový bod:    

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. Přidání profilu podřízené západní USA s nadřazenou položkou s **[přidat AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** rutiny a **NestedEndpoints** typu

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Tato tabulka vysvětluje každou proměnnou v rutinu:

    |Parametr konfigurace|Název proměnné nebo hodnota|Účel|
    |--|--|--|
    |-EndpointName|podřízené. koncový bod uswest|Profil – západ|
    |-TrafficManagerProfile|$parentprofile|Profil přiřadit tento koncový bod|
    |– Typ|NestedEndpoints|Další informace najdete v tématu [přidat AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0). |
    |-TargetResourceId|$westprofile. ID|ID podřízené profilu|
    |-EndpointStatus|Povoleno|Stav koncového bodu po přidání do nadřazené|
    |-EndpointLocation|"westus"|[Název oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/) prostředku|
    |-MinChildEndpoints|1|Minimální počet podřízených koncových bodů|

    Jak vypadá úspěšná odpověď jako a zahrnuje obě předchozí `child-endpoint-useast` koncový bod a nové `child-endpoint-uswest` koncový bod:

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. Nastavit koncové body pomocí **[Set-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Set-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** rutiny 

    ```PowerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Úspěšná odpověď je stejný jako krok 3 odpověď.

### <a name="powershell-variables"></a>Proměnné prostředí PowerShell
V předchozích částech byly vytvořeny tři proměnné prostředí PowerShell: `$eastprofile`, `$westprofile`, `$parentprofile`. Tyto proměnné se používají ke konci konfigurace Traffic Manager. Pokud jste vytvoření proměnné, nebo problém zapomněl nebo prostředí PowerShell časového limitu, můžete použít rutinu prostředí PowerShell  **[Get-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Get-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)**, abyste mohli získat profil znovu a přiřaďte ho k proměnné. 

Nahrazení položek v lomených závorkách `<>`, s správné hodnoty pro každý ze tří profilů potřebujete. 

```PowerShell
$<variable-name> = Get-AzureRmTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Ověřte Traffic Manager funguje
Chcete-li zkontrolovat, jestli pracují profily Traffic Manager, profily musí mít stav `Online` tento stav je založen na cestě dotazování koncového bodu. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Zobrazení nové profily na portálu Azure
Můžete ověřit, že všechny tři profily jsou vytvořeny pohledem na prostředky v `luis-traffic-manager` skupinu prostředků.

![Snímek obrazovky Azure skupiny Leoš provoz správce prostředků](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Ověřte, že stav profilu je Online
Traffic Manager dotazovat cestu každý koncový bod a ujistěte se, že je online. Pokud je online, stav podřízených profily jsou `Online`. To se zobrazí na **přehled** pro každý profil. 

![Snímek obrazovky Azure Traffic Manager profil přehled zobrazující monitorování stavu z Online](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Ověření Traffic Manager dotazování funguje
Jiný způsob, jak ověřit traffic Manageru dotazování funguje je s protokoly LEOŠ koncový bod. Na [LEOŠ] [ LUIS] seznam aplikací webu stránky, exportovat koncový bod protokolu pro aplikaci. Protože Traffic Manager se dotazuje často na dva koncové body, existují záznamy v protokolech i v případě, že byly jenom na několik minut. Nezapomeňte vyhledejte položky, kde dotaz začíná `traffic-manager-`.

```text
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Ověřit odpověď DNS z Traffic Manager funguje
K ověření, že odpověď DNS vrátí koncový bod LEOŠ, žádost profilem nadřazené spravovat přenosy DNS pomocí klientské knihovny DNS. DNS název pro profil nadřazené je `luis-dns-parent.trafficmanager.net`.

Následující kód Node.js požádá profilu nadřazené a vrátí koncový bod LEOŠ:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

Úspěšná odpověď s koncovým bodem LEOŠ je:

```cmd
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Použití nadřazené profil služby Traffic Manager
Abyste mohli spravovat přenosy napříč koncovými body, je třeba vložit volání do DNS Traffic Manager najít LEOŠ koncový bod. Toto volání se provádí pro každou žádost LEOŠ koncový bod a je třeba k simulaci zeměpisnou polohu uživatele LEOŠ klientské aplikace. Přidejte kód odpovědi DNS mezi klientské aplikace LEOŠ a žádosti do LEOŠ pro předpověď koncový bod. 


## <a name="clean-up"></a>Vyčištění
Odeberte dva klíče LEOŠ předplatné, tři profily Traffic Manageru a skupině prostředků, která obsahovala těchto pět prostředků. To se provádí z portálu Azure. Odstranit pět prostředky ze seznamu prostředků. Potom odstraňte skupinu prostředků. 

## <a name="next-steps"></a>Další postup

Zkontrolujte [middleware](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) možnosti v BotFramework v4 pochopit, jak tento kód provoz správy mohou být přidány do BotFramework robota. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
