---
title: Hostování více webů v Azure Application Gateway
description: Tento článek poskytuje přehled podpory Azure Application Gateway více lokalit.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/11/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 4d945a255dacd35c61c3c80574b7d46b56de4aab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80257406"
---
# <a name="application-gateway-multiple-site-hosting"></a>Hostování více webů ve službě Application Gateway

Hostování více webů umožňuje konfigurovat více než jednu webovou aplikaci na stejném portu služby Application Gateway. Tato funkce umožňuje nakonfigurovat efektivnější topologii nasazení přidáním až 100 webů do jedné aplikační brány. Každou stránku lze přesměrovat na vlastní back-endový fond. V následujícím příkladu brána Application Gateway obsluhuje provoz pro `contoso.com` a `fabrikam.com` ze dvou fondů back-end serverů s názvem fondserverucontoso a fondserverufabrikam.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> Pravidla se zpracovávají v pořadí, v jakém jsou uvedena na portálu pro SKU v1. V případě SKU verze V2 mají přesné shody vyšší prioritu. Důrazně doporučujeme nakonfigurovat naslouchací procesy pro více webů před konfigurací základního naslouchacího procesu.  Tím se zajistí směrování provozu do správného back-endu. Pokud je základní naslouchací proces uveden jako první a odpovídá příchozímu požadavku, požadavek se zpracuje tímto naslouchacím procesem.

Žádosti na adresu `http://contoso.com` se směrují na ContosoServerPool a žádosti na adresu `http://fabrikam.com` na FabrikamServerPool.

Podobně můžete hostovat více subdomén stejné nadřazené domény ve stejném nasazení služby Application Gateway. Můžete například hostovat `http://blog.contoso.com` a `http://app.contoso.com` v jediném nasazení aplikační brány.

## <a name="host-headers-and-server-name-indication-sni"></a>Hlavičky hostitele a Identifikace názvu serveru (SNI)

Existují tři běžné mechanismy pro povolení hostování více webů ve stejné infrastruktuře.

1. Hostování více webových aplikací, z nichž každá je na jedinečné IP adrese.
2. Použití názvu hostitele k hostování více webových aplikací na stejné IP adrese.
3. Použití různých portů k hostování více webových aplikací na stejné IP adrese.

V současné době Application Gateway podporuje jednu veřejnou IP adresu, kde naslouchá provozu. To znamená, že v současné době není podporována více aplikací, z nichž každá má vlastní IP adresu. 

Application Gateway podporuje několik aplikací, které každé naslouchá na různých portech, ale tento scénář vyžaduje, aby aplikace přijímaly provoz na nestandardních portech. To není často konfigurace, kterou požadujete.

Služba Application Gateway se při hostování více než jednoho webu na stejné veřejné IP adrese a portu spoléhá na hlavičky hostitele HTTP 1.1. Weby hostované v aplikační bráně můžou také podporovat snižování zátěže TLS s rozšířením Indikace názvu serveru (SNI) TLS. Tento scénář znamená, že klientský prohlížeč a back-endová webová farma musí podporovat HTTP/1.1 a rozšíření protokolu TLS, jak je definováno v dokumentu RFC 6066.

## <a name="listener-configuration-element"></a>Konfigurační prvek naslouchacího procesu

Existující prvky konfigurace HTTPListener jsou vylepšené pro podporu názvů hostitelů a prvků indikace názvu serveru. Používá se Application Gateway ke směrování provozu do příslušného back-endu. 

Následující příklad kódu je fragment prvku HttpListeners ze souboru šablony:

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

V pravidle směrování není vyžadována žádná změna. Stále byste měli volit pravidlo směrování „Základní“ k provázání příslušných naslouchacích procesů webů s odpovídajícími back-endovými fondy adres.

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

