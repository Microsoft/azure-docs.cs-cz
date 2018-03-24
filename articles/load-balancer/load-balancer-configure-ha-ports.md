---
title: Konfigurace portů pro vysokou dostupnost pro vyrovnávání zatížení Azure | Microsoft Docs
description: Naučte se používat porty vysokou dostupnost pro interní přenosů na všech portech služby Vyrovnávání zatížení
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/20178
ms.author: kumud
ms.openlocfilehash: 7f7f8e254e0ed0556446e7b08eaf46ec59977f62
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Konfigurace portů pro vysokou dostupnost pro interní nástroj

Tento článek obsahuje příklad nasazení vysoké dostupnosti portů na interní nástroj. Další informace o konfiguracích, které jsou specifické pro síťových virtuálních zařízení (NVAs) najdete na webech odpovídající zprostředkovatele.

>[!NOTE]
>Azure Load Balancer podporuje dva různé typy: Basic a Standard. Tento článek popisuje nástroj pro vyrovnávání zatížení. Další informace o základní nástroj pro vyrovnávání zatížení najdete v tématu [přehled nástroje pro vyrovnávání zatížení](load-balancer-overview.md).

Na obrázku vidíte příklad nasazení, které jsou popsané v tomto článku následující konfiguraci:

- Ve fondu back endové služby Vyrovnávání zatížení interní za vysokou dostupnost porty konfigurace jsou nasazeny NVAs. 
- Trasy definované uživatelem (UDR) použít na směrování podsítí DMZ veškerý provoz NVAs tím, že další směrování jako interní virtuální IP adresy služby Vyrovnávání zatížení. 
- Nástroje pro vyrovnávání zatížení pro vnitřní distribuuje provoz do jednoho z active NVAs podle algoritmus Vyrovnávání zatížení.
- Hodnocení chyb zabezpečení zpracovává provoz a předává do původního cíle v podsíti back-end.
- Pokud odpovídající UDR je nakonfigurovaný v back-end podsíť, může trvat návratový cesta stejné trasy. 

![Příklad nasazení vysoké dostupnosti porty](./media/load-balancer-configure-ha-ports/haports.png)


## <a name="preview-sign-up"></a>Náhled registrace

K účasti ve verzi preview funkce vysokou dostupnost porty v nástroj pro vyrovnávání zatížení standardní Azure, zaregistrujte své předplatné k získání přístupu pomocí Azure CLI 2.0 nebo prostředí PowerShell. Registrace předplatného pro [standardní Preview nástroje pro vyrovnávání zatížení](https://aka.ms/lbpreview#preview-sign-up).

>[!NOTE]
>Registrace standardní Preview nástroje pro vyrovnávání zatížení může trvat až jednu hodinu.

## <a name="configure-high-availability-ports"></a>Konfigurace portů pro vysokou dostupnost

Konfigurace portů vysokou dostupnost, nastavení Vyrovnávání zatížení interní NVAs ve fondu back-end. Nastavte odpovídající konfiguraci služby Vyrovnávání zatížení stavu test ke zjištění stavu hodnocení chyb zabezpečení a pravidlo Vyrovnávání zatížení s vysokou dostupností porty. Je součástí konfigurace související se nástroj pro vyrovnávání zatížení Obecné [Začínáme](load-balancer-get-started-ilb-arm-portal.md). V tomto článku jsou vysvětlené v konfiguraci vysoké dostupnosti porty.

Konfigurace v podstatě zahrnuje nastavení front-end port a back-end port hodnota, která má **0**. Nastavte na hodnotu protokol **všechny**. Tento článek popisuje postup konfigurace portů pro vysokou dostupnost pomocí portálu Azure, PowerShell a Azure CLI 2.0.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Pravidlo Vyrovnávání zatížení vysoké dostupnosti porty nakonfigurovat na portálu Azure

Chcete-li konfigurovat vysokou dostupnost porty pomocí portálu Azure, vyberte **HA porty** zaškrtávací políčko. Pokud vybraná, se automaticky vyplní odpovídající konfigurační protokol a port. 

![Konfigurace portů vysokou dostupnost prostřednictvím portálu Azure](./media/load-balancer-configure-ha-ports/haports-portal.png)


### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Konfigurace pravidla Vyrovnávání zatížení porty vysokou dostupnost prostřednictvím šablony Resource Manageru

Vysoká dostupnost porty můžete nakonfigurovat pomocí 2017-08-01 verze rozhraní API pro Microsoft.Network/loadBalancers v prostředku nástroj pro vyrovnávání zatížení. Následující fragment kódu JSON znázorňuje změny v konfiguraci služby Vyrovnávání zatížení pro vysokou dostupnost porty přes REST API:

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

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Konfigurace se pravidlo Vyrovnávání zatížení vysoké dostupnosti portů pomocí prostředí PowerShell

Vytvořit pravidlo Vyrovnávání zatížení vysokou dostupnost porty při vytvoření nástroje pro vyrovnávání zatížení pro vnitřní pomocí prostředí PowerShell, použijte následující příkaz:

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli-20"></a>Pravidlo Vyrovnávání zatížení vysoké dostupnosti porty nakonfigurovat 2.0 rozhraní příkazového řádku Azure

V kroku 4 [vytvořit sadu Nástroje pro vyrovnávání zatížení pro vnitřní](load-balancer-get-started-ilb-arm-cli.md), použijte následující příkaz k vytvoření pravidla služby load balancer vysokou dostupnost porty:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Další postup

Další informace o [vysokou dostupnost porty](load-balancer-ha-ports-overview.md).
