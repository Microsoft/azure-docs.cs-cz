---
title: Zvýšení kvóty koncového bodu – LUIS
titleSuffix: Azure Cognitive Services
description: Porozumění jazykům (LUIS) nabízí možnost zvýšit kvótu požadavku koncového bodu nad rámec kvóty jednoho klíče. To se provádí vytvořením další klíče pro LUIS a jejich přidání do aplikace LUIS na stránce **Publikovat** v resources **and keys** části.
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: c4ea9c5663755a4feb1693dd925d99b10c466140
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "70256606"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Správa kvót koncového bodu mezi klíči pomocí Microsoft Azure Traffic Manageru
Porozumění jazykům (LUIS) nabízí možnost zvýšit kvótu požadavku koncového bodu nad rámec kvóty jednoho klíče. To se provádí vytvořením další klíče pro LUIS a jejich přidání do aplikace LUIS na stránce **Publikovat** v resources **and keys** části. 

Klientská aplikace musí spravovat provoz mezi klíči. Luis to nedělá. 

Tento článek vysvětluje, jak spravovat provoz mezi klíči pomocí Azure [Traffic Manager][traffic-manager-marketing]. Musíte už mít trénované a publikované aplikace LUIS. Pokud ho nemáte, postupujte podle [rychlého startu](luis-get-started-create-app.md)předem sestavené domény . 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Připojení k PowerShellu na webu Azure Portal
Na webu [Azure][azure-portal] Portal otevřete okno PowerShellu. Ikona okna PowerShellu je **>_** v horním navigačním panelu. Pomocí PowerShellu z portálu získáte nejnovější verzi prostředí PowerShell a jste ověřeni. Prostředí PowerShell na portálu vyžaduje účet [Azure Storage.](https://azure.microsoft.com/services/storage/) 

![Snímek obrazovky s portálem Azure s otevřeným oknem Powershellu](./media/traffic-manager/azure-portal-powershell.png)

V následujících částech jsou [rutiny prostředí Traffic Manager PowerShell](https://docs.microsoft.com/powershell/module/az.trafficmanager/#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Vytvoření skupiny prostředků Azure pomocí PowerShellu
Před vytvořením prostředků Azure vytvořte skupinu prostředků, která bude obsahovat všechny prostředky. Pojmenujte `luis-traffic-manager` skupinu prostředků `West US`a použijte oblast . Oblast skupiny prostředků ukládá metadata o skupině. Nezpomalí vaše prostředky, pokud se nacházejí v jiné oblasti. 

Vytvořte skupinu prostředků pomocí rutiny **[New-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)**

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Vytvoření klíčů LUIS pro zvýšení celkové kvóty koncového bodu
1. Na webu Azure Portal vytvořte dva klíče `West US` **language understanding,** jeden v a jeden v . `East US` Použijte existující skupinu prostředků vytvořenou v `luis-traffic-manager`předchozí části s názvem . 

    ![Snímek obrazovky portálu Azure se dvěma klíči LUIS ve skupině prostředků Luis-Traffic-Manager](./media/traffic-manager/luis-keys.png)

2. Na webu [LUIS][LUIS] v části **Spravovat** na stránce **Prostředky Azure** přiřaďte k aplikaci klíče a znovu publikujte aplikaci výběrem tlačítka **Publikovat** v pravé horní nabídce. 

    Příklad adresy URL ve sloupci **koncového bodu** používá požadavek GET s klíčem koncového bodu jako parametr dotazu. Zkopírujte adresy URL koncových bodů dvou nových klíčů. Používají se jako součást konfigurace Traffic Manager dále v tomto článku.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Správa požadavků koncových bodů LUIS napříč klíči pomocí Traffic Manageru
Traffic Manager vytvoří nový přístupový bod DNS pro koncové body. Nefunguje jako brána nebo proxy, ale přísně na úrovni DNS. Tento příklad nezmění žádné záznamy DNS. Používá knihovnu DNS ke komunikaci se Traffic Managerzískat správný koncový bod pro tento konkrétní požadavek. _Každý_ požadavek určený pro službu LUIS nejprve vyžaduje požadavek Traffic Manager k určení koncového bodu LUIS, který má být používán. 

### <a name="polling-uses-luis-endpoint"></a>Dotazování používá koncový bod LUIS
Traffic Manager pravidelně dotazování koncové body a ujistěte se, že koncový bod je stále k dispozici. Dotazovaná adresa URL traffic manageru musí být přístupná s požadavkem GET a vrátit 200. Adresa URL koncového bodu na stránce **Publikovat** to dělá. Vzhledem k tomu, že každý klíč koncového bodu má jiné parametry řetězce trasy a dotazu, každý klíč koncového bodu potřebuje jinou cestu dotazování. Pokaždé, když Traffic Manager ankety, to stojí žádost o kvótu. Parametr řetězce dotazu **q** koncového bodu LUIS je utterance odeslaná službě LUIS. Tento parametr, namísto odeslání utterance, se používá k přidání traffic manager dotazování do protokolu koncového bodu LUIS jako ladicí technika při získávání Traffic Manager nakonfigurován.

Vzhledem k tomu, že každý koncový bod SLUŽBY LUIS potřebuje vlastní cestu, potřebuje vlastní profil Traffic Manageru. Chcete-li spravovat napříč profily, vytvořte [ _vnořenou_ ](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) architekturu Traffic Manageru. Jeden nadřazený profil odkazuje na podřízené profily a řídí provoz přes ně.

Jakmile je správce provozu nakonfigurován, nezapomeňte změnit cestu k použití parametru řetězce protokolu=false, aby se protokol nevyplňoval dotazováním.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Konfigurace traffic manageru s vnořenými profily
V následujících částech se vytvoří dva podřízené profily, jeden pro klíč Luis východ a jeden pro klíč Západní LUIS. Poté je vytvořen nadřazený profil a dva podřízené profily jsou přidány do nadřazeného profilu. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Vytvoření profilu east us traffic manageru pomocí prostředí PowerShell
Chcete-li vytvořit profil East US Traffic Manager, existuje několik kroků: vytvoření profilu, přidání koncového bodu a nastavení koncového bodu. Profil Traffic Manager může mít mnoho koncových bodů, ale každý koncový bod má stejnou cestu ověření. Vzhledem k tomu, že adresy URL koncového bodu LUIS pro východní a západní odběry se liší z důvodu oblasti a koncového bodu klíče, každý koncový bod LUIS musí být jeden koncový bod v profilu. 

1. Vytvoření profilu pomocí rutiny **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)**

    K vytvoření profilu použijte následující rutinu. Ujistěte se, `appIdLuis` `subscriptionKeyLuis`že jste změnili a . Název subscriptionKey je pro klíč LUIS východní USA. Pokud cesta není správná, včetně ID aplikace LUIS a klíče koncového `degraded` bodu, dotazování Traffic Manager je stav, protože správa provozu nemůže úspěšně požádat o koncový bod LUIS. Ujistěte se, `q` `traffic-manager-east` že hodnota je, takže můžete vidět tuto hodnotu v protokolech koncového bodu LUIS.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    Tato tabulka vysvětluje každou proměnnou v rutině:
    
    |Konfigurační parametr|Název proměnné nebo hodnota|Účel|
    |--|--|--|
    |-Name|luis-profil-eastus|Název Traffic Manageru na webu Azure Portal|
    |-ResourceGroupName|luis-dopravní manažer|Vytvořeno v předchozí části|
    |-TrafficRoutingMethod|Výkon|Další informace naleznete v [tématu Traffic Manager metody směrování][routing-methods]. Pokud používáte výkon, požadavek url správce provozu musí pocházet z oblasti uživatele. Pokud procházíte chatbotem nebo jinou aplikací, je odpovědností chatbota napodobovat region ve volání do Traffic Manageru. |
    |-RelativeDnsName|luis-dns-eastus|Toto je subdoména služby: luis-dns-eastus.trafficmanager.net|
    |-Ttl|30|Interval dotazování, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port a protokol pro službu LUIS je HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Nahraďte `<appIdLuis>` a `<subscriptionKeyLuis>` zadejte své vlastní hodnoty.|
    
    Úspěšný požadavek nemá žádnou odpověď.

2. Přidat koncový bod v USA v USA pomocí rutiny **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Tato tabulka vysvětluje každou proměnnou v rutině:

    |Konfigurační parametr|Název proměnné nebo hodnota|Účel|
    |--|--|--|
    |-Název koncového bodu|luis-východ-koncový bod|Název koncového bodu zobrazený pod profilem|
    |-TrafficManagerProfile|$eastprofile|Použití objektu profilu vytvořeného v kroku 1|
    |-Typ|Externí koncové body|Další informace naleznete v tématu [Traffic Manager endpoint][traffic-manager-endpoints] |
    |- Cíl|eastus.api.cognitive.microsoft.com|Toto je doména koncového bodu LUIS.|
    |-Koncový bodUmístění|"eastus"|Oblast koncového bodu|
    |-Koncový bodStav|Povoleno|Povolit koncový bod při jeho vytvoření|

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

3. Nastavení koncového bodu –USA pomocí rutiny **[Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Úspěšná odpověď bude stejná odpověď jako krok 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Vytvoření profilu správce provozu v USA v západní části pomocí prostředí PowerShell
Chcete-li vytvořit profil Správce provozu v usa v západní části, postupujte stejným způsobem: vytvořte profil, přidejte koncový bod a nastavte koncový bod.

1. Vytvoření profilu pomocí rutiny **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    K vytvoření profilu použijte následující rutinu. Ujistěte se, `appIdLuis` `subscriptionKeyLuis`že jste změnili a . Název subscriptionKey je pro klíč LUIS východní USA. Pokud cesta není správná, včetně ID aplikace LUIS a klíče koncového `degraded` bodu, dotazování Traffic Manager je stav, protože správa provozu nemůže úspěšně požádat o koncový bod LUIS. Ujistěte se, `q` `traffic-manager-west` že hodnota je, takže můžete vidět tuto hodnotu v protokolech koncového bodu LUIS.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    Tato tabulka vysvětluje každou proměnnou v rutině:
    
    |Konfigurační parametr|Název proměnné nebo hodnota|Účel|
    |--|--|--|
    |-Name|luis-profil-westus|Název Traffic Manageru na webu Azure Portal|
    |-ResourceGroupName|luis-dopravní manažer|Vytvořeno v předchozí části|
    |-TrafficRoutingMethod|Výkon|Další informace naleznete v [tématu Traffic Manager metody směrování][routing-methods]. Pokud používáte výkon, požadavek url správce provozu musí pocházet z oblasti uživatele. Pokud procházíte chatbotem nebo jinou aplikací, je odpovědností chatbota napodobovat region ve volání do Traffic Manageru. |
    |-RelativeDnsName|luis-dns-westus|Toto je subdoména služby: luis-dns-westus.trafficmanager.net|
    |-Ttl|30|Interval dotazování, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port a protokol pro službu LUIS je HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Nahraďte `<appId>` a `<subscriptionKey>` zadejte své vlastní hodnoty. Nezapomeňte, že tento klíč koncového bodu se liší od klíčového koncového bodu|
    
    Úspěšný požadavek nemá žádnou odpověď.

2. Přidání koncového bodu V USA v západní USA pomocí rutiny **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Tato tabulka vysvětluje každou proměnnou v rutině:

    |Konfigurační parametr|Název proměnné nebo hodnota|Účel|
    |--|--|--|
    |-Název koncového bodu|luis-west-koncový bod|Název koncového bodu zobrazený pod profilem|
    |-TrafficManagerProfile|$westprofile|Použití objektu profilu vytvořeného v kroku 1|
    |-Typ|Externí koncové body|Další informace naleznete v tématu [Traffic Manager endpoint][traffic-manager-endpoints] |
    |- Cíl|westus.api.cognitive.microsoft.com|Toto je doména koncového bodu LUIS.|
    |-Koncový bodUmístění|"westus"|Oblast koncového bodu|
    |-Koncový bodStav|Povoleno|Povolit koncový bod při jeho vytvoření|

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

3. Nastavení koncového bodu V USA v západní USA pomocí rutiny **[Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Úspěšná odpověď je stejná odpověď jako krok 2.

### <a name="create-parent-traffic-manager-profile"></a>Vytvořit nadřazený profil Traffic Manageru
Vytvořte nadřazený profil traffic manageru a propojte dva podřízené profily Traffic Manageru s nadřazenou položkou.

1. Vytvoření nadřazeného profilu pomocí rutiny **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Tato tabulka vysvětluje každou proměnnou v rutině:

    |Konfigurační parametr|Název proměnné nebo hodnota|Účel|
    |--|--|--|
    |-Name|luis-profil-rodič|Název Traffic Manageru na webu Azure Portal|
    |-ResourceGroupName|luis-dopravní manažer|Vytvořeno v předchozí části|
    |-TrafficRoutingMethod|Výkon|Další informace naleznete v [tématu Traffic Manager metody směrování][routing-methods]. Pokud používáte výkon, požadavek url správce provozu musí pocházet z oblasti uživatele. Pokud procházíte chatbotem nebo jinou aplikací, je odpovědností chatbota napodobovat region ve volání do Traffic Manageru. |
    |-RelativeDnsName|luis-dns-parent|Toto je subdoména služby: luis-dns-parent.trafficmanager.net|
    |-Ttl|30|Interval dotazování, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port a protokol pro službu LUIS je HTTPS/443|
    |-MonitorPath|`/`|Na této cestě nezáleží, protože místo toho se používají podřízené cesty koncového bodu.|

    Úspěšný požadavek nemá žádnou odpověď.

2. Přidání podřízeného profilu east us do nadřazeného profilu s **[typem Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** a **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Tato tabulka vysvětluje každou proměnnou v rutině:

    |Konfigurační parametr|Název proměnné nebo hodnota|Účel|
    |--|--|--|
    |-Název koncového bodu|dítě-koncový bod-useast|Východní profil|
    |-TrafficManagerProfile|$parentprofile|Profil pro přiřazení tohoto koncového bodu|
    |-Typ|Vnořené koncové body|Další informace naleznete v [tématu Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$eastprofile. Id|ID podřízeného profilu|
    |-Koncový bodStav|Povoleno|Stav koncového bodu po přidání do nadřazeného|
    |-Koncový bodUmístění|"eastus"|[Název oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/) prostředku|
    |-MinChildEndpoints|1|Minimální počet podřízených koncových bodů|

    Úspěšná odpověď vypadá takto a `child-endpoint-useast` zahrnuje nový koncový bod:    

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

3. Přidání podřízeného profilu v usa do nadřazeného profilu s **[rutinou Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** a **typem NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Tato tabulka vysvětluje každou proměnnou v rutině:

    |Konfigurační parametr|Název proměnné nebo hodnota|Účel|
    |--|--|--|
    |-Název koncového bodu|child-endpoint-uswest|Západní profil|
    |-TrafficManagerProfile|$parentprofile|Profil pro přiřazení tohoto koncového bodu|
    |-Typ|Vnořené koncové body|Další informace naleznete v [tématu Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$westprofile. Id|ID podřízeného profilu|
    |-Koncový bodStav|Povoleno|Stav koncového bodu po přidání do nadřazeného|
    |-Koncový bodUmístění|"westus"|[Název oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/) prostředku|
    |-MinChildEndpoints|1|Minimální počet podřízených koncových bodů|

    Úspěšná odpověď vypadá a zahrnuje `child-endpoint-useast` předchozí koncový `child-endpoint-uswest` bod i nový koncový bod:

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

4. Nastavení koncových bodů pomocí rutiny **[Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** 

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Úspěšná odpověď je stejná odpověď jako krok 3.

### <a name="powershell-variables"></a>Proměnné prostředí PowerShell
V předchozích částech byly vytvořeny tři `$eastprofile`proměnné `$westprofile` `$parentprofile`prostředí PowerShell: , , . Tyto proměnné se používají ke konci konfigurace Traffic Manager. Pokud jste se rozhodli nevytvářet proměnné nebo zapomněli, nebo vaše okno prostředí PowerShell časový čas out, můžete použít rutinu Prostředí PowerShell, **[Get-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)**, chcete-li získat profil znovu a přiřadit jej k proměnné. 

Nahraďte položky v `<>`úhlových závorkách , správnými hodnotami pro každý ze tří potřebných profilů. 

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Ověřit, že Traffic Manager funguje
Chcete-li ověřit, že profily Traffic Manager práce, profily musí mít stav `Online` Tento stav je založen na cestě dotazování koncového bodu. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Zobrazení nových profilů na webu Azure Portal
Můžete ověřit, že všechny tři profily jsou `luis-traffic-manager` vytvořeny při pohledu na prostředky ve skupině prostředků.

![Snímek obrazovky s luis-traffic-managerem skupiny prostředků Azure](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Ověření stavu profilu je online
Traffic Manager dotazování cestu každého koncového bodu a ujistěte se, že je online. Pokud je online, stav podřízených `Online`profilů je . Zobrazí se v **přehledu** každého profilu. 

![Snímek obrazovky s profilem Azure Traffic Manager Přehled zobrazující stav monitoru online](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Ověřit volební výsledky traffic manageru funguje
Dalším způsobem, jak ověřit funguje dotazování správce provozu, je protokoly koncového bodu LUIS. Na stránce se seznamem webových aplikací [LUIS][LUIS] exportujte protokol koncového bodu pro aplikaci. Vzhledem k tomu, že Traffic Manager dotazování často pro dva koncové body, existují položky v protokolech i v případě, že byly pouze na několik minut. Nezapomeňte vyhledat položky, kde dotaz `traffic-manager-`začíná .

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Ověření odpovědi DNS ze správce provozu funguje
Chcete-li ověřit, zda odpověď DNS vrací koncový bod služby LUIS, požádejte o službu DNS nadřazeného profilu Správa provozu pomocí klientské knihovny DNS. Název DNS nadřazeného profilu je `luis-dns-parent.trafficmanager.net`.

Následující kód Node.js vytvoří požadavek pro nadřazený profil a vrátí koncový bod LUIS:

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

## <a name="use-the-traffic-manager-parent-profile"></a>Použití nadřazeného profilu Traffic Manageru
Chcete-li spravovat provoz mezi koncovými body, musíte vložit volání dns Traffic Manager najít koncový bod LUIS. Toto volání se provádí pro každý požadavek koncového bodu LUIS a potřebuje simulovat geografické umístění uživatele klientské aplikace LUIS. Přidejte kód odpovědi DNS mezi klientskou aplikaci LUIS a požadavek na službu LUIS pro předpověď koncového bodu. 

## <a name="resolving-a-degraded-state"></a>Řešení zhoršeného stavu

Povolte [diagnostické protokoly](../../traffic-manager/traffic-manager-diagnostic-logs.md) pro Traffic Manager, abyste zjistili, proč je stav koncového bodu snížen.

## <a name="clean-up"></a>Vyčištění
Odeberte dva klíče koncového bodu LUIS, tři profily traffic manageru a skupinu prostředků, která obsahovala těchto pět prostředků. To se provádí z portálu Azure. Odstraníte pět prostředků ze seznamu prostředků. Potom odstraňte skupinu prostředků. 

## <a name="next-steps"></a>Další kroky

Zkontrolujte možnosti [middlewaru](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) v BotFramework u4, abyste pochopili, jak lze tento kód řízení provozu přidat do botu BotFramework. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
