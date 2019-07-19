---
title: Konfigurace časového limitu nečinnosti Load Balancer TCP v Azure
titlesuffix: Azure Load Balancer
description: Konfigurace časového limitu nečinnosti Load Balancer TCP
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: b3df1ead7a3164ffd9a4b4acf8820d0f5b82cee3
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274167"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Nakonfigurujte nastavení časového limitu nečinnosti protokolu TCP pro Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

V jeho výchozí konfiguraci má Azure Load Balancer nastavení časového limitu nečinnosti na 4 minuty. Pokud je období neaktivity delší než hodnota časového limitu, není nijak zaručeno, že relace TCP nebo HTTP mezi klientem a vaší cloudovou službou bude zachovaná.

Po ukončení připojení může klientská aplikace zobrazit tuto chybovou zprávu: "Základní připojení bylo ukončeno: Připojení, které mělo být udržováno jako aktivní, bylo zavřeno serverem. "

Běžným postupem je používání udržování připojení TCP. Tento postup zachovává aktivní připojení po delší dobu. Další informace najdete v těchto [příkladech rozhraní .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Když je povolená možnost Keep-Alive, pakety se odešlou během období nečinnosti na připojení. Tyto pakety Keep-Alive zajišťují, že hodnota časového limitu nečinnosti není nikdy dosažena a připojení bude udržováno po dlouhou dobu.

Toto nastavení funguje jenom pro příchozí připojení. Aby nedošlo ke ztrátě připojení, je nutné nakonfigurovat udržování připojení TCP s intervalem nižším, než je nastavení časového limitu nečinnosti nebo zvýšit hodnotu časového limitu nečinnosti. Pro podporu takových scénářů jsme přidali podporu konfigurovatelného časového limitu nečinnosti. Teď můžete nastavit dobu trvání 4 až 30 minut.

Udržování připojení TCP je dobré i pro situace, kdy životnost baterie není omezením. Nedoporučuje se pro mobilní aplikace. Při použití udržování připojení TCP v mobilní aplikaci můžete baterii zařízení rychleji vyprázdnit.

![Časový limit TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

Následující části popisují, jak změnit nastavení časového limitu nečinnosti ve virtuálních počítačích a cloudových službách.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Nakonfigurujte časový limit TCP pro veřejnou IP adresu na úrovni instance na 15 minut.

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

Parametr `IdleTimeoutInMinutes` je volitelný. Pokud tato hodnota není nastavená, výchozí časový limit je 4 minuty. Přípustný rozsah časového limitu je 4 až 30 minut.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Nastavení časového limitu nečinnosti při vytváření koncového bodu Azure na virtuálním počítači

Chcete-li změnit nastavení časového limitu pro koncový bod, použijte následující:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Chcete-li načíst konfiguraci časového limitu nečinnosti, použijte následující příkaz:

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

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Nastavení časového limitu TCP pro sadu koncových bodů s vyrovnáváním zatížení

Pokud jsou koncové body součástí sady koncových bodů s vyrovnáváním zatížení, je nutné nastavit časový limit TCP pro sadu koncových bodů s vyrovnáváním zatížení. Příklad:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Změna nastavení časového limitu pro cloudové služby

K aktualizaci cloudové služby můžete použít sadu Azure SDK. Nastavení koncového bodu pro cloudové služby provedete v souboru. csdef. Aktualizace časového limitu TCP pro nasazení cloudové služby vyžaduje upgrade nasazení. Výjimkou je, že časový limit TCP je určen pouze pro veřejnou IP adresu. Nastavení veřejné IP adresy jsou v souboru. cscfg a můžete je aktualizovat prostřednictvím aktualizace a upgradu nasazení.

Změny. csdef pro nastavení koncového bodu jsou:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

Změny. cscfg pro nastavení timeout u veřejných IP adres:

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

## <a name="rest-api-example"></a>Příklad REST API

Časový limit nečinnosti TCP můžete nakonfigurovat pomocí rozhraní API pro správu služeb. Ujistěte se, že `x-ms-version` je záhlaví nastavené na `2014-06-01` verzi nebo novější. Aktualizuje konfiguraci zadaných vstupních koncových bodů s vyrovnáváním zatížení na všech virtuálních počítačích v nasazení.

### <a name="request"></a>Žádost

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Odpověď

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
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

## <a name="next-steps"></a>Další kroky

[Interní přehled nástroje pro vyrovnávání zatížení](load-balancer-internal-overview.md)

[Začínáme s konfigurací internetového nástroje pro vyrovnávání zatížení](load-balancer-get-started-internet-arm-ps.md)

[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)
