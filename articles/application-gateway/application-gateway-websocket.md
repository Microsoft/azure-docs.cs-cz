---
title: Podpora protokolu WebSocket v Azure Application Gateway | Microsoft Docs
description: Tato stránka obsahuje přehled podpory protokolu WebSocket brány aplikace.
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 8968dac1-e9bc-4fa1-8415-96decacab83f
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: amsriva
ms.openlocfilehash: 75b06ddd02da231b7813c609c848c75e42116da5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836795"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Přehled podpory protokolu WebSocket v aplikační brány

Application Gateway poskytuje nativní podporu pro WebSocket napříč všech velikostí brány. Neexistuje žádné uživatelsky konfigurovatelného nastavení selektivně povolení nebo zakázání podpory protokolu WebSocket. 

Protokol WebSocket standardizované v [RFC6455](https://tools.ietf.org/html/rfc6455) umožňuje plně duplexní komunikace mezi serverem a klientem přes dlouhotrvající připojení TCP. Tato funkce umožňuje více interaktivní komunikace mezi webového serveru a klienta, které může být obousměrné bez nutnosti dotazování jako požadavků ve implementace založené na protokolu HTTP. WebSocket má nedostatek nároky na rozdíl od protokolu HTTP a můžete znovu použít stejné připojení protokolu TCP pro více požadavku nebo odpovědi, výsledkem je efektivnější využití prostředků. Protokol WebSocket protokoly jsou navrženy pro práci přes tradiční HTTP porty 80 a 443.

Můžete nadále používat standardní naslouchací proces protokolu HTTP na portu 80 nebo 443 přijímat provoz protokolu WebSocket. Provoz protokolu WebSocket je pak přesměrované na serveru povoleno back-end protokolu WebSocket pomocí příslušné back-endový fond uvedeného v pravidlech brány aplikace. Back-end serveru musí reagovat na sondy brány aplikace, které jsou popsány v [přehled test stavu](application-gateway-probe-overview.md) části. Sondy stavu služby Application gateway jsou pouze prostřednictvím protokolu HTTP nebo HTTPS. Každý server back-end musí odpovědět na sondy HTTP pro službu application gateway pro směrování provozu protokolu WebSocket k serveru.

## <a name="listener-configuration-element"></a>Konfigurační prvek naslouchacího procesu

Existující naslouchací proces protokolu HTTP lze použít pro podporu přenosů protokolu WebSocket. Zde je fragment element httpListeners z ukázkového souboru šablony. Potřebovali byste naslouchací procesy HTTP a HTTPS pro podporu protokolu WebSocket a zabezpečení přenosů protokolu WebSocket. Podobně můžete použít [portál](application-gateway-create-gateway-portal.md) nebo [prostředí PowerShell](application-gateway-create-gateway-arm.md) k vytvoření aplikační brány s moduly pro naslouchání na portu 80/443 pro podporu přenosů protokolu WebSocket.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Konfigurace pravidla BackendAddressPool, BackendHttpSetting a směrování

BackendAddressPool se používá k definování fond back-end servery protokolu WebSocket povoleno. BackendHttpSetting je definována pomocí back-end port 80 a 443. Vlastnosti pro spřažení na základě souborů cookie a requestTimeouts nejsou relevantní pro přenosy protokolu WebSocket. Neexistuje žádná změna v pravidlo směrování, "Základní" se používá ke svázání odpovídající naslouchací proces a odpovídající fondu adres back-end. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>Back-end WebSocket povoleno

Váš back-end musí mít protokolu HTTP nebo HTTPS webový server spuštěný ve konfigurovaného portu pro protokolu WebSocket pro práci (obvykle 80/443). Tento požadavek je, protože protokol WebSocket vyžaduje počáteční handshake být upgradu protokolu WebSocket jako pole hlavičky protokolu HTTP. Následuje příklad hlavičky:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Dalším důvodem je, že tento test stavu back-end brány aplikace podporuje pouze protokoly HTTP a HTTPS. Pokud back-end serveru neodpovídá na protokolu HTTP nebo HTTPS sondy, se dostala mimo fond back-end.

## <a name="next-steps"></a>Další kroky

Po získání informací o podporu protokolu WebSocket, přejděte na [vytvoření služby application gateway](application-gateway-create-gateway.md) začít pracovat s protokolu WebSocket webové aplikace s podporou.

