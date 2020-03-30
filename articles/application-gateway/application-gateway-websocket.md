---
title: Podpora websocketu v Azure Application Gateway
description: Application Gateway poskytuje nativní podporu protokolu WebSocket ve všech velikostech brány. Neexistují žádná uživatelsky konfigurovatelná nastavení.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: baa02c4d946a121f26f421af99835ae2bea18847
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74130326"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Přehled podpory WebSocket v aplikační bráně

Application Gateway poskytuje nativní podporu protokolu WebSocket ve všech velikostech brány. Neexistuje žádné uživatelsky konfigurovatelné nastavení pro selektivní povolení nebo zakázání podpory protokolu WebSocket. 

Protokol WebSocket standardizovaný v [rfc6455](https://tools.ietf.org/html/rfc6455) umožňuje plně duplexní komunikaci mezi serverem a klientem přes dlouho běžící připojení TCP. Tato funkce umožňuje interaktivnější komunikaci mezi webovým serverem a klientem, která může být obousměrná bez nutnosti dotazování, jak je požadováno v implementacích založených na protokolu HTTP. WebSocket má nízkou režii na rozdíl od protokolu HTTP a můžete znovu použít stejné připojení TCP pro více požadavků nebo odpovědí, což má za následek efektivnější využití prostředků. Protokoly WebSocket jsou navrženy tak, aby fungovaly přes tradiční porty HTTP 80 a 443.

Můžete pokračovat v používání standardní http naslouchací proces na portu 80 nebo 443 přijímat websocket přenosy. Provoz websocketu je pak směrován na back-endový server s povoleným webem Socket pomocí příslušného back-endového fondu, jak je určeno v pravidlech brány aplikace. Back-endový server musí reagovat na sondy aplikační brány, které jsou popsány v části [přehledu sondy stavu.](application-gateway-probe-overview.md) Sondy stavu aplikační brány jsou pouze http/https. Každý server back-end musí reagovat na http sondy pro aplikační bránu pro směrování přenosů WebSocket na server.

Používá se v aplikacích, které využívají rychlou komunikaci v reálném čase, jako je chat, řídicí panel a herní aplikace.

## <a name="how-does-websocket-work"></a>Jak websocket funguje

Chcete-li vytvořit připojení WebSocket, konkrétní handshake založené http je exchanged mezi klientem a serverem. Pokud je protokol aplikační vrstvy úspěšný, je "upgradován" z protokolu HTTP na websockets pomocí dříve vytvořeného připojení TCP. Jakmile k tomu dojde, HTTP je zcela mimo obraz; data mohou být odesílána nebo přijímána pomocí protokolu WebSocket oběma koncovými body, dokud není připojení WebSocket ukončeno. 

![webová zásuvka](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Konfigurační prvek naslouchacího procesu

Existující naslouchací proces HTTP lze použít pro podporu přenosu WebSocket. Následuje úryvek prvku httpListeners z ukázkového souboru šablony. Budete potřebovat http a https naslouchací procesy pro podporu WebSocket a zabezpečené websocket přenosy. Podobně můžete použít portál nebo Azure PowerShell k vytvoření aplikační brány s naslouchacími procesy na portu 80/443 pro podporu provozu WebSocket.

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

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>BackendAddressPool, BackendHttpSetting a konfigurace pravidla směrování

BackendAddressPool se používá k definování back-endového fondu se servery s povoleným webem. Back-endHttpSetting je definován s back-endport port 80 a 443. Hodnota časového času požadavku v nastavení protokolu HTTP platí také pro relaci WebSocket. V pravidle směrování, které se používá k propojení příslušného naslouchací ho sachy s odpovídajícím fondem back-endových adres, není vyžadována žádná změna. 

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

## <a name="websocket-enabled-backend"></a>Back-end s povoleným websocketem

Váš back-end musí mít webový server HTTP/HTTPS spuštěný na nakonfigurovaném portu (obvykle 80/443), aby websocket fungoval. Tento požadavek je, protože protokol WebSocket vyžaduje počáteční handshake být HTTP s upgradem na protokol WebSocket jako pole záhlaví. Následuje příklad záhlaví:

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

Dalším důvodem je, že sonda back-endový stav brány aplikace podporuje pouze protokoly HTTP a HTTPS. Pokud back-endový server nereaguje na sondy HTTP nebo HTTPS, je převzat z back-endového fondu.

## <a name="next-steps"></a>Další kroky

Po zjištění podpory websocketu přejděte k [vytvoření aplikační brány,](quick-create-powershell.md) abyste mohli začít s webovou aplikací s povolenou webovou aplikací WebSocket.