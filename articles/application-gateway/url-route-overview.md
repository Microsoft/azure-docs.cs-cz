---
title: Přehled směrování obsahu na základě adresy URL v Azure Application Gateway
description: Tento článek obsahuje přehled směrování obsahu založeného na adresách URL na základě adresy AZURE Application Gateway, konfigurace UrlPathMap a pravidla PathBasedRouting.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 09/10/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e20acb131b1a091fef858dab34705f4a8d3b4c4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77251834"
---
# <a name="url-path-based-routing-overview"></a>Přehled směrování na základě cest URL

Směrování na základě cesty URL umožňuje směrovat provoz do fondů back-end serveru na základě cest URL požadavku. 

Jedním ze scénářů je směrování požadavků na různé typy obsahu do různých fondů back-endové serveru.

V následujícím příkladu služba Application Gateway obsluhuje provoz pro contoso.com ze tří fondů back-endového serveru, například: VideoFondServeru, ObrazkyFondServeru a VychoziFondServeru.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Požadavky na\:http //contoso.com/video/* jsou směrovány do\:Fondu VideoServerA a http //contoso.com/images/* jsou směrovány do fondu ImageServerPool. Pokud nevyhovuje žádný vzor cesty, vybere se VychoziFondServeru.

> [!IMPORTANT]
> Pro skladovou položku v1 jsou pravidla zpracována v pořadí, v jakém jsou uvedena na portálu. Pokud je základní naslouchací proces uveden jako první a odpovídá příchozímu požadavku, požadavek se zpracuje tímto naslouchacím procesem. Pro skladovou položku v2 mají přesné shody vyšší prioritu. Důrazně se však doporučuje nejprve nakonfigurovat posluchače s více sítěmi před konfigurací základního naslouchací procesu. Tím se zajistí směrování provozu do správného back-endu.

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

### <a name="pathpattern"></a>Vzor cesty

PathPattern je seznam vzorů cesty, které mají odpovídat. Každý vzor musí začínat znakem „/“ a znak „*“ lze použít pouze na konci za znakem „/“. Řetězec přidaný do matcheru cesty neobsahuje žádný text po prvním ? nebo #, a tyto znaky zde nejsou povoleny. V opačném případě jsou v pathpatternu povoleny všechny znaky povolené v adrese URL.

Podporované vzory závisí na tom, zda nasadíte aplikační bránu v1 nebo v2:

#### <a name="v1"></a>v1

Pravidla cesty jsou malá a velká písmena.

|vzor cesty v1  |Je podporována?  |
|---------|---------|
|`/images/*`     |ano|
|`/images*`     |ne|
|`/images/*.jpg`     |ne|
|`/*.jpg`     |ne|
|`/Repos/*/Comments/*`     |ne|
|`/CurrentUser/Comments/*`     |ano|

#### <a name="v2"></a>v2

Pravidla cesty jsou malá a velká písmena.

|vzor cesty v2  |Je podporována?  |
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

## <a name="next-steps"></a>Další kroky

Po získání informací o směrování obsahu na základě adresy URL přejděte k tématu [Vytvoření služby Application Gateway používající směrování na základě adresy URL](create-url-route-portal.md) a vytvořte službu Application Gateway s pravidly směrování adres URL.
