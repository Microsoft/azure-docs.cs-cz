---
title: Konfigurace časového limitu nečinnosti TCP pro nástroj pro vyrovnávání zatížení v Azure
titlesuffix: Azure Load Balancer
description: Konfigurace časového limitu nečinnosti TCP pro nástroj pro vyrovnávání zatížení
services: load-balancer
documentationcenter: na
author: kumudd
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 24a7d2354693e362d7709b8817c438555caae0e3
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256192"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Konfigurace nastavení časového limitu nečinnosti TCP pro nástroj pro vyrovnávání zatížení Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Ve výchozí konfiguraci má nástroj pro vyrovnávání zatížení Azure nastavení časového limitu nečinnosti 4 minuty. Pokud určité době nečinnosti delší než hodnota časového limitu, neexistuje žádná záruka, že se udržuje relace TCP nebo HTTP mezi klientem a cloudové služby.

Když se připojení uzavře, klientské aplikace může zobrazit následující chybová zpráva: "Nadřízené připojení bylo uzavřeno: Připojení, které mělo být zachováno bylo ukončeno serverem."

Běžnou praxí je použití udržování připojení protokolu TCP. Tento postup udržuje připojení aktivní po delší dobu. Další informace najdete v těchto [.NET příklady](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). S keep-alive povolena, jsou pakety odesílány během období nečinnosti připojení. Tyto pakety keep-alive zajistěte, aby nikdy nebude dosaženo hodnoty časového limitu nečinnosti a který se zachová připojení dlouhou dobu.

Toto nastavení funguje pro pouze příchozí připojení. Abyste se vyhnuli ztrátě připojení, musíte nakonfigurovat interval menší než nastavení časového limitu nečinnosti TCP keep-alive nebo zvyšte hodnotu časového limitu nečinnosti. Pro zajištění podpory těchto scénářů, přidali jsme podporu pro Konfigurovatelný časový limit nečinnosti. Teď ji můžete nastavit po dobu 4 až 30 minut.

Udržování připojení TCP funguje dobře pro scénáře, ve kterém výdrži baterie není omezení. Není doporučeno pro mobilní aplikace. Pomocí TCP keep-alive v mobilní aplikaci můžete vyprázdnit baterie zařízení pouze rychleji.

![Vypršení časového limitu TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

Následující části popisují, jak změnit nastavení časového limitu nečinnosti ve virtuálních počítačích a cloudových služeb.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Konfigurace časového limitu TCP pro veřejné IP na úrovni instance až 15 minut

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

Parametr `IdleTimeoutInMinutes` je volitelný. Pokud není nastavená, je výchozí časový limit 4 minuty. Rozsah přijatelný časový limit je 4 až 30 minut.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Při vytváření koncový bod Azure na virtuálním počítači nastavit časový limit nečinnosti

Chcete-li změnit nastavení časového limitu pro koncový bod, použijte následující:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Pokud chcete načíst konfiguraci časového limitu nečinnosti, použijte následující příkaz:

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Nastavit vypršení časového limitu TCP pro sady koncových bodů s vyrovnáváním zatížení

Pokud koncové body jsou součástí sady koncových bodů s vyrovnáváním zatížení, vypršení časového limitu TCP musí být nastavena na koncový bod s vyrovnáváním zatížení sadě. Příklad:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Změnit nastavení časového limitu pro cloudové služby

Sada Azure SDK můžete použít k aktualizaci svojí cloudové služby. Proveďte nastavení koncového bodu pro cloudové služby v souboru .csdef. Aktualizace časového limitu TCP pro nasazení cloudové služby vyžaduje upgrade nasazení. Výjimkou je, pokud je pouze pro veřejné IP adresy zadané vypršení časového limitu TCP. Nastavení veřejné IP adresy jsou v souboru .cscfg a můžete je aktualizovat prostřednictvím aktualizací nasazení a upgrade.

.Csdef změny nastavení koncového bodu jsou tyto:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

Změny .cscfg pro nastavení časového limitu na veřejné IP adresy jsou tyto:

```xml
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
    <InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
        <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
    </InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="rest-api-example"></a>Příklad rozhraní REST API

Vypršení časového limitu nečinnosti protokolu TCP můžete nakonfigurovat pomocí rozhraní API pro správu služby. Ujistěte se, že `x-ms-version` záhlaví je nastavené na verzi `2014-06-01` nebo novější. Aktualizuje konfiguraci serveru zadaný s vyrovnáváním zatížení vstupní koncové body na všechny virtuální počítače v nasazení.

### <a name="request"></a>Žádost

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Odpověď

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
    <InputEndpoint>
    <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
    <LocalPort>local-port-number</LocalPort>
    <Port>external-port-number</Port>
    <LoadBalancerProbe>
        <Path>path-of-probe</Path>
        <Port>port-assigned-to-probe</Port>
        <Protocol>probe-protocol</Protocol>
        <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
        <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
    </LoadBalancerProbe>
    <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
    <Protocol>endpoint-protocol</Protocol>
    <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
    <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
    <EndpointACL>
        <Rules>
        <Rule>
            <Order>priority-of-the-rule</Order>
            <Action>permit-rule</Action>
            <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
            <Description>description-of-the-rule</Description>
        </Rule>
        </Rules>
    </EndpointACL>
    </InputEndpoint>
</LoadBalancedEndpointList>
```

## <a name="next-steps"></a>Další postup

[Interní služby load balancer – přehled](load-balancer-internal-overview.md)

[Začínáme s konfigurací nástroje pro vyrovnávání zatížení přístupem k Internetu](load-balancer-get-started-internet-arm-ps.md)

[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)
