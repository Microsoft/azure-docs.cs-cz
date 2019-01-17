---
title: Hostování více webů ve službě Azure Application Gateway
description: Tato stránka poskytuje přehled podpory více webů Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 1/16/2019
ms.author: amsriva
ms.openlocfilehash: 04aca43e7220b0d5f644ca4f03db3a7442972728
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358464"
---
# <a name="application-gateway-multiple-site-hosting"></a>Hostování více webů ve službě Application Gateway

Hostování více webů umožňuje konfigurovat více než jednu webovou aplikaci ve stejné instanci služby Application Gateway. Tato funkce umožňuje nakonfigurovat efektivnější topologii vašich nasazení tak, že přidáte až 100 webů do jedné služby application gateway. Každou stránku lze přesměrovat na vlastní back-endový fond. V následujícím příkladu služba Application Gateway obsluhuje provoz pro contoso.com a fabrikam.com ze dvou fondů back-endového serveru s názvy FondServeruContoso a FondServeruFabrikam.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

> [!IMPORTANT]
> Pravidla se zpracovávají v pořadí, v jakém jsou uvedena na portálu. Důrazně doporučujeme nakonfigurovat naslouchací procesy pro více webů před konfigurací základního naslouchacího procesu.  Tím se zajistí směrování provozu do správného back-endu. Pokud je základní naslouchací proces uveden jako první a odpovídá příchozímu požadavku, požadavek se zpracuje tímto naslouchacím procesem.

Požadavky na adresu http://contoso.com se směrují na ContosoServerPool a požadavky na adresu http://fabrikam.com na FabrikamServerPool.

Podobně lze ve stejném nasazení služby Application Gateway hostovat dvě poddomény stejné nadřazené domény. Příklady použití subdomén můžou zahrnovat adresy http://blog.contoso.com a http://app.contoso.com hostované v jednom nasazení aplikační brány.

## <a name="host-headers-and-server-name-indication-sni"></a>Hlavičky hostitele a Identifikace názvu serveru (SNI)

Existují tři běžné mechanismy pro povolení hostování více webů ve stejné infrastruktuře.

1. Hostování více webových aplikací, z nichž každá je na jedinečné IP adrese.
2. Použití názvu hostitele k hostování více webových aplikací na stejné IP adrese.
3. Použití různých portů k hostování více webových aplikací na stejné IP adrese.

V současné době získá služba Application Gateway jednu veřejnou IP adresu, na které naslouchá provozu. Proto v současné době podpora více aplikací, z nichž každá má vlastní IP adresu, není podporována. Služba Application Gateway podporuje hostování více aplikací, z nichž každá naslouchá na jiném portu, ale tento scénář by vyžadoval, aby aplikace přijímaly provoz na nestandardních portech, a tato konfigurace často není požadována. Služba Application Gateway se při hostování více než jednoho webu na stejné veřejné IP adrese a portu spoléhá na hlavičky hostitele HTTP 1.1. Weby hostované ve službě Application Gateway mohou také podporovat přesměrování zpracování SSL pomocí rozšíření protokolu TLS Identifikace názvu serveru (SNI). Tento scénář znamená, že klientský prohlížeč a back-endová webová farma musí podporovat HTTP/1.1 a rozšíření protokolu TLS, jak je definováno v dokumentu RFC 6066.

## <a name="listener-configuration-element"></a>Konfigurační prvek naslouchacího procesu

Existující konfigurační prvek HTTPListener je vylepšený pro podporu názvu hostitele a prvků Identifikace názvu serveru. Ty slouží k tomu, aby služba Application Gateway mohla směrovat provoz na příslušný back-endový fond. Následující ukázka kódu je fragment prvku HttpListeners ze souboru šablony.

```json
"httpListeners": [
    {
        "name": "appGatewayHttpsListener1",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
            },
            "Protocol": "Https",
            "SslCertificate": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
            },
            "HostName": "contoso.com",
            "RequireServerNameIndication": "true"
        }
    },
    {
        "name": "appGatewayHttpListener2",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
            },
            "Protocol": "Http",
            "HostName": "fabrikam.com",
            "RequireServerNameIndication": "false"
        }
    }
],
```

Na stránce [Šablona Resource Manageru používající hostování více webů](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) najdete kompletní nasazení založené na šabloně.

## <a name="routing-rule"></a>Pravidlo směrování

V pravidle směrování není požadována žádná změna. Stále byste měli volit pravidlo směrování „Základní“ k provázání příslušných naslouchacích procesů webů s odpovídajícími back-endovými fondy adres.

```json
"requestRoutingRules": [
{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

},
{
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}
]
```

## <a name="next-steps"></a>Další postup

Po získání informací o hostování více webů přejděte k tématu [Vytvoření služby Application Gateway používající hostování více webů](application-gateway-create-multisite-azureresourcemanager-powershell.md) a vytvořte službu Application Gateway se schopností podporovat více než jednu webovou aplikaci.