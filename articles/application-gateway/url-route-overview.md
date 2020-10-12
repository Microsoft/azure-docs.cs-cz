---
title: Přehled směrování obsahu na základě adresy URL v Azure Application Gateway
description: Tento článek poskytuje přehled směrování obsahu založeného na adrese URL Azure Application Gateway, konfigurace UrlPathMap a pravidla PathBasedRouting.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 09/10/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: a9b2e8148586ec58ea6a7a033099e726920857b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84987933"
---
# <a name="url-path-based-routing-overview"></a>Přehled směrování na základě cest URL

Směrování na základě cesty URL umožňuje směrovat provoz do fondů back-end serveru na základě cest URL požadavku. 

Jedním ze scénářů je směrování požadavků na různé typy obsahu do různých fondů back-endové serveru.

V následujícím příkladu služba Application Gateway obsluhuje provoz pro contoso.com ze tří fondů back-endového serveru, například: VideoFondServeru, ObrazkyFondServeru a VychoziFondServeru.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Požadavky na http \: //contoso.com/video/* jsou směrovány do videofondserveru a http \: //contoso.com/images/* jsou směrovány do obrazkyfondserveru. Pokud nevyhovuje žádný vzor cesty, vybere se VychoziFondServeru.

> [!IMPORTANT]
> V případě SKU V1 se pravidla zpracovávají v pořadí, v jakém jsou uvedena na portálu. Pokud je základní naslouchací proces uveden jako první a odpovídá příchozímu požadavku, požadavek se zpracuje tímto naslouchacím procesem. V případě SKU verze V2 mají přesné shody vyšší prioritu. Před konfigurací základního naslouchacího procesu se ale důrazně doporučuje nakonfigurovat nejprve naslouchací procesy pro více webů. Tím se zajistí směrování provozu do správného back-endu.

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

PathPattern je seznam vzorů cest, který se má shodovat. Každý vzor musí začínat znakem „/“ a znak „*“ lze použít pouze na konci za znakem „/“. Řetězec dodaný do shody cest nezahrnuje text za první? nebo # a tyto znaky tady nejsou povolené. V opačném případě jsou všechny znaky povolené v adrese URL povoleny v PathPattern.

Podporované vzory závisí na tom, jestli nasazujete Application Gateway v1 nebo v2:

#### <a name="v1"></a>V1

Pravidla cesty rozlišují malá a velká písmena.

|V1 – vzor cesty  |Je podporováno?  |
|---------|---------|
|`/images/*`     |ano|
|`/images*`     |ano|
|`/images/*.jpg`     |ne|
|`/*.jpg`     |ne|
|`/Repos/*/Comments/*`     |ne|
|`/CurrentUser/Comments/*`     |ano|

#### <a name="v2"></a>v2

Pravidla cesty rozlišují malá a velká písmena.

|V2 – vzor cesty  |Je podporováno?  |
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
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}"
    }

}
    }
]
```

## <a name="next-steps"></a>Další kroky

Po získání informací o směrování obsahu na základě adresy URL přejděte k tématu [Vytvoření služby Application Gateway používající směrování na základě adresy URL](create-url-route-portal.md) a vytvořte službu Application Gateway s pravidly směrování adres URL.
