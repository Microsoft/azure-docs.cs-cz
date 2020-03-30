---
title: Konfigurace portů s vysokou dostupností pro Azure Load Balancer
titleSuffix: Azure Load Balancer
description: Naučte se používat porty s vysokou dostupností pro vyrovnávání zatížení interní provoz na všech portech
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477778"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Konfigurace portů s vysokou dostupností pro interní systém vyrovnávání zatížení

Tento článek obsahuje příklad nasazení portů s vysokou dostupností na interním vyrovnávání zatížení. Další informace o konfiguracích specifických pro síťová virtuální zařízení (NVA) naleznete na webových stránkách odpovídajícího poskytovatele.

>[!NOTE]
>Azure Load Balancer podporuje dva různé typy: Basic a Standard. Tento článek popisuje standardní vyrovnávání zatížení. Další informace o základním nástroje pro vyrovnávání zatížení naleznete v tématu [Přehled nástroje pro vyrovnávání zatížení](load-balancer-overview.md).

Obrázek znázorňuje následující konfiguraci příkladu nasazení popsaného v tomto článku:

- Virtuální virtuální počítače jsou nasazeny v back-endfondu interní nástroj pro vyrovnávání zatížení za konfiguraci porty s vysokou dostupností. 
- Uživatelem definovaná trasa (UDR) použitá v podsíti DMZ směruje veškerý provoz do síťových virtuálních zařízení tím, že jako virtuální IP nástroj pro interní nástroj pro vyrovnávání zatížení vytvoří další směrování. 
- Interní systém vyrovnávání zatížení distribuuje provoz do jednoho z aktivních virtuálních virtuálních vpodle algoritmu pro vyrovnávání zatížení.
- Síťové virtuální sítě zpracuje provoz a předá jej do původního cíle v podsíti back-end.
- Zpáteční cesta může trvat stejnou cestou, pokud je v podsíti back-end nakonfigurován odpovídající UDR. 

![Příklad nasazení portů s vysokou dostupností](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Konfigurace portů s vysokou dostupností

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Chcete-li konfigurovat porty s vysokou dostupností, nastavte interní systém vyrovnávání zatížení s virtuálními virtuálními virtuálními sítěmi v back-endovém fondu. Nastavte odpovídající konfiguraci sondy pro vyrovnávání zatížení pro detekci stavu NVA a pravidla nástroje pro vyrovnávání zatížení s porty s vysokou dostupností. Obecná konfigurace související s nástrojem pro vyrovnávání zatížení je popsána v části [Začínáme](load-balancer-get-started-ilb-arm-portal.md). Tento článek upozorňuje na konfiguraci portů s vysokou dostupností.

Konfigurace v podstatě zahrnuje nastavení portu front-end a hodnoty portu back-end na **0**. Nastavte hodnotu protokolu na **všechny**. Tento článek popisuje, jak nakonfigurovat porty s vysokou dostupností pomocí portálu Azure, PowerShellu a rozhraní příkazového příkazu k řešení Azure.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Konfigurace pravidla vyrovnávání zatížení portů s vysokou dostupností pomocí portálu Azure

Chcete-li konfigurovat porty s vysokou dostupností pomocí portálu Azure, zaškrtněte políčko **Porty HA.** Je-li tato možnost vybrána, bude automaticky vyplněna související konfigurace portu a protokolu. 

![Konfigurace portů s vysokou dostupností prostřednictvím portálu Azure](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Konfigurace pravidla vyrovnávání zatížení portů s vysokou dostupností pomocí šablony Správce prostředků

Porty s vysokou dostupností můžete nakonfigurovat pomocí verze rozhraní API 2017-08-01 pro Microsoft.Network/loadBalancers v prostředku vyrovnávání zatížení. Následující fragment JSON ilustruje změny v konfiguraci nástroje pro vyrovnávání zatížení pro porty s vysokou dostupností prostřednictvím rozhraní REST API:

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

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Konfigurace pravidla vyrovnávání zatížení portů s vysokou dostupností pomocí prostředí PowerShell

Pomocí následujícího příkazu vytvořte pravidlo vyrovnávání zatížení portů s vysokou dostupností při vytváření interního systému vyrovnávání zatížení pomocí prostředí PowerShell:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Konfigurace pravidla vyrovnávání zatížení portů s vysokou dostupností pomocí rozhraní příkazového příkazu Azure

V kroku 4 [vytvoření sady interního vyrovnávání zatížení](load-balancer-get-started-ilb-arm-cli.md)vytvořte pomocí následujícího příkazu pravidlo vyrovnávání zatížení portů s vysokou dostupností:

```azurecli
az network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Další kroky

Další informace o [portech s vysokou dostupností](load-balancer-ha-ports-overview.md).