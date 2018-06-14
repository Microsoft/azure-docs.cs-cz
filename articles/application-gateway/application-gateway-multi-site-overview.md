---
title: Hostování více webů ve službě Azure Application Gateway | Dokumentace Microsoftu
description: Tato stránka poskytuje přehled podpory služby Application Gateway pro více webů.
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: ''
ms.assetid: 49993fd2-87e5-4a66-b386-8d22056a616d
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: amsriva
ms.openlocfilehash: 645f68d836babf11f32fc391e6dacc9430f0070c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "22704757"
---
# <a name="application-gateway-multiple-site-hosting"></a>Hostování více webů ve službě Application Gateway

Hostování více webů umožňuje konfigurovat více než jednu webovou aplikaci ve stejné instanci služby Application Gateway. Tato funkce umožňuje nakonfigurovat efektivnější topologii vašich nasazení tím, že přidáte až 20 webů do jedné služby Application Gateway. Každou stránku lze přesměrovat na vlastní back-endový fond. V následujícím příkladu služba Application Gateway obsluhuje provoz pro contoso.com a fabrikam.com ze dvou fondů back-endového serveru s názvy FondServeruContoso a FondServeruFabrikam.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

> [!IMPORTANT]
> Pravidla se zpracovávají v pořadí, v jakém jsou uvedena na portálu. Důrazně doporučujeme nakonfigurovat naslouchací procesy pro více webů před konfigurací základního naslouchacího procesu.  Tím se zajistí směrování provozu do správného back-endu. Pokud je základní naslouchací proces uveden jako první a odpovídá příchozímu požadavku, požadavek se zpracuje tímto naslouchacím procesem.

Požadavky na http://contoso.com jsou směrovány na FondServeruContoso a požadavky na http://fabrikam.com jsou směrovány na FondServeruFabrikam.

Podobně lze ve stejném nasazení služby Application Gateway hostovat dvě poddomény stejné nadřazené domény. Příklady použití poddomén mohou zahrnovat http://blog.contoso.com a http://app.contoso.com hostované v jednom nasazení služby Application Gateway.

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

## <a name="next-steps"></a>Další kroky

Po získání informací o hostování více webů přejděte k tématu [Vytvoření služby Application Gateway používající hostování více webů](application-gateway-create-multisite-azureresourcemanager-powershell.md) a vytvořte službu Application Gateway se schopností podporovat více než jednu webovou aplikaci.

