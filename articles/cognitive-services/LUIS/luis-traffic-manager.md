---
title: Navyšte kvótu koncového bodu
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) nabízí možnost zvýšení kvóty požadavku koncového bodu nad rámec kvót jeden klíč. Uděláte to pomocí vytváření více klíčů pro LUIS a jejich přidání do aplikace LUIS na **publikovat** stránku **prostředky a klíče** oddílu.
author: diberry
manager: cgronlun
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: ecf79f5c294b7ef7d14eea49c9bd568f6921fb65
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218003"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Microsoft Azure Traffic Manager můžete používat ke správě kvóty koncový bod napříč klíče
Language Understanding (LUIS) nabízí možnost zvýšení kvóty požadavku koncového bodu nad rámec kvót jeden klíč. Uděláte to pomocí vytváření více klíčů pro LUIS a jejich přidání do aplikace LUIS na **publikovat** stránku **prostředky a klíče** oddílu. 

Klientská aplikace musí spravovat přes klíče provoz. Služba LUIS, který neprovádí. 

Tento článek vysvětluje, jak spravovat provoz v rámci klíče s Azure [Traffic Manageru][traffic-manager-marketing]. Musíte mít již trénovaného a publikované aplikace LUIS. Pokud nemáte, postupujte podle předem připravených domény [rychlý Start](luis-get-started-create-app.md). 

## <a name="connect-to-powershell-in-the-azure-portal"></a>Připojení k prostředí PowerShell na webu Azure Portal
V [Azure] [ azure-portal] portál, otevřete okno Powershellu. Ikona pro okno prostředí PowerShell je **> _** v horním navigačním panelu. Pomocí prostředí PowerShell z portálu můžete získat nejnovější verzi prostředí PowerShell a ověření. Vyžaduje prostředí PowerShell na portálu [služby Azure Storage](https://azure.microsoft.com/services/storage/) účtu. 

![Otevřít snímek obrazovky Azure Portalu se v okně prostředí Powershell](./media/traffic-manager/azure-portal-powershell.png)

V následujících částech použijte [rutin Powershellu pro Traffic Manager](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/?view=azurermps-6.2.0#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Vytvoření skupiny prostředků Azure pomocí Powershellu
Před vytvořením prostředků Azure, vytvořte skupinu prostředků obsahující všechny prostředky. Název skupiny prostředků `luis-traffic-manager` a použít je v oblasti `West US`. Oblast skupina prostředků ukládá metadata o skupině. Pokud jsou v jiné oblasti ho nebude zpomalit vaše prostředky. 

Vytvořte skupinu prostředků s **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-6.2.0)** rutiny:

```powerShell
New-AzureRmResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Vytvoření klíčů o navýšení kvóty pro celkový počet koncových bodů služby LUIS
1. Na webu Azure Portal, pak vytvoříte další dva **Language Understanding** klíčů, jeden v `West US` a druhý v `East US`. Použijte existující skupinu prostředků, vytvořili v předchozí části s názvem `luis-traffic-manager`. 

    ![Snímek obrazovky Azure Portalu se dva klíče LUIS ve skupině prostředků luis. traffic Manageru](./media/traffic-manager/luis-keys.png)

2. V [LUIS] [ LUIS] webu v **spravovat** části na **klíče a koncových bodů** stránce, přiřaďte klíče aplikace a publikovat aplikace tím, že Výběr **publikovat** tlačítko v pravé horní nabídce. 

    Příklad adresy URL v **koncový bod** sloupec používá požadavek GET s klíče koncového bodu jako parametr dotazu. Zkopírování adresy URL koncových bodů dvě nové klíče. Používají se jako součást konfigurace Traffic Manageru dále v tomto článku.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Správa požadavků na koncový bod služby LUIS napříč klíče pomocí Traffic Manageru
Traffic Manager vytvoří nový přístupový bod DNS pro vaše koncové body. Nejedná se jako brána nebo proxy, ale výhradně na úrovni DNS. V tomto příkladu se nezmění všechny záznamy DNS. Knihovna DNS používá ke komunikaci s Traffic Managerem získat správný koncový bod pro tento konkrétní požadavek. _Každý_ požadavek určený pro LUIS vyžaduje nejprve žádost o Traffic Manageru k určení koncového bodu služby LUIS, chcete-li použít. 

### <a name="polling-uses-luis-endpoint"></a>Dotazování používá koncový bod služby LUIS
Traffic Manager dotazuje koncových bodů pravidelně a ujistěte se, že koncový bod je stále k dispozici. Adresy URL Traffic Manageru dotazovaní musí být přístupné požadavek GET a vrátit 200. Adresa URL koncového bodu na **publikovat** stránky to dělá. Každý klíč koncového bodu má jinou trasy a parametrů řetězce dotazu, musí každý klíč koncového bodu dotazování na jinou cestu. Pokaždé, když Traffic Manager dotazuje, to bude stát žádost o kvótu. Parametr řetězce dotazu **q** LUIS koncový bod je utterance odesílat LUIS. Tento parametr, místo abyste odesílali utterance, se používá k přidání dotazování Traffic Manager koncový bod protokolu LUIS jako technika ladění při získávání Traffic Managerem nakonfigurovaným.

Protože každý koncový bod služby LUIS potřebuje svůj vlastní cestu, potřebuje svůj vlastní profil služby Traffic Manager. Abyste mohli spravovat profily, vytvořit [ _vnořené_ Traffic Manageru](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) architektury. Jeden nadřazený profil odkazuje na podřízené položky profily a spravovat provoz mezi nimi.

Po nakonfigurování Traffic Manager nezapomeňte změnit cestu pro protokolování = parametru řetězce dotazu na hodnotu false, váš protokol není nezaplňuje pomocí cyklického dotazování.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Nakonfigurovat vnořené profily Traffic Manageru
Následující části vytvořit dva podřízené profily, jeden pro klíč LUIS – východ a jeden pro klíč LUIS – západ. Pak se vytvoří profil nadřazený a dva podřízené profily jsou přidány do profilu nadřazené. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Vytvoření profilu Traffic Manageru východní USA pomocí prostředí PowerShell
Chcete-li vytvořit profil služby Traffic Manager USA – východ, existuje několik kroků: vytvoření profilu, přidejte koncový bod a nastavit koncový bod. Profil služby Traffic Manager může mít mnoho koncových bodů, ale každý koncový bod má stejnou cestu k ověření. Protože se liší z důvodu klíč oblasti a koncový bod adresy URL koncových bodů služby LUIS pro předplatná east a west, každý koncový bod služby LUIS musí být jeden koncový bod v profilu. 

1. Vytvoření profilu s **[New-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerprofile?view=azurermps-6.2.0)** rutiny

    Použijte tuto rutinu a vytvořte profil. Ujistěte se, že chcete-li změnit `appIdLuis` a `subscriptionKeyLuis`. SubscriptionKey určený klíč LUIS USA – východ. Pokud se cesta není správná, LUIS koncového bodu a ID klíče aplikace, včetně dotazování Traffic Manager je ve stavu `degraded` protože provozu nelze provést požadavek úspěšně koncový bod služby LUIS. Ujistěte se, že hodnota `q` je `traffic-manager-east` , zobrazí se tato hodnota v protokolech koncový bod služby LUIS.

    ```powerShell
    $eastprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    Tato tabulka popisuje každou proměnnou v rutinu:
    
    |Parametr konfigurace|Název nebo hodnota proměnné|Účel|
    |--|--|--|
    |– Název|Služba luis. profil eastus|Název Traffic Manageru na webu Azure portal|
    |– Název skupiny prostředků|Služba luis. traffic Manageru|Vytvořili v předchozí části|
    |Trafficroutingmethod – funkce|Výkon|Další informace najdete v tématu [metody směrování Traffic Manageru][routing-methods]. Pokud používáte výkonu, požadavek adresy URL Traffic Manageru musí pocházet z oblasti uživatele. Pokud probíhá chatovacího robota nebo jiné aplikace, zodpovídá za chatovací robot tak, aby napodoboval oblast ve volání do Traffic Manageru. |
    |-RelativeDnsName|Služba luis-dns-eastus|Toto je subdoména služby: luis-dns-eastus.trafficmanager.net|
    |Hodnota Ttl-|30|Interval dotazování, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port a protokol pro LUIS je HTTPS a 443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Nahraďte <appIdLuis> a <subscriptionKeyLuis> vlastními hodnotami.|
    
    Úspěšné žádosti nemá žádnou odpověď.

2. Přidat koncový bod východní USA s **[Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/add-azurermtrafficmanagerendpointconfig?view=azurermps-6.2.0)** rutiny

    ```powerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Tato tabulka popisuje každou proměnnou v rutinu:

    |Parametr konfigurace|Název nebo hodnota proměnné|Účel|
    |--|--|--|
    |-Název_koncového_bodu|koncový bod služby luis – východ|Název koncového bodu se zobrazí v rámci profilu|
    |-TrafficManagerProfile|$eastprofile|Použít profil objekt vytvořený v kroku 1|
    |– Typ|ExternalEndpoints|Další informace najdete v tématu [koncových bodů Traffic Manageru][traffic-manager-endpoints] |
    |-Cíl|eastus.API.cognitive.microsoft.com|Jedná se o doménu pro koncový bod služby LUIS.|
    |-EndpointLocation|"eastus"|Oblast koncového bodu|
    |-EndpointStatus|Povoleno|Povolení koncového bodu, když se vytvoří|

    Úspěšná odpověď vypadá takto:

    ```console
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

3. Nastavení koncového bodu východní USA s **[Set-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/set-azurermtrafficmanagerprofile?view=azurermps-6.2.0)** rutiny

    ```powerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Úspěšná odpověď bude mít stejnou odpověď v kroku 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Vytvoření profilu Traffic Manageru západní USA pomocí prostředí PowerShell
Chcete-li vytvořit profil služby Traffic Manager USA – Západ, postupujte podle stejných kroků: vytvoření profilu, přidejte koncový bod a nastavit koncový bod.

1. Vytvoření profilu s **[New-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/New-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** rutiny

    Použijte tuto rutinu a vytvořte profil. Ujistěte se, že chcete-li změnit `appIdLuis` a `subscriptionKeyLuis`. SubscriptionKey určený klíč LUIS USA – východ. Pokud cesta není správná, včetně klíče koncového bodu a ID aplikace LUIS, dotazování Traffic Manager je ve stavu `degraded` protože provozu nelze provést požadavek úspěšně koncový bod služby LUIS. Ujistěte se, že hodnota `q` je `traffic-manager-west` , zobrazí se tato hodnota v protokolech koncový bod služby LUIS.

    ```powerShell
    $westprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    Tato tabulka popisuje každou proměnnou v rutinu:
    
    |Parametr konfigurace|Název nebo hodnota proměnné|Účel|
    |--|--|--|
    |– Název|Služba luis. profil westus|Název Traffic Manageru na webu Azure portal|
    |– Název skupiny prostředků|Služba luis. traffic Manageru|Vytvořili v předchozí části|
    |Trafficroutingmethod – funkce|Výkon|Další informace najdete v tématu [metody směrování Traffic Manageru][routing-methods]. Pokud používáte výkonu, požadavek adresy URL Traffic Manageru musí pocházet z oblasti uživatele. Pokud probíhá chatovacího robota nebo jiné aplikace, zodpovídá za chatovací robot tak, aby napodoboval oblast ve volání do Traffic Manageru. |
    |-RelativeDnsName|Služba luis-dns-westus|Toto je subdoména služby: luis-dns-westus.trafficmanager.net|
    |Hodnota Ttl-|30|Interval dotazování, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port a protokol pro LUIS je HTTPS a 443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Nahraďte <appId> a <subscriptionKey> vlastními hodnotami. Mějte na paměti, že tento klíč koncového bodu se liší od klíče koncového bodu – východ|
    
    Úspěšné žádosti nemá žádnou odpověď.

2. Přidat koncový bod západní USA s **[Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** rutiny

    ```powerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Tato tabulka popisuje každou proměnnou v rutinu:

    |Parametr konfigurace|Název nebo hodnota proměnné|Účel|
    |--|--|--|
    |-Název_koncového_bodu|koncový bod služby luis – západ|Název koncového bodu se zobrazí v rámci profilu|
    |-TrafficManagerProfile|$westprofile|Použít profil objekt vytvořený v kroku 1|
    |– Typ|ExternalEndpoints|Další informace najdete v tématu [koncových bodů Traffic Manageru][traffic-manager-endpoints] |
    |-Cíl|westus.API.cognitive.microsoft.com|Jedná se o doménu pro koncový bod služby LUIS.|
    |-EndpointLocation|"westus"|Oblast koncového bodu|
    |-EndpointStatus|Povoleno|Povolení koncového bodu, když se vytvoří|

    Úspěšná odpověď vypadá takto:

    ```console
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

3. Nastavení koncového bodu západní USA s **[Set-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Set-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** rutiny

    ```powerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Úspěšná odpověď je stejnou odpověď v kroku 2.

### <a name="create-parent-traffic-manager-profile"></a>Vytvořit profil služby Traffic Manager nadřazené
Nadřazené profil služby Traffic Manager vytvořit a připojit dva podřízené profily Traffic Manageru na nadřazený prvek.

1. Vytvoření profilu nadřazené s **[New-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/New-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** rutiny

    ```powerShell
    $parentprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Tato tabulka popisuje každou proměnnou v rutinu:

    |Parametr konfigurace|Název nebo hodnota proměnné|Účel|
    |--|--|--|
    |– Název|Služba luis. profil nadřazené|Název Traffic Manageru na webu Azure portal|
    |– Název skupiny prostředků|Služba luis. traffic Manageru|Vytvořili v předchozí části|
    |Trafficroutingmethod – funkce|Výkon|Další informace najdete v tématu [metody směrování Traffic Manageru][routing-methods]. Pokud používáte výkonu, požadavek adresy URL Traffic Manageru musí pocházet z oblasti uživatele. Pokud probíhá chatovacího robota nebo jiné aplikace, zodpovídá za chatovací robot tak, aby napodoboval oblast ve volání do Traffic Manageru. |
    |-RelativeDnsName|Služba luis-dns nadřazené|Toto je subdoména služby: luis-dns-parent.trafficmanager.net|
    |Hodnota Ttl-|30|Interval dotazování, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port a protokol pro LUIS je HTTPS a 443|
    |-MonitorPath|`/`|Tuto cestu nebude vadit, protože cesty podřízeného koncového bodu se místo toho používají.|

    Úspěšné žádosti nemá žádnou odpověď.

2. Přidat profil podřízené východní USA s nadřazeným s **[Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** a **NestedEndpoints** typu

    ```powerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Tato tabulka popisuje každou proměnnou v rutinu:

    |Parametr konfigurace|Název nebo hodnota proměnné|Účel|
    |--|--|--|
    |-Název_koncového_bodu|podřízený endpoint-useast|Profil – východ|
    |-TrafficManagerProfile|$parentprofile|Profil k přiřazení tohoto koncového bodu pro|
    |– Typ|NestedEndpoints|Další informace najdete v tématu [Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0). |
    |-TargetResourceId|$eastprofile. ID|ID profilu podřízené|
    |-EndpointStatus|Povoleno|Stav koncového bodu po přidání do nadřazené|
    |-EndpointLocation|"eastus"|[Název oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/) prostředku|
    |-MinChildEndpoints|1|Minimální počet podřízených koncových bodů|

    Vzhled úspěšné odpovědi podobně jako následující a obsahuje novou `child-endpoint-useast` koncový bod:    

    ```console
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

3. Přidat profil podřízené USA – západ na hodnotu parent s **[Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** rutiny a **NestedEndpoints** typu

    ```powerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Tato tabulka popisuje každou proměnnou v rutinu:

    |Parametr konfigurace|Název nebo hodnota proměnné|Účel|
    |--|--|--|
    |-Název_koncového_bodu|podřízený endpoint-uswest|Profil – západ|
    |-TrafficManagerProfile|$parentprofile|Profil k přiřazení tohoto koncového bodu pro|
    |– Typ|NestedEndpoints|Další informace najdete v tématu [Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0). |
    |-TargetResourceId|$westprofile. ID|ID profilu podřízené|
    |-EndpointStatus|Povoleno|Stav koncového bodu po přidání do nadřazené|
    |-EndpointLocation|"westus"|[Název oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/) prostředku|
    |-MinChildEndpoints|1|Minimální počet podřízených koncových bodů|

    Vzhled úspěšné odpovědi, jako je a zahrnuje i předchozí `child-endpoint-useast` koncového bodu a nové `child-endpoint-uswest` koncový bod:

    ```console
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

4. Nastavení koncových bodů s **[Set-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Set-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** rutiny 

    ```powerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Úspěšná odpověď je stejnou odpověď v kroku 3.

### <a name="powershell-variables"></a>Proměnné prostředí PowerShell
V předchozích částech, byly vytvořeny tří proměnných prostředí PowerShell: `$eastprofile`, `$westprofile`, `$parentprofile`. Tyto proměnné se použijí na konci konfigurace Traffic Manageru. Pokud jste se rozhodli vytvořit proměnné nebo zapomněli, nebo prostředí PowerShell vyprší časový limit, můžete použít rutinu Powershellu  **[Get-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Get-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)**, abyste mohli získat profil znovu a přiřaďte ho na proměnnou. 

Nahraďte položky v lomených závorkách `<>`, s správné hodnoty pro všechny tři profily, které potřebujete. 

```powerShell
$<variable-name> = Get-AzureRmTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Zkontrolujte, jestli funguje Traffic Manageru
Chcete-li ověřit, že Traffic Manager profily, profily musí mít stav `Online` tento stav je založen na cestu k dotazování na koncový bod. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Zobrazit nové profily na webu Azure Portal
Můžete ověřit, že vytvářejí prohlédněte prostředky ve všech tří profilů `luis-traffic-manager` skupinu prostředků.

![Snímek obrazovky Azure prostředku skupiny luis – traffic-manager](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Ověřte, že stav profilu je Online
Traffic Manager dotazuje cestu každého koncového bodu, aby se zajistilo, že je online. Pokud je online, se stav podřízených profily `Online`. Tato hodnota se zobrazí na **přehled** pro každý profil. 

![Snímek obrazovky s Azure Traffic Manageru profilu – přehled zobrazení monitorování stavu z Online](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Ověření dotazování funguje Traffic Manageru
Dalším způsobem, jak ověřit traffic manager funguje cyklického dotazování je s protokoly koncového bodu služby LUIS. Na [LUIS] [ LUIS] web apps seznamu stránek, exportovat protokol koncového bodu pro aplikaci. Protože často dotazuje na dva koncové body Traffic Manageru, existují záznamy v protokolech i v případě, že pouze se na několik minut. Nezapomeňte si vyhledejte položky, kde dotaz začíná `traffic-manager-`.

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Ověřit odpověď DNS z Traffic Manager funguje
Ověřte, že odpověď DNS vrátí koncový bod služby LUIS, požádat o nadřazené profilu spravovat provoz DNS s využitím klientské knihovny služby DNS. Název DNS nadřazené profilu je `luis-dns-parent.trafficmanager.net`.

Následující kód Node.js požádá nadřazené profilu a vrátí koncový bod služby LUIS:

```nodejs
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

Úspěšná odpověď s koncovým bodem služby LUIS je:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Použití nadřazené profil Traffic Manageru
Pokud chcete spravovat provoz napříč koncovými body, je potřeba vložit volání do DNS Traffic Manageru se najít koncový bod služby LUIS. Toto volání se provádí pro každý požadavek pro koncový bod služby LUIS a potřebuje k simulaci geografické umístění uživatelů klientské aplikace LUIS. Přidejte kód odpovědi DNS mezi klientské aplikace LUIS a žádost do služby LUIS pro předpověď koncový bod. 


## <a name="clean-up"></a>Vyčištění
Odeberte dva klíče koncového bodu služby LUIS, tři profily Traffic Manageru a skupinu prostředků, který obsahoval těchto pět zdrojů. To provedete z webu Azure portal. V seznamu prostředků odstraníte pět prostředky. Potom odstraňte skupinu prostředků. 

## <a name="next-steps"></a>Další postup

Kontrola [middleware](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) možnosti v BotFramework v4 pochopit, jak tento kód pro správu provozu mohou být přidány k robotovi BotFramework. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
