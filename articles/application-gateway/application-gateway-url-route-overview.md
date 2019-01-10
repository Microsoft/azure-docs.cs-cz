---
title: Přehled směrování obsahu na základě adresy URL v Azure Application Gateway
description: Tato stránka poskytuje přehled adresa URL brány aplikací Azure – směrování na základě obsahu, konfigurace UrlPathMap a pravidla pathbasedrouting ve službě.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 1/8/2019
ms.author: victorh
ms.openlocfilehash: 1ada74f5c85ef327957ec4981e83f68bcafea858
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188755"
---
# <a name="url-path-based-routing-overview"></a>Přehled směrování na základě cest URL

Směrování na základě cesty URL umožňuje směrovat provoz do fondů back-end serveru na základě cest URL požadavku. 

Jedním ze scénářů je směrování požadavků na různé typy obsahu do různých fondů back-endové serveru.

V následujícím příkladu služba Application Gateway obsluhuje provoz pro contoso.com ze tří fondů back-end serverů například: Videofondserveru, Obrazkyfondserveru a Vychozifondserveru.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Požadavky na adresu http://contoso.com/video/ se směrují na VideoServerPool a požadavky na adresu http://contoso.com/images/ na ImageServerPool. Pokud nevyhovuje žádný vzor cesty, vybere se VychoziFondServeru.

> [!IMPORTANT]
> Pravidla se zpracovávají v pořadí, v jakém jsou uvedena na portálu. Důrazně doporučujeme nakonfigurovat naslouchací procesy pro více webů před konfigurací základního naslouchacího procesu.  Tím se zajistí směrování provozu do správného back-endu. Pokud je základní naslouchací proces uveden jako první a odpovídá příchozímu požadavku, požadavek se zpracuje tímto naslouchacím procesem.

## <a name="urlpathmap-configuration-element"></a>Konfigurační prvek UrlPathMap

Prvek UrlPathMap slouží k určení vzorů cest pro mapování fondů back-end serveru. Následující ukázka kódu je fragment prvku UrlPathMap ze souboru šablony.

```json
"urlPathMaps": [{
    "name": "{urlpathMapName}",
    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/    {subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName1}"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpSettingsList/{settingname1}"
        },
        "pathRules": [{
            "name": "{pathRuleName}",
            "properties": {
                "paths": [
                    "{pathPattern}"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName2}"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpsettingsList/{settingName2}"
                }
            }
        }]
    }
}]
```

### <a name="pathpattern"></a>PathPattern

PathPattern je seznam vzorů cest tak, aby odpovídaly. Každý vzor musí začínat znakem „/“ a znak „*“ lze použít pouze na konci za znakem „/“. Řetězec k předávaný cesta neobsahuje žádný text po prvním? nebo # a tyto znaky tady nejsou povolené. V opačném případě PathPattern může všech znaků v adrese URL.

Podporované vzory jsou závislé na tom, jestli nasazujete Application Gateway v1 nebo v2:

#### <a name="v1"></a>V1

Pravidla přidávání cest jsou malá a velká písmena.

|vzor cesty V1  |Je podporováno?  |
|---------|---------|
|`/images/*`     |ano|
|`/images*`     |ne|
|`/images/*.jpg`     |ne|
|`/*.jpg`     |ne|
|`/Repos/*/Comments/*`     |ne|
|`/CurrentUser/Comments/*`     |ano|

#### <a name="v2"></a>v2

Pravidla přidávání cest rozlišují malá a velká písmena.

|vzor cesty v2  |Je podporováno?  |
|---------|---------|
|`/images/*`     |ano|
|`/images*`     |ano|
|`/images/*.jpg`     |ne|
|`/*.jpg`     |ne|
|`/Repos/*/Comments/*`     |ne|
|`/CurrentUser/Comments/*`     |ano|

Více informací najdete v dokumentu [Šablona Resource Manageru používající směrování na základě adresy URL](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing).

## <a name="pathbasedrouting-rule"></a>Pravidlo PathBasedRouting

Pravidlo RequestRoutingRule typu PathBasedRouting slouží k vytvoření vazby mezi naslouchacím procesem a UrlPathMap. Všechny požadavky přijaté tímto naslouchacím procesem jsou směrovány na základě zásad zadaných v UrlPathMap.
Fragment pravidla PathBasedRouting:

```json
"requestRoutingRules": [
    {

"name": "{ruleName}",
"id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/requestRoutingRules/{ruleName}",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/ urlPathMaps/{urlpathMapName}"
    },

}
    }
]
```

## <a name="next-steps"></a>Další postup

Po získání informací o směrování obsahu na základě adresy URL přejděte k tématu [Vytvoření služby Application Gateway používající směrování na základě adresy URL](application-gateway-create-url-route-portal.md) a vytvořte službu Application Gateway s pravidly směrování adres URL.
