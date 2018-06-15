---
title: Vytvoření interního nástroje pro vyrovnávání zatížení pro Azure Cloud Services | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit interní nástroj pro vyrovnávání zatížení pomocí prostředí PowerShell v modelu nasazení Classic
services: load-balancer
documentationcenter: na
author: genlin
manager: cshepard
tags: azure-service-management
ms.assetid: 57966056-0f46-4f95-a295-483ca1ad135d
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 96a7c5b21d78dcc84a32fbfc8835542d64a5d7af
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34067754"
---
# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>Začínáme vytvářet interní nástroj pro vyrovnávání zatížení (Classic) pro cloudové služby

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Cloudové služby](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../azure-resource-manager/resource-manager-deployment-model.md).  Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zjistěte, jak [provést tento postup pomocí modelu Resource Manageru](load-balancer-get-started-ilb-arm-ps.md).

## <a name="configure-internal-load-balancer-for-cloud-services"></a>Konfigurace interního nástroje pro vyrovnávání zatížení pro cloudové služby

Interní nástroj pro vyrovnávání zatížení je podporován pro virtuální počítače i cloudové služby. Koncový bod interního nástroje pro vyrovnávání zatížení vytvořený v cloudové službě, která je mimo regionální virtuální síť, bude dostupný pouze v rámci této cloudové služby.

Konfigurace interního nástroje pro vyrovnávání zatížení musí být nastavena během vytváření prvního nasazení v cloudové službě, jak znázorňuje ukázka níže.

> [!IMPORTANT]
> Předpokladem pro spuštění níže uvedených kroků je již vytvořená virtuální síť pro nasazení v cloudu. K vytvoření interního vyrovnávání zatížení budete potřebovat název této virtuální sítě a název podsítě.

### <a name="step-1"></a>Krok 1

V sadě Visual Studio otevřete konfigurační soubor služby (.cscfg) svého nasazení v cloudu a přidejte následující část, která vytvoří interní vyrovnávání zatížení pod poslední položkou `</Role>` pro konfiguraci sítě.

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="name of the load balancer">
        <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

Přidejme hodnoty pro soubor s konfigurací sítě, abychom ukázali, jak bude vypadat. V příkladu předpokládejme, že jste vytvořili virtuální síť s názvem test_vnet s podsítí 10.0.0.0/24 s názvem test_subnet a statickou IP adresou 10.0.0.4. Nástroj pro vyrovnávání zatížení bude mít název testLB.

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="testLB">
        <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

Další informace o schématu nástroje pro vyrovnávání zatížení najdete v tématu [Přidání nástroje pro vyrovnávání zatížení](https://msdn.microsoft.com/library/azure/dn722411.aspx).

### <a name="step-2"></a>Krok 2

Změňte definiční soubor služby (.csdef) tak, aby přidal koncové body do interního vyrovnávání zatížení. V okamžiku vytvoření instance role definiční soubor služby přidá příslušné instance rolí do interního vyrovnávání zatížení.

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
    </Endpoints>
</WorkerRole>
```

Použijme stejné hodnoty z výše uvedeného příkladu a přidejme tyto hodnoty do definičního souboru služby.

```xml
<WorkerRole name="WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
    </Endpoints>
</WorkerRole>
```

Vyrovnávání zatížení síťového provozu bude probíhat pomocí nástroje pro vyrovnávání zatížení testLB, který používá port 80 pro příchozí požadavky a odesílá provoz do instancí rolí pracovního procesu rovněž na portu 80.

## <a name="next-steps"></a>Další kroky

[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení pomocí spřažení se zdrojovou IP adresou](load-balancer-distribution-mode.md)

[Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)

