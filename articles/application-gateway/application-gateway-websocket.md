---
title: Podpora protokolu WebSocket v Azure Application Gateway
description: Application Gateway poskytuje nativní podporu protokolu WebSocket ve všech velikostech brány. Neexistují žádná uživatelsky konfigurovatelné nastavení.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: 59ca7708b24d2e75381290b80adeb671e2b49822
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91362691"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Přehled podpory protokolu WebSocket v Application Gateway

Application Gateway poskytuje nativní podporu protokolu WebSocket ve všech velikostech brány. Neexistuje žádné uživatelsky konfigurovatelné nastavení pro selektivní povolení nebo zakázání podpory protokolu WebSocket. 

Protokol WebSocket standardizovaný v [RFC6455](https://tools.ietf.org/html/rfc6455) umožňuje plně duplexní komunikaci mezi serverem a klientem po dlouhém běhu připojení TCP. Tato funkce umožňuje interaktivní komunikaci mezi webovým serverem a klientem, což může být obousměrné bez nutnosti cyklického dotazování podle požadavků v implementacích založených na protokolu HTTP. WebSocket má na rozdíl od HTTP nízkou režii a může znovu použít stejné připojení TCP pro více požadavků nebo odpovědí, což má za následek efektivnější využití prostředků. Protokoly protokolu WebSocket jsou navržené tak, aby fungovaly přes tradiční porty HTTP 80 a 443.

Pro příjem provozu protokolu WebSocket můžete nadále používat standardní naslouchací proces HTTP na portu 80 nebo 443. Přenosy protokolu WebSocket se pak přesměrují na back-end Server s protokolem WebSocket s použitím příslušného back-endu, jak je uvedeno v pravidlech služby Application Gateway. Back-end Server musí reagovat na testy služby Application Gateway, které jsou popsány v části [Přehled sondy stavu](application-gateway-probe-overview.md) . Sondy stavu Application Gateway jsou jenom HTTP/HTTPS. Každý back-end Server musí reagovat na testy HTTP, aby brána Application Gateway směrovala provoz protokolu WebSocket na server.

Používá se v aplikacích, které využívají rychlou komunikaci v reálném čase, jako jsou konverzace, řídicí panely a herní aplikace.

## <a name="how-does-websocket-work"></a>Jak funguje WebSocket

K navázání připojení pomocí protokolu WebSocket se vymění konkrétní Metoda handshake založená na protokolu HTTP mezi klientem a serverem. V případě úspěchu je protokol aplikační vrstvy upgradován z HTTP na WebSockets pomocí dříve vytvořeného připojení TCP. V takovém případě je HTTP zcela mimo obrázek. data je možné odesílat nebo přijímat pomocí protokolu WebSocket oběma koncovými body, až do ukončení připojení protokolu WebSocket. 

![Diagram porovnává klienta s webovým serverem, který se připojuje dvakrát, aby získal dvě odpovědi s interakcí protokolu WebSocket, kde se klient připojí k serveru jednou, aby získal více odpovědí.](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Konfigurační prvek naslouchacího procesu

K podpoře provozu protokolu WebSocket se dá použít existující naslouchací proces HTTP. Následuje fragment prvku httpListeners z ukázkového souboru šablony. Pro podporu protokolu WebSocket a zabezpečení provozu protokolu WebSocket budete potřebovat naslouchací proces HTTP i HTTPS. Podobně můžete pomocí portálu nebo Azure PowerShell vytvořit Aplikační bránu s naslouchacími procesy na portu 80/443 pro podporu provozu protokolu WebSocket.

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

BackendAddressPool se používá k definování fondu back-endu s povolenými servery protokolu WebSocket. BackendHttpSetting je definována s back-end port 80 a 443. Hodnota časového limitu požadavku v nastavení HTTP platí také pro relaci protokolu WebSocket. V pravidle směrování se nevyžaduje žádná změna, která se používá k propojení příslušného naslouchacího procesu s odpovídajícím fondem back-end adres. 

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

## <a name="websocket-enabled-backend"></a>Back-end s povoleným protokolem WebSocket

Aby mohl WebSocket fungovat, musí mít váš back-end webový server HTTP/HTTPS, který běží na konfigurovaném portu (obvykle 80/443). Důvodem je, že protokol WebSocket vyžaduje, aby počáteční Metoda handshake byla HTTP s upgradem na protokol WebSocket jako pole hlavičky. Následuje příklad záhlaví:

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

Dalším důvodem je, že sonda stavu back-endu služby Application Gateway podporuje jenom protokoly HTTP a HTTPS. Pokud back-end server nereaguje na testy HTTP nebo HTTPS, vychází z back-endu fondu.

## <a name="next-steps"></a>Další kroky

Po získání podpory protokolu WebSocket si přečtěte, jak [vytvořit Aplikační bránu](quick-create-powershell.md) a začít používat webovou aplikaci s povoleným protokolem WebSocket.