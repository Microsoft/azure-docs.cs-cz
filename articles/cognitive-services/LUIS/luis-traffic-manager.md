---
title: Zvýšení kvóty koncového bodu – LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) nabízí možnost zvýšit kvótu požadavku koncového bodu nad rámec kvóty s jedním klíčem. To se provádí vytvořením dalších klíčů pro LUIS a jejich přidáním do aplikace LUIS na stránce **publikovat** v části **prostředky a klíče** .
manager: nitinme
ms.custom: seodec18, devx-track-js, devx-track-azurepowershell
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/20/2019
ms.openlocfilehash: 37c4bd2af080a76e93bc9599f06e4d502985979f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609647"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Použití Microsoft Azure Traffic Manager ke správě kvóty koncových bodů napříč klíči
Language Understanding (LUIS) nabízí možnost zvýšit kvótu požadavku koncového bodu nad rámec kvóty s jedním klíčem. To se provádí vytvořením dalších klíčů pro LUIS a jejich přidáním do aplikace LUIS na stránce **publikovat** v části **prostředky a klíče** .

Klientská aplikace musí spravovat provoz napříč klíči. LUIS to neudělá.

Tento článek vysvětluje, jak spravovat provoz napříč klíči pomocí Azure [Traffic Manager][traffic-manager-marketing]. Musíte mít již naučenou a publikovanou aplikaci LUIS. Pokud ho nemáte, postupujte předem vytvořeným [rychlým startem](luis-get-started-create-app.md)domény.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Připojení k PowerShellu v Azure Portal
Na webu [Azure][azure-portal] Portal otevřete okno PowerShellu. Ikona okna PowerShellu je **>_** v horním navigačním panelu. Pomocí prostředí PowerShell z portálu získáte nejnovější verzi prostředí PowerShell a jste ověřeni. Prostředí PowerShell na portálu vyžaduje účet [Azure Storage](https://azure.microsoft.com/services/storage/) .

![Snímek obrazovky Azure Portal s otevřeným oknem PowerShell](./media/traffic-manager/azure-portal-powershell.png)

Následující části používají [Traffic Manager rutiny PowerShellu](/powershell/module/az.trafficmanager/#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Vytvoření skupiny prostředků Azure pomocí PowerShellu
Před vytvořením prostředků Azure vytvořte skupinu prostředků, která bude obsahovat všechny prostředky. Pojmenujte skupinu prostředků `luis-traffic-manager` a použijte oblast `West US` . V oblasti skupiny prostředků se ukládají metadata o této skupině. Nezpomaluje vaše prostředky, pokud jsou v jiné oblasti.

Vytvořte skupinu prostředků pomocí rutiny **[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)** :

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Vytvoření klíčů LUIS pro zvýšení celkové kvóty koncového bodu
1. V Azure Portal vytvořte dva klíče **Language Understanding** , jednu v `West US` a jednu v `East US` . Použijte existující skupinu prostředků vytvořenou v předchozí části s názvem `luis-traffic-manager` .

    ![Snímek obrazovky Azure Portal se dvěma klíči LUIS ve skupině prostředků Luis-Traffic-Manager](./media/traffic-manager/luis-keys.png)

2. Na webu [Luis][LUIS] v části **Spravovat** na stránce **prostředky Azure** přiřaďte klíče k aplikaci a znovu publikujte aplikaci, a to tak, že v pravé horní nabídce kliknete na tlačítko **publikovat** .

    Ukázková adresa URL ve sloupci **koncový bod** používá jako parametr dotazu požadavek GET s klíčem koncového bodu. Zkopírujte adresy URL koncového bodu pro dva nové klíče. Používají se jako součást konfigurace Traffic Manager dále v tomto článku.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Správa požadavků na koncový bod LUIS napříč klíči pomocí Traffic Manager
Traffic Manager vytvoří nový přístupový bod DNS pro koncové body. Nefunguje jako brána nebo proxy server, ale výhradně na úrovni DNS. Tento příklad nemění žádné záznamy DNS. Pomocí knihovny DNS komunikuje se Traffic Manager a získá správný koncový bod pro konkrétní požadavek. _Každý_ požadavek určený pro Luis nejdřív vyžaduje Traffic Manager žádost o určení, který koncový bod Luis se má použít.

### <a name="polling-uses-luis-endpoint"></a>Cyklické dotazování používá koncový bod LUIS.
Traffic Manager se pravidelně dotazují koncové body, aby se zajistilo, že je koncový bod stále k dispozici. Dotazovaná adresa URL Traffic Manager musí být přístupná pomocí žádosti GET a vracet 200. To dělá adresa URL koncového bodu na stránce **publikovat** . Vzhledem k tomu, že každý klíč koncového bodu má jiný parametr trasy a řetězce dotazů, musí mít každý klíč koncového bodu jinou cestu cyklického dotazování. Pokaždé, když se Traffic Manager dotazuje, účtuje náklady a požadavky na kvótu. Parametr řetězce dotazu **q** koncového bodu Luis je utterance odesílaný do Luis. Tento parametr namísto odeslání utterance slouží k přidání Traffic Manager cyklického dotazování do protokolu koncového bodu LUIS jako techniky ladění při získávání Traffic Manager konfigurace.

Vzhledem k tomu, že každý koncový bod LUIS potřebuje svou vlastní cestu, potřebuje svůj vlastní profil Traffic Manager. Aby bylo možné spravovat napříč profily, vytvořte [ _vnořenou_ Traffic Manager](../../traffic-manager/traffic-manager-nested-profiles.md) architekturu. Jeden nadřazený profil odkazuje na podřízené profily a umožňuje spravovat provoz napříč nimi.

Po nakonfigurování Traffic Manager nezapomeňte změnit cestu k použití parametru řetězce dotazu Logging = false, takže se protokol neplní dotazem.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Konfigurace Traffic Manager s vnořenými profily
V následujících částech se vytvoří dva podřízené profily, jeden pro klávesu východní LUIS a druhý pro klíč západní LUIS. Pak se vytvoří nadřazený profil a do nadřazeného profilu se přidají dva podřízené profily.

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Vytvoření profilu Východní USA Traffic Manager pomocí prostředí PowerShell
Chcete-li vytvořit profil Východní USA Traffic Manager, je k dispozici několik kroků: vytvořit profil, přidat koncový bod a nastavit koncový bod. Profil Traffic Manager může mít mnoho koncových bodů, ale každý koncový bod má stejnou cestu ověřování. Vzhledem k tomu, že se adresy URL koncového bodu LUIS pro odběry východ a západ liší v důsledku oblasti a klíče koncového bodu, musí být každý koncový bod LUIS v profilu jeden koncový bod.

1. Vytvoření profilu pomocí rutiny **[New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)**

    K vytvoření profilu použijte následující rutinu. Nezapomeňte změnit `appIdLuis` a `subscriptionKeyLuis` . SubscriptionKey je určen pro klíč LUIS Východní USA. Pokud cesta není správná, včetně ID aplikace LUIS a klíče koncového bodu, dotazování Traffic Manager je stav, `degraded` protože správa provozu nemůže úspěšně požádat o koncový bod Luis. Ujistěte se, že hodnota `q` je `traffic-manager-east` , takže tuto hodnotu můžete zobrazit v protokolech koncového bodu Luis.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```

    Tato tabulka vysvětluje každou proměnnou v rutině:

    |Konfigurační parametr|Název nebo hodnota proměnné|Účel|
    |--|--|--|
    |-Name|Luis-Profile-eastus|Název Traffic Manager v Azure Portal|
    |– ResourceGroupName|Luis – Traffic Manager|Vytvořeno v předchozí části|
    |-TrafficRoutingMethod|Výkon|Další informace najdete v tématu [metody směrování Traffic Manager][routing-methods]. Pokud používáte výkon, požadavek adresy URL na Traffic Manager musí pocházet z oblasti uživatele. Pokud přecházíte přes chatovací robot nebo jinou aplikaci, je zodpovědností chatovací robot napodobení oblasti ve volání Traffic Manager. |
    |-RelativeDnsName|Luis-DNS-eastus|Toto je subdoména této služby: luis-dns-eastus.trafficmanager.net|
    |-TTL|30|Interval dotazování, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port a protokol pro LUIS jsou HTTPS/443.|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Nahraďte `<appIdLuis>` a `<subscriptionKeyLuis>` vlastními hodnotami.|

    Úspěšná žádost nemá žádnou odpověď.

2. Přidat Východní USA koncový bod pomocí rutiny **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Tato tabulka vysvětluje každou proměnnou v rutině:

    |Konfigurační parametr|Název nebo hodnota proměnné|Účel|
    |--|--|--|
    |-Koncový bod|Luis – východ – koncový bod|Název koncového bodu zobrazený pod profilem|
    |-TrafficManagerProfile|$eastprofile|Použití objektu Profile vytvořeného v kroku 1|
    |– Typ|ExternalEndpoints|Další informace najdete v tématu [Traffic Manager koncový bod][traffic-manager-endpoints] . |
    |– Cíl|eastus.api.cognitive.microsoft.com|Toto je doména pro koncový bod LUIS.|
    |-EndpointLocation|eastus|Oblast koncového bodu|
    |-EndpointStatus|Povoleno|Povolit koncový bod při jeho vytvoření|

    Úspěšná odpověď vypadá nějak takto:

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

3. Nastavit Východní USA koncový bod pomocí rutiny **[set-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Úspěšná odpověď bude stejná jako odpověď v kroku 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Vytvoření profilu Západní USA Traffic Manager pomocí prostředí PowerShell
Pokud chcete vytvořit profil Západní USA Traffic Manager, postupujte podle stejných kroků: vytvořit profil, přidat koncový bod a nastavit koncový bod.

1. Vytvoření profilu pomocí rutiny **[New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    K vytvoření profilu použijte následující rutinu. Nezapomeňte změnit `appIdLuis` a `subscriptionKeyLuis` . SubscriptionKey je určen pro klíč LUIS Východní USA. Pokud cesta není správná, včetně ID aplikace LUIS a klíče koncového bodu, dotazování Traffic Manager je stav, `degraded` protože správa provozu nemůže úspěšně vyžadovat koncový bod Luis. Ujistěte se, že hodnota `q` je `traffic-manager-west` , takže tuto hodnotu můžete zobrazit v protokolech koncového bodu Luis.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```

    Tato tabulka vysvětluje každou proměnnou v rutině:

    |Konfigurační parametr|Název nebo hodnota proměnné|Účel|
    |--|--|--|
    |-Name|Luis-Profile-westus|Název Traffic Manager v Azure Portal|
    |– ResourceGroupName|Luis – Traffic Manager|Vytvořeno v předchozí části|
    |-TrafficRoutingMethod|Výkon|Další informace najdete v tématu [metody směrování Traffic Manager][routing-methods]. Pokud používáte výkon, požadavek adresy URL na Traffic Manager musí pocházet z oblasti uživatele. Pokud přecházíte přes chatovací robot nebo jinou aplikaci, je zodpovědností chatovací robot napodobení oblasti ve volání Traffic Manager. |
    |-RelativeDnsName|Luis-DNS-westus|Toto je subdoména této služby: luis-dns-westus.trafficmanager.net|
    |-TTL|30|Interval dotazování, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port a protokol pro LUIS jsou HTTPS/443.|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Nahraďte `<appId>` a `<subscriptionKey>` vlastními hodnotami. Zapamatujte si, že tento klíč koncového bodu je jiný než klíč koncového bodu východ|

    Úspěšná žádost nemá žádnou odpověď.

2. Přidat Západní USA koncový bod pomocí rutiny **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Tato tabulka vysvětluje každou proměnnou v rutině:

    |Konfigurační parametr|Název nebo hodnota proměnné|Účel|
    |--|--|--|
    |-Koncový bod|Luis – západ – koncový bod|Název koncového bodu zobrazený pod profilem|
    |-TrafficManagerProfile|$westprofile|Použití objektu Profile vytvořeného v kroku 1|
    |– Typ|ExternalEndpoints|Další informace najdete v tématu [Traffic Manager koncový bod][traffic-manager-endpoints] . |
    |– Cíl|westus.api.cognitive.microsoft.com|Toto je doména pro koncový bod LUIS.|
    |-EndpointLocation|westus|Oblast koncového bodu|
    |-EndpointStatus|Povoleno|Povolit koncový bod při jeho vytvoření|

    Úspěšná odpověď vypadá nějak takto:

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

3. Nastavit Západní USA koncový bod pomocí rutiny **[set-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Úspěšná odpověď je stejná jako odpověď v kroku 2.

### <a name="create-parent-traffic-manager-profile"></a>Vytvořit nadřazený profil Traffic Manager
Vytvořte nadřazený profil Traffic Manager a propojte dva podřízené Traffic Manager profily s nadřazeným prvkem.

1. Vytvoření nadřazeného profilu pomocí rutiny **[New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Tato tabulka vysvětluje každou proměnnou v rutině:

    |Konfigurační parametr|Název nebo hodnota proměnné|Účel|
    |--|--|--|
    |-Name|Luis-Profile-nadřazený|Název Traffic Manager v Azure Portal|
    |– ResourceGroupName|Luis – Traffic Manager|Vytvořeno v předchozí části|
    |-TrafficRoutingMethod|Výkon|Další informace najdete v tématu [metody směrování Traffic Manager][routing-methods]. Pokud používáte výkon, požadavek adresy URL na Traffic Manager musí pocházet z oblasti uživatele. Pokud přecházíte přes chatovací robot nebo jinou aplikaci, je zodpovědností chatovací robot napodobení oblasti ve volání Traffic Manager. |
    |-RelativeDnsName|Luis-DNS – nadřazený|Toto je subdoména této služby: luis-dns-parent.trafficmanager.net|
    |-TTL|30|Interval dotazování, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port a protokol pro LUIS jsou HTTPS/443.|
    |-MonitorPath|`/`|Tato cesta nezáleží na tom, že se místo toho používají podřízené cesty koncových bodů.|

    Úspěšná žádost nemá žádnou odpověď.

2. Přidání podřízeného profilu Východní USA k nadřazené položce s typem **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** a **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Tato tabulka vysvětluje každou proměnnou v rutině:

    |Konfigurační parametr|Název nebo hodnota proměnné|Účel|
    |--|--|--|
    |-Koncový bod|podřízený koncový bod – useast|Profil pro východní jazyky|
    |-TrafficManagerProfile|$parentprofile|Profil, kterému chcete přiřadit tento koncový bod|
    |– Typ|NestedEndpoints|Další informace najdete v tématu [Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |– Parametrem targetresourceid|$eastprofile. Účet|ID podřízeného profilu|
    |-EndpointStatus|Povoleno|Stav koncového bodu po přidání do nadřazeného objektu|
    |-EndpointLocation|eastus|[Název oblasti prostředku Azure](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|Minimální počet podřízených koncových bodů|

    Úspěšná odpověď vypadá jako následující a zahrnuje nový `child-endpoint-useast` koncový bod:

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

3. Přidání podřízeného profilu Západní USA k nadřazené položce pomocí rutiny **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** a **NestedEndpoints** typu

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Tato tabulka vysvětluje každou proměnnou v rutině:

    |Konfigurační parametr|Název nebo hodnota proměnné|Účel|
    |--|--|--|
    |-Koncový bod|podřízený koncový bod – uswest|Profil západ|
    |-TrafficManagerProfile|$parentprofile|Profil, kterému chcete přiřadit tento koncový bod|
    |– Typ|NestedEndpoints|Další informace najdete v tématu [Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |– Parametrem targetresourceid|$westprofile. Účet|ID podřízeného profilu|
    |-EndpointStatus|Povoleno|Stav koncového bodu po přidání do nadřazeného objektu|
    |-EndpointLocation|westus|[Název oblasti prostředku Azure](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|Minimální počet podřízených koncových bodů|

    Úspěšná odpověď vypadá jako a zahrnuje předchozí `child-endpoint-useast` koncový bod i nový `child-endpoint-uswest` koncový bod:

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

4. Nastavení koncových bodů pomocí rutiny **[set-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Úspěšná odpověď je stejná jako odpověď v kroku 3.

### <a name="powershell-variables"></a>Proměnné PowerShellu
V předchozích částech byly vytvořeny tři proměnné prostředí PowerShell: `$eastprofile` , `$westprofile` , `$parentprofile` . Tyto proměnné se používají ke konci konfigurace Traffic Manager. Pokud jste se rozhodli nevytvářet proměnné nebo jste zapomněli, nebo časový limit okna PowerShellu vypršel, můžete použít rutinu PowerShellu **[Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)** a získat profil znovu a přiřadit ho k proměnné.

Položky v lomených závorkách nahraďte `<>` správnými hodnotami pro každý ze tří potřebných profilů.

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Ověření Traffic Manager funguje
Aby bylo možné ověřit, že profily Traffic Manager fungují, musí mít profily stav `Online` tohoto stavu na základě cesty cyklického dotazování koncového bodu.

### <a name="view-new-profiles-in-the-azure-portal"></a>Zobrazení nových profilů v Azure Portal
Můžete ověřit, že jsou vytvořeny všechny tři profily, a to tak, že se podíváte na prostředky ve `luis-traffic-manager` skupině prostředků.

![Snímek obrazovky skupiny prostředků Azure Luis-Traffic-Manager](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Ověřte, že stav profilu je online.
Traffic Manager se dotazuje cesty každého koncového bodu, aby se zajistilo, že je online. Pokud je online, stav podřízených profilů je `Online` . Zobrazuje se na **přehledu** jednotlivých profilů.

![Snímek obrazovky s přehledem profilu Azure Traffic Manager, který zobrazuje stav monitorování online](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Ověření, že funguje dotazování Traffic Manager
Dalším způsobem, jak ověřit, že cyklické dotazování Traffic Manageru funguje, je s protokoly koncového bodu LUIS. Na stránce seznam webových aplikací [Luis][LUIS] exportujte protokol koncového bodu pro aplikaci. Vzhledem k tomu, že Traffic Manager často cyklické dotazování pro dva koncové body, existují položky v protokolech i v případě, že byly pouze během několika minut. Nezapomeňte vyhledat položky, na kterých dotaz začíná `traffic-manager-` .

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Ověření odpovědi DNS z Traffic Manager funguje
Pokud chcete ověřit, že odpověď DNS vrátí koncový bod LUIS, požádejte o provoz správu DNS nadřazeného profilu pomocí klientské knihovny DNS. Název DNS nadřazeného profilu je `luis-dns-parent.trafficmanager.net` .

Následující kód Node.js vytvoří požadavek na nadřazený profil a vrátí koncový bod LUIS:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

Úspěšná odpověď s koncovým bodem LUIS je:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com',
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Použít nadřazený profil Traffic Manager
Aby bylo možné spravovat provoz mezi koncovými body, je nutné vložit volání služby Traffic Manager DNS, aby se našel koncový bod LUIS. Toto volání je provedeno pro každý požadavek koncového bodu LUIS a musí simulovat geografické umístění uživatele klientské aplikace LUIS. Přidejte kód odpovědi DNS do mezi klientskou aplikací LUIS a požadavkem na LUIS pro předpověď koncového bodu.

## <a name="resolving-a-degraded-state"></a>Řešení stavu snížené úrovně

Povolením [diagnostických protokolů](../../traffic-manager/traffic-manager-diagnostic-logs.md) pro Traffic Manager zjistíte, proč je stav koncového bodu degradován.

## <a name="clean-up"></a>Vyčištění
Odeberte dva klíče koncového bodu LUIS, tři profily Traffic Manager a skupinu prostředků, která obsahuje tyto pět prostředků. To se provádí z Azure Portal. Odstraníte pět prostředků ze seznamu prostředků. Pak odstraňte skupinu prostředků.

## <a name="next-steps"></a>Další kroky

Přečtěte si možnosti [middlewaru](/azure/bot-service/bot-builder-create-middleware?tabs=csaddmiddleware%252ccsetagoverwrite%252ccsmiddlewareshortcircuit%252ccsfallback%252ccsactivityhandler) v BotFramework v4, abyste porozuměli tomu, jak je možné tento kód správy provozu přidat do robota BotFramework.

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: ../../traffic-manager/index.yml
[LUIS]: ./luis-reference-regions.md#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: ../../traffic-manager/traffic-manager-routing-methods.md
[traffic-manager-endpoints]: ../../traffic-manager/traffic-manager-endpoint-types.md