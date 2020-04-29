---
title: Konfigurace portů s vysokou dostupností pro Azure Load Balancer
titleSuffix: Azure Load Balancer
description: Naučte se používat porty vysoké dostupnosti pro interní provoz vyrovnávání zatížení na všech portech.
services: load-balancer
documentationcenter: na
author: rdhillon
manager: narayan
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: allensu
ms.openlocfilehash: c6b8ecb443408f23ae604bd9c8139cb0a2afcd12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477778"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Konfigurace portů s vysokou dostupností pro interní nástroj pro vyrovnávání zatížení

Tento článek poskytuje příklad nasazení portů s vysokou dostupností na interním nástroji pro vyrovnávání zatížení. Další informace o konfiguracích specifických pro síťová virtuální zařízení (síťová virtuální zařízení) najdete v tématu odpovídající weby poskytovatele.

>[!NOTE]
>Azure Load Balancer podporuje dva různé typy: Basic a Standard. Tento článek popisuje Standard Load Balancer. Další informace o základních Load Balancer najdete v tématu [přehled Load Balancer](load-balancer-overview.md).

Ilustrace znázorňuje následující konfiguraci příkladu nasazení popsanou v tomto článku:

- Síťová virtuální zařízení se nasazují do fondu back-endu interního nástroje pro vyrovnávání zatížení za konfigurací portů s vysokou dostupností. 
- Uživatelem definovaná trasa (UDR) použitá v podsíti DMZ směruje veškerý provoz do síťová virtuální zařízení tím, že jako virtuální IP adresu interního nástroje pro vyrovnávání zatížení provedou další segment směrování. 
- Interní nástroj pro vyrovnávání zatížení distribuuje provoz do jednoho z aktivních síťová virtuální zařízení podle algoritmu nástroje pro vyrovnávání zatížení.
- SÍŤOVÉ virtuální zařízení zpracuje provoz a předá ho původnímu cíli v back-endové podsíti.
- Pokud je v back-endové podsíti nakonfigurovaný odpovídající UDR, může návratová cesta trvat stejnou cestu. 

![Příklady nasazení portů s vysokou dostupností](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Konfigurace portů s vysokou dostupností

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud chcete nakonfigurovat porty s vysokou dostupností, nastavte interní nástroj pro vyrovnávání zatížení s síťová virtuální zařízení ve fondu back-endu. Nastavte odpovídající konfiguraci sondy stavu nástroje pro vyrovnávání zatížení pro zjištění stavu síťové virtuální zařízení a pravidla nástroje pro vyrovnávání zatížení s porty s vysokou dostupností. Konfigurace související s obecným nástrojem pro vyrovnávání zatížení je popsaná v části [Začínáme](load-balancer-get-started-ilb-arm-portal.md). Tento článek popisuje konfiguraci portů vysoké dostupnosti.

Konfigurace v podstatě zahrnuje nastavení portu front-end a back-endové hodnoty portu na hodnotu **0**. Nastavte hodnotu protokolu na **vše**. Tento článek popisuje, jak nakonfigurovat porty s vysokou dostupností pomocí Azure Portal, PowerShellu a rozhraní příkazového řádku Azure CLI.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Konfigurace pravidla nástroje pro vyrovnávání zatížení portů s vysokou dostupností pomocí Azure Portal

Pokud chcete nakonfigurovat porty s vysokou dostupností pomocí Azure Portal, zaškrtněte políčko **porty ha** . Je-li tento příkaz vybrán, je automaticky vyplněna konfigurace souvisejícího portu a protokolu. 

![Konfigurace portů s vysokou dostupností prostřednictvím Azure Portal](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Konfigurace pravidla vyrovnávání zatížení portů s vysokou dostupností prostřednictvím šablony Správce prostředků

Porty s vysokou dostupností můžete nakonfigurovat pomocí verze 2017-08-01 rozhraní API pro Microsoft. Network/loadBalancers v prostředku Load Balancer. Následující fragment kódu JSON znázorňuje změny v konfiguraci nástroje pro vyrovnávání zatížení pro porty s vysokou dostupností prostřednictvím REST API:

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Konfigurace pravidla nástroje pro vyrovnávání zatížení portů s vysokou dostupností pomocí prostředí PowerShell

Pomocí následujícího příkazu vytvořte pravidlo nástroje pro vyrovnávání zatížení portů vysoké dostupnosti při vytváření interního nástroje pro vyrovnávání zatížení pomocí PowerShellu:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Konfigurace pravidla nástroje pro vyrovnávání zatížení portů s vysokou dostupností pomocí Azure CLI

V kroku 4 [Vytvoření sady interního nástroje pro vyrovnávání zatížení](load-balancer-get-started-ilb-arm-cli.md)pomocí následujícího příkazu vytvořte pravidlo nástroje pro vyrovnávání zatížení pro porty s vysokou dostupností:

```azurecli
az network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [portech s vysokou dostupností](load-balancer-ha-ports-overview.md).