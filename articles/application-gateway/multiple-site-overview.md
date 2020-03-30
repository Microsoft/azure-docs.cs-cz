---
title: Hostování více webů v Azure Application Gateway
description: Tento článek obsahuje přehled podpory azure aplikační brány pro více webů.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/11/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 4d945a255dacd35c61c3c80574b7d46b56de4aab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257406"
---
# <a name="application-gateway-multiple-site-hosting"></a>Hostování více webů ve službě Application Gateway

Více web hosting umožňuje nakonfigurovat více než jednu webovou aplikaci na stejném portu aplikační brány. Tato funkce umožňuje nakonfigurovat efektivnější topologii pro vaše nasazení přidáním až 100 webů do jedné aplikační brány. Každou stránku lze přesměrovat na vlastní back-endový fond. V následujícím příkladu aplikace brána slouží provoz pro `contoso.com` a `fabrikam.com` ze dvou fondů serverů back-end s názvem ContosoServerPool a FabrikamServerPool.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> Pravidla jsou zpracovány v pořadí, v jakém jsou uvedeny na portálu pro skladovou položku v1. Pro skladovou položku v2 mají přesné shody vyšší prioritu. Důrazně doporučujeme nakonfigurovat naslouchací procesy pro více webů před konfigurací základního naslouchacího procesu.  Tím se zajistí směrování provozu do správného back-endu. Pokud je základní naslouchací proces uveden jako první a odpovídá příchozímu požadavku, požadavek se zpracuje tímto naslouchacím procesem.

Žádosti na adresu `http://contoso.com` se směrují na ContosoServerPool a žádosti na adresu `http://fabrikam.com` na FabrikamServerPool.

Podobně můžete hostovat více subdomén stejné nadřazené domény ve stejném nasazení aplikační brány. Můžete například `http://blog.contoso.com` hostovat `http://app.contoso.com` a na nasazení jedné aplikační brány.

## <a name="host-headers-and-server-name-indication-sni"></a>Hlavičky hostitele a Identifikace názvu serveru (SNI)

Existují tři běžné mechanismy pro povolení hostování více webů ve stejné infrastruktuře.

1. Hostování více webových aplikací, z nichž každá je na jedinečné IP adrese.
2. Použití názvu hostitele k hostování více webových aplikací na stejné IP adrese.
3. Použití různých portů k hostování více webových aplikací na stejné IP adrese.

V současné době aplikační brána podporuje jednu veřejnou IP adresu, kde naslouchá provozu. Takže více aplikací, z nichž každá má vlastní IP adresu, není v současné době podporováno. 

Aplikační brána podporuje více aplikací, z nichž každý naslouchá na různých portech, ale tento scénář vyžaduje, aby aplikace přijímat provoz na nestandardníporty. Toto často není konfigurace, kterou chcete.

Služba Application Gateway se při hostování více než jednoho webu na stejné veřejné IP adrese a portu spoléhá na hlavičky hostitele HTTP 1.1. Weby hostované na aplikační bráně mohou také podporovat přepočet TLS s rozšířením TLS (SNI) s označením názvu serveru .) Tento scénář znamená, že klientský prohlížeč a back-endová webová farma musí podporovat HTTP/1.1 a rozšíření protokolu TLS, jak je definováno v dokumentu RFC 6066.

## <a name="listener-configuration-element"></a>Konfigurační prvek naslouchacího procesu

Existující konfigurační prvky HTTPListener jsou rozšířeny tak, aby podporovaly prvky označení názvu hostitele a serveru. Používá se aplikace gateway směrovat provoz do příslušného fondu back-endu. 

Následující příklad kódu je úryvek prvku HttpListeners ze souboru šablony:

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

V pravidle směrování není nutná žádná změna. Stále byste měli volit pravidlo směrování „Základní“ k provázání příslušných naslouchacích procesů webů s odpovídajícími back-endovými fondy adres.

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

Po získání informací o hostování více webů přejděte k tématu [Vytvoření služby Application Gateway používající hostování více webů](tutorial-multiple-sites-powershell.md) a vytvořte službu Application Gateway se schopností podporovat více než jednu webovou aplikaci.

