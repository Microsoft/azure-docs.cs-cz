---
title: Podpora protokolu WebSocket ve službě Azure Application Gateway | Dokumentace Microsoftu
description: Tato stránka poskytuje přehled podpory Application Gateway pomocí protokolu WebSocket.
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
ms.openlocfilehash: cc6e2480ea117a288ae94c9cd66be6a354d8230f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993325"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Přehled podpory protokolu WebSocket ve službě Application Gateway

Application Gateway poskytuje nativní podpora protokolu WebSocket všechny velikosti brány. Neexistuje žádné uživatelsky konfigurovatelné nastavení pro selektivní povolení nebo zakázání podpory protokolu WebSocket. 

Protokol WebSocket standardizaci [RFC6455](https://tools.ietf.org/html/rfc6455) umožňuje plně duplexní komunikaci mezi serverem a klientem přes dlouho běžící připojení TCP. Tato funkce umožňuje vyšší interaktivitou komunikace mezi webovým serverem a klientem, který může být obousměrné bez nutnosti dotazování jako požadovaných v implementacích založené na protokolu HTTP. Objekt WebSocket je nízké nároky na rozdíl od protokolu HTTP a můžete znovu použít stejné připojení TCP pro více požadavku/odpovědi, což vede k mnohem efektivnější využití prostředků. Protokoly WebSocket jsou navrženy pro práci přes tradiční HTTP porty 80 a 443.

Můžete pokračovat v používání standardní naslouchací proces protokolu HTTP na portu 80 nebo 443 pro příjem provozu pomocí protokolu WebSocket. Přenos pomocí protokolu WebSocket je pak přesměrované na serveru povoleno back-end protokolu WebSocket pomocí odpovídající back-endový fond jak je uvedeno v pravidlech brány application. Back-end serveru musí odpovědět na sond brány aplikací, které jsou popsány v [přehled sondy stavu](application-gateway-probe-overview.md) oddílu. Sondy stavu služby Application gateway jsou jenom HTTP/HTTPS. Každý back-end serveru musí odpovědět na sondy protokolu HTTP služby application gateway pro směrování provozu objektu websocket na straně serveru.

## <a name="listener-configuration-element"></a>Konfigurační prvek naslouchacího procesu

Existující naslouchací proces protokolu HTTP slouží k podpoře přenos pomocí protokolu WebSocket. Tady je fragment prvku httpListeners ze souboru šablony vzorku. Je třeba naslouchacích procesů HTTP a HTTPS pro podporu protokolu WebSocket a zabezpečený přenos pomocí protokolu WebSocket. Podobně můžete použít [portál](application-gateway-create-gateway-portal.md) nebo [Powershellu](application-gateway-create-gateway-arm.md) k vytvoření služby application gateway s moduly pro naslouchání na portu 80/443 podporují přenos pomocí protokolu WebSocket.

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

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Konfigurace pravidla BackendAddressPool, nastavení BackendHttpSetting a směrování

BackendAddressPool slouží k definování back-endového fondu se servery pomocí protokolu WebSocket povolena. Nastavení backendHttpSetting je definována s back-endový port 80 a 443. Vlastnosti pro spřažení na základě souborů cookie a requestTimeouts nejsou relevantní pro přenos pomocí protokolu WebSocket. Není žádná změna v pravidle směrování, "Základní" se používá a jejich zapojení odpovídající naslouchacího procesu pro odpovídající back-endového fondu adres. 

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

## <a name="websocket-enabled-backend"></a>Back-end pomocí protokolu WebSocket povoleno

Back-endu, musí mít HTTP/HTTPS webový server běží na nakonfigurované portu protokolu websocket pro práci (obvykle 80 a 443). Tento požadavek je, protože protokol WebSocket vyžaduje počáteční handshake být u upgradu protokol WebSocket jako pole hlavičky protokolu HTTP. Následuje příklad hlavičky:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Dalším důvodem je, že tuto sondu stavu back-endu aplikace brána podporuje pouze protokoly HTTP a HTTPS. Pokud back-end serveru neodpovídá na HTTP nebo HTTPS testy, je vyřazen ze back-endový fond.

## <a name="next-steps"></a>Další postup

Po získání informací o podpora protokolu WebSocket, přejděte na [vytvoření služby application gateway](application-gateway-create-gateway.md) začít s protokol WebSocket webové aplikace s podporou.

