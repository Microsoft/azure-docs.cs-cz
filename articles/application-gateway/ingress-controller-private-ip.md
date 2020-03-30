---
title: Použití privátní IP adresy pro interní směrování pro koncový bod příchozího přenosu dat
description: Tento článek obsahuje informace o tom, jak používat privátní IP adresy pro interní směrování a tedy vystavení koncového bodu příchozího přenosu dat v rámci clusteru zbytku virtuální sítě.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795497"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Použití privátní IP adresy pro interní směrování pro koncový bod příchozího přenosu dat 

Tato funkce umožňuje vystavit koncový bod `Virtual Network` příchozího přenosu dat v rámci pomocí privátní IP adresy.

## <a name="pre-requisites"></a>Požadavky  
Aplikační brána s [konfigurací privátní IP adresy](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)

Existují dva způsoby, jak nakonfigurovat řadič pro použití privátní IP pro příchozí přenos dat,

## <a name="assign-to-a-particular-ingress"></a>Přiřazení k určitému příchozímu přenosu dat
Chcete-li vystavit konkrétní příchozí přenos dat [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) přes privátní IP, použijte poznámky v příchozím přenosu dat.

### <a name="usage"></a>Využití
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

Pro aplikační brány bez privátní IP adresy `appgw.ingress.kubernetes.io/use-private-ip: "true"` ingresses s notated s budou ignorovány. To bude uvedeno v příchozím přenosu dat události a AGIC pod protokolu.

* Chyba, jak je uvedeno v události příchozího přenosu dat

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Chyba, jak je uvedeno v protokolech AGIC

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Přiřadit globálně
V případě, že požadavek je omezit všechny příchozí být `appgw.usePrivateIP: true` `helm` vystaveny přes soukromé IP, použití v config.

### <a name="usage"></a>Využití
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

To způsobí, že řadič příchozího přenosu dat filtruje konfigurace IP adres pro privátní IP adresu při konfiguraci front-endových posluchačů v bráně aplikace.
AGIC bude panika `usePrivateIP: true` a selhání, pokud a není přiřazena žádná soukromá IP adresa.

> [!NOTE]
> Aplikační brána v2 Skladová položka vyžaduje veřejnou IP adresu. Pokud požadujete, aby byla aplikační [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) brána soukromá, připojte k podsíti aplikační brány a zamezte provozu.
