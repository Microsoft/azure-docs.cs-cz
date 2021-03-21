---
title: Pro interní směrování pro koncový bod příchozího přenosu dat použít privátní IP adresu
description: Tento článek poskytuje informace o tom, jak používat privátní IP adresy pro interní směrování a vystavit koncový bod příchozího přenosu v clusteru do zbytku virtuální sítě.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8be37ed1da0da4da3db43ef4c1cd01ed962f24ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397304"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Použití soukromé IP adresy pro interní směrování pro koncový bod příchozího přenosu dat 

Tato funkce umožňuje vystavit koncový bod příchozího přenosu dat v rámci `Virtual Network` používání privátní IP adresy.

## <a name="pre-requisites"></a>Požadavky  
Application Gateway s [konfigurací privátní IP adresy](./configure-application-gateway-with-private-frontend-ip.md)

Existují dva způsoby, jak řadič nakonfigurovat tak, aby používal privátní IP adresu pro příchozí přenosy,

## <a name="assign-to-a-particular-ingress"></a>Přiřadit ke konkrétnímu příchozímu přenosu dat
Pokud chcete zobrazit konkrétní příchozí přenos dat přes privátní IP adresu, použijte anotaci v příchozím přenosu dat [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) .

### <a name="usage"></a>Využití
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

Pro aplikační brány bez privátní IP adresy se příchozí poznámky s poznámkami `appgw.ingress.kubernetes.io/use-private-ip: "true"` budou ignorovat. Tato akce bude uvedena v protokolu událostí příchozího přenosu dat a AGIC pod.

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
V případě požadavku je nutné omezit všechny příchozí přenosy na zpřístupnění přes soukromou IP adresu, a to pomocí `appgw.usePrivateIP: true` v `helm` config.

### <a name="usage"></a>Využití
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Při konfiguraci naslouchacího procesu front-endu na Application Gateway bude adaptér příchozího přenosu dat filtrovat konfiguraci IP adres pro privátní IP adresu.
AGIC se bude tísňový a selhat, pokud `usePrivateIP: true` není přiřazená žádná privátní IP adresa.

> [!NOTE]
> SKU Application Gateway v2 vyžaduje veřejnou IP adresu. Pokud vyžadujete, Application Gateway být privátní, připojte se [`Network Security Group`](../virtual-network/network-security-groups-overview.md) k podsíti Application Gateway a omezte provoz.