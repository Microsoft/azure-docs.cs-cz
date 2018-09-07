---
title: Vytvoření pomocí rozhraní REST API služby Azure Load Balancer | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit pomocí rozhraní REST API služby Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: load-balancer
ms.date: 06/06/2018
ms.author: kumud
ms.openlocfilehash: ca952fa4fbea742121e579b28be35d834f17eade
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057021"
---
# <a name="create-an-azure-basic-load-balancer-using-rest-api"></a>Vytvoření Azure Load balancer úrovně Basic pomocí rozhraní REST API

Azure Load Balancer distribuuje nové příchozí toky, které přicházejí na front-endu nástroje pro vyrovnávání zatížení do fondu instancí back-endu, podle pravidel a sond stavu. Nástroje pro vyrovnávání zatížení je k dispozici ve dvou skladových položkách: Basic a Standard. Abyste pochopili rozdíl mezi dvě verze SKU, [SKU nástroje pro vyrovnávání zatížení porovnání](load-balancer-overview.md#skus).
 
Tento návod ukazuje, jak vytvořit objekt pomocí Azure Load balancer úrovně Basic [rozhraní Azure REST API](/rest/api/azure/) zavádějí vyrovnávat příchozí požadavek na víc virtuálních počítačů v rámci virtuální sítě Azure. Úplnou referenční dokumentaci a další ukázky jsou k dispozici v [Reference k rozhraní REST nástroje pro vyrovnávání zatížení Azure](/rest/api/load-balancer/).
 
## <a name="build-the-request"></a>Žádost o sestavení
Pomocí následující požadavek HTTP PUT můžete vytvořit nový Azure Load balancer úrovně Basic.
 ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}?api-version=2018-02-01
  ```
### <a name="uri-parameters"></a>Parametry identifikátoru URI

|Název  |V  |Požaduje se |Typ |Popis |
|---------|---------|---------|---------|--------|
|subscriptionId   |  path       |  True       |   řetězec      |  Přihlašovací údaje předplatného, které jedinečně identifikují předplatné Microsoft Azure. ID předplatného je součástí identifikátoru URI pro každé volání služby.      |
|resourceGroupName     |     path    | True        |  řetězec       |   Název skupiny prostředků.     |
|loadBalancerName     |  path       |      True   |    řetězec     |    Název nástroje pro vyrovnávání zatížení.    |
|verze API-version    |   query     |  True       |     řetězec    |  Verze rozhraní API klienta.      |



### <a name="request-body"></a>Tělo požadavku

Jediný požadovaný parametr `location`. Pokud nedefinujete *SKU* verze, základní nástroje pro vyrovnávání zatížení se vytvoří ve výchozím nastavení.  Použití [volitelné parametry](https://docs.microsoft.com/rest/api/load-balancer/loadbalancers/createorupdate#request-body) přizpůsobit nástroje pro vyrovnávání zatížení.

| Název | Typ | Popis |
| :--- | :--- | :---------- |
| location | řetězec | Umístění prostředku. Získat aktuální seznam umístění s využitím [seznamu umístění](https://docs.microsoft.com/rest/api/resources/subscriptions/listlocations) operace. |


## <a name="example-create-and-update-a-basic-load-balancer"></a>Příklad: Vytvoření a aktualizaci základní nástroje pro vyrovnávání zatížení

V tomto příkladu nejdřív vytvoření Load Balanceru úrovně Basic spolu s jeho prostředky. Dále je nutné nakonfigurovat prostředky nástroje pro vyrovnávání zatížení, které zahrnují konfigurace protokolu IP front-endu, back-endový fond adres, pravidla, sondu stavu a příchozí pravidlo NAT Vyrovnávání zatížení.

Než vytvoříte nástroj pro vyrovnávání zatížení pomocí níže uvedený příklad, vytvořte virtuální síť s názvem *vnetlb* s podsítí *subnetlb* ve skupině prostředků s názvem *rg1* v **USA – východ** umístění.

### <a name="step-1-create-a-basic-load-balancer"></a>KROK 1. Vytvoření Load Balanceru úrovně Basic
V tomto kroku vytvoříte základní pro vyrovnávání zatížení s názvem *lb* na **USA – VÝCHOD** umístění v rámci *rg1* skupinu prostředků.
#### <a name="sample-request"></a>Ukázková žádost

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>Tělo požadavku

  ```JSON
   {
    "location": "eastus",
   }
  ```
### <a name="step-2-configure-load-balancer-resources"></a>KROK 2. Konfigurace prostředků nástroje pro vyrovnávání zatížení
V tomto kroku nakonfigurujete nástroj pro vyrovnávání zatížení *lb* prostředky, které zahrnují front-endová konfigurace protokolu IP (*fe-lb*), back-endového fondu adres (*být lb*), () pravidlo Vyrovnávání zatížení *rulelb*), sondy stavu (*sondy lb*) a příchozího pravidla NAT (*v nat-rule*).
#### <a name="sample-request"></a>Ukázková žádost

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>Tělo požadavku

  ```JSON
{
  "properties": {
    "frontendIPConfigurations": [
      {
        "name": "fe-lb",
        "properties": {
          "subnet": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnetlb/subnets/subnetlb"
          },
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ],
          "inboundNatRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/inboundNatRules/in-nat-rule"
            }  ]  }  }  ],
    "backendAddressPools": [
      {
        "name": "be-lb",
        "properties": {
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]   }   }   ],
    "loadBalancingRules": [
      {
        "name": "rulelb",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 80,
          "backendPort": 80,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp",
          "loadDistribution": "Default",
          "backendAddressPool": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/be-lb"
          },
          "probe": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/probes/probe-lb"
          }  }  }  ],
    "probes": [
      {
        "name": "probe-lb",
        "properties": {
          "protocol": "Http",
          "port": 80,
          "requestPath": "healthcheck.aspx",
          "intervalInSeconds": 15,
          "numberOfProbes": 2,
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]  }  } ],
    "inboundNatRules": [
      {
        "name": "in-nat-rule",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 3389,
          "backendPort": 3389,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp"
        } } ],
    "inboundNatPools": [],
    "outboundNatRules": []
  }  }
```
