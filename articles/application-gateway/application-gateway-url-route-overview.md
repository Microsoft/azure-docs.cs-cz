---
title: Přehled směrování obsahu na základě adresy URL v Azure Application Gateway
description: Tato stránka poskytuje přehled adresa URL brány aplikací Azure – směrování na základě obsahu, konfigurace UrlPathMap a pravidla pathbasedrouting ve službě.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 1/8/2019
ms.author: victorh
ms.openlocfilehash: 95d443eeebf2fe5a8fad185210f88576a5f21f6a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435291"
---
# <a name="url-path-based-routing-overview"></a>Přehled směrování na základě cest URL

Směrování na základě cesty URL umožňuje směrovat provoz do fondů back-end serveru na základě cest URL požadavku. 

Jedním ze scénářů je směrování požadavků na různé typy obsahu do různých fondů back-endové serveru.

V následujícím příkladu služba Application Gateway obsluhuje provoz pro contoso.com ze tří fondů back-end serverů například: Videofondserveru, Obrazkyfondserveru a Vychozifondserveru.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Požadavky pro protokol http\://contoso.com/video/* jsou směrovány na Videofondserveru a http\://contoso.com/images/* jsou směrovány na Obrazkyfondserveru. Pokud nevyhovuje žádný vzor cesty, vybere se VychoziFondServeru.

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

#### <a name="v1"></a>v1

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
