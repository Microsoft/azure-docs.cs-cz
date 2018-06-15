---
title: Přehled směrování obsahu na základě adresy URL v Azure Application Gateway
description: Tento článek poskytuje přehled směrování obsahu na základě adresy URL, konfigurace UrlPathMap a pravidla PathBasedRouting ve službě Application Gateway.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/23/2018
ms.author: victorh
ms.openlocfilehash: eaecb731d6f64bf7dd15d9a9b11739fd3fbc983e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32160541"
---
# <a name="azure-application-gatewaty-url-path-based-routing-overview"></a>Přehled směrování na základě cesty URL v Azure Application Gateway

Směrování na základě cesty URL umožňuje směrovat provoz do fondů back-end serveru na základě cest URL požadavku. 

Jedním ze scénářů je směrování požadavků na různé typy obsahu do různých fondů back-endové serveru.

V následujícím příkladu služba Application Gateway obsluhuje provoz pro contoso.com ze tří fondů back-endového serveru, například: VideoFondServeru, ObrazkyFondServeru a VychoziFondServeru.

![imageURLroute](./media/url-route-overview/figure1.png)

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

> [!NOTE]
> PathPattern: Toto nastavení je seznam vzorů cest, které je nutné splnit. Každý vzor musí začínat znakem „/“ a znak „*“ lze použít pouze na konci za znakem „/“. Řetězec předávaný ke kontrole cesty neobsahuje žádný text po počátečním znaku „?“ nebo „#“ a tyto znaky zde nejsou povolené.

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

Po získání informací o směrování obsahu na základě adresy URL přejděte k tématu [Vytvoření služby Application Gateway používající směrování na základě adresy URL](tutorial-url-route-powershell.md) a vytvořte aplikační bránu s pravidly směrování adres URL.
