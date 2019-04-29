---
title: Podpora protokolu WebSocket ve službě Azure Application Gateway | Dokumentace Microsoftu
description: Tato stránka poskytuje přehled podpory Application Gateway pomocí protokolu WebSocket.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/18/2019
ms.openlocfilehash: 54c34690e678f07d6309a1877b0ca5d0a0b274f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831231"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Přehled podpory protokolu WebSocket ve službě Application Gateway

Application Gateway poskytuje nativní podpora protokolu WebSocket všechny velikosti brány. Neexistuje žádné uživatelsky konfigurovatelné nastavení pro selektivní povolení nebo zakázání podpory protokolu WebSocket. 

Protokol WebSocket standardizaci [RFC6455](https://tools.ietf.org/html/rfc6455) umožňuje plně duplexní komunikaci mezi serverem a klientem přes dlouho běžící připojení TCP. Tato funkce umožňuje vyšší interaktivitou komunikace mezi webovým serverem a klientem, který může být obousměrné bez nutnosti dotazování jako požadovaných v implementacích založené na protokolu HTTP. Objekt WebSocket je nízké nároky na rozdíl od protokolu HTTP a můžete znovu použít stejné připojení TCP pro více požadavku/odpovědi, což vede k mnohem efektivnější využití prostředků. Protokoly WebSocket jsou navrženy pro práci přes tradiční HTTP porty 80 a 443.

Můžete pokračovat v používání standardní naslouchací proces protokolu HTTP na portu 80 nebo 443 pro příjem provozu pomocí protokolu WebSocket. Přenos pomocí protokolu WebSocket je pak přesměrované na serveru povoleno back-end protokolu WebSocket pomocí odpovídající back-endový fond jak je uvedeno v pravidlech brány application. Back-end serveru musí odpovědět na sond brány aplikací, které jsou popsány v [přehled sondy stavu](application-gateway-probe-overview.md) oddílu. Sondy stavu služby Application gateway jsou jenom HTTP/HTTPS. Každý back-end serveru musí odpovědět na sondy protokolu HTTP služby application gateway pro směrování provozu objektu websocket na straně serveru.

Používá se v aplikacích, které využívají samosprávné komunikace rychlé, v reálném čase, jako jsou konverzace, řídicí panel a herních aplikací.

## <a name="how-does-websocket-work"></a>Jak funguje objektu websocket na straně

K navázání připojení soketu WebSocket, konkrétní metody handshake založené na protokolu HTTP se vyměňují mezi klientem a serverem. V případě úspěšného ověření application layer protocol je "upgradovat" z HTTP na protokoly Websocket, pomocí dříve navázaném připojení TCP. Jakmile k tomu dojde, HTTP je zcela mimo obrázku; data mohou být odeslány nebo přijaty pomocí protokolu WebSocket oba koncové body, dokud není zavřena připojení soketu WebSocket. 

![addcert](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Konfigurační prvek naslouchacího procesu

Existující naslouchací proces protokolu HTTP slouží k podpoře přenos pomocí protokolu WebSocket. Tady je fragment prvku httpListeners ze souboru šablony vzorku. Je třeba naslouchacích procesů HTTP a HTTPS pro podporu protokolu WebSocket a zabezpečený přenos pomocí protokolu WebSocket. Podobně můžete použít na portálu nebo v prostředí Azure PowerShell k vytvoření služby application gateway s moduly pro naslouchání na portu 80/443 podporují přenos pomocí protokolu WebSocket.

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

Po získání informací o podpora protokolu WebSocket, přejděte na [vytvoření služby application gateway](quick-create-powershell.md) začít s protokol WebSocket webové aplikace s podporou.