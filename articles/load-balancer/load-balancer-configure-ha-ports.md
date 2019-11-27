---
title: Konfigurace portů s vysokou dostupností nástroje Azure Load Balancer
titleSuffix: Azure Load Balancer
description: Další informace o použití portů s vysokou dostupností pro interní provoz na všech portech pro vyrovnávání zatížení
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
ms.openlocfilehash: c2e787a1f81d9f3d31b981c31a0249dd362b7bb9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225410"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Konfigurace portů s vysokou dostupností pro interní load balancer

Tento článek poskytuje ukázkové nasazení portů s vysokou dostupností na službě interní služby load balancer. Další informace o konfiguracích, které jsou specifické pro síťová virtuální zařízení (Nva) najdete v odpovídající zprostředkovatel webových stránek.

>[!NOTE]
>Azure Load Balancer podporuje dva různé typy: Basic a Standard. Tento článek popisuje Load balanceru úrovně Standard. Další informace o základních Load Balancer najdete v tématu [přehled Load Balancer](load-balancer-overview.md).

Na obrázku vidíte příklad nasazení, které jsou popsané v tomto článku následující konfiguraci:

- Síťová virtuální zařízení jsou nasazené ve fondu back endové interního nástroje load balancer za konfigurace portů s vysokou dostupností. 
- Trasy definované uživatelem (UDR) použít na trasách podsítě DMZ veškerý provoz do síťových virtuálních zařízení tím, že další segment směrování jako interní virtuální IP adresu nástroje pro vyrovnávání zatížení. 
- Interní služby load balancer distribuuje provoz do jedné z aktivních síťových virtuálních zařízení podle algoritmus Vyrovnávání zatížení.
- Síťové virtuální zařízení zpracovává přenosy a předá jej na původní cíl v back endové podsítě.
- Návratový cesty může trvat stejného postupu, pokud je nakonfigurované odpovídající uživatelem definovaná TRASA v back endové podsítě. 

![Příklad nasazení portů s vysokou dostupností](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Konfigurace portů s vysokou dostupností

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Konfigurace portů s vysokou dostupností, nastavte v back endového fondu interního nástroje se síťovými virtuálními zařízeními. Nastavte odpovídající stavu testu konfiguraci nástroje pro vyrovnávání zatížení ke zjištění stavu síťového virtuálního zařízení a pravidlo nástroje pro vyrovnávání zatížení pomocí portů s vysokou dostupností. Konfigurace související s obecným nástrojem pro vyrovnávání zatížení je popsaná v části [Začínáme](load-balancer-get-started-ilb-arm-portal.md). Tento článek popisuje konfiguraci portů s vysokou dostupností.

Konfigurace v podstatě zahrnuje nastavení portu front-end a back-endové hodnoty portu na hodnotu **0**. Nastavte hodnotu protokolu na **vše**. Tento článek popisuje postup konfigurace portů s vysokou dostupností s využitím webu Azure portal, Powershellu a Azure CLI.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Konfigurace portů s vysokou dostupností pravidlo nástroje pro vyrovnávání zatížení pomocí webu Azure portal

Pokud chcete nakonfigurovat porty s vysokou dostupností pomocí Azure Portal, zaškrtněte políčko **porty ha** . Pokud je vybráno, se automaticky vyplní související konfiguraci portu a protokolu. 

![Konfigurace portů s vysokou dostupností pomocí webu Azure portal](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Konfigurace portů s vysokou dostupností pravidla Vyrovnávání zatížení pomocí šablony Resource Manageru

Porty s vysokou dostupností můžete nakonfigurovat pomocí rozhraní API verze 2017-08-01 pro Microsoft.Network/loadBalancers prostředků nástroje pro vyrovnávání zatížení. Následující fragment kódu JSON ukazuje změny v konfiguraci nástroje pro vyrovnávání zatížení pro portů s vysokou dostupností přes rozhraní REST API:

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

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Konfigurace portů s vysokou dostupností pravidlo nástroje pro vyrovnávání zatížení pomocí Powershellu

Použijte následující příkaz k vytvoření pravidla služby load balancer portů s vysokou dostupností, při vytvoření interní služby load balancer pomocí prostředí PowerShell:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Konfigurace portů s vysokou dostupností pravidlo nástroje pro vyrovnávání zatížení pomocí Azure CLI

V kroku 4 [Vytvoření sady interního nástroje pro vyrovnávání zatížení](load-balancer-get-started-ilb-arm-cli.md)pomocí následujícího příkazu vytvořte pravidlo nástroje pro vyrovnávání zatížení pro porty s vysokou dostupností:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [portech s vysokou dostupností](load-balancer-ha-ports-overview.md).