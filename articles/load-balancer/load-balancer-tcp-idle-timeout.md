---
title: Nakonfigurujte časový limit nečinnosti TCP nástroje pro vyrovnávání zatížení | Microsoft Docs
description: Nakonfigurujte časový limit nečinnosti TCP nástroje pro vyrovnávání zatížení
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: 4625c6a8-5725-47ce-81db-4fa3bd055891
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: f19ac77f7c7f7d4ab8909d628f9dcce08c07c928
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855226"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Konfigurace nastavení časového limitu nečinnosti TCP pro službu Vyrovnávání zatížení Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Ve výchozí konfiguraci Vyrovnávání zatížení Azure má časový limit nečinnosti nastavení 4 minut. Je-li určité době nečinnosti delší než hodnota časového limitu, neexistuje žádná záruka, který se spravuje relace TCP nebo HTTP mezi klientem a cloudové služby.

Když se připojení uzavře, klientské aplikace může zobrazit následující chybová zpráva: "základní připojení bylo ukončeno: byl očekáván být zachováno připojení bylo ukončeno serverem."

Běžnou praxí je použít udržování připojení TCP. Tento postup zachová připojení active delší dobu. Další informace najdete v tématu tyto [.NET příklady](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). S udržování připojení povolena, jsou pakety odesílány době nečinnosti na připojení. Tyto udržovací pakety zajistěte, aby hodnota časového limitu nečinnosti není přístupný a se zachová připojení na dlouhou dobu.

Toto nastavení funguje pro příchozí připojení pouze. Aby nedošlo ke ztrátě připojení, musíte nakonfigurovat interval menší než nastavení pro časový limit nečinnosti udržování připojení TCP nebo zvyšte hodnotu časového limitu nečinnosti. Pro podporu takových scénářů, přidali jsme podporu pro konfigurovat časový limit nečinnosti. Nyní ji můžete nastavit po dobu 4 až 30 minut.

Udržování připojení TCP funguje dobře pro scénáře, kdy výdrž baterie není omezení. Není doporučeno pro mobilní aplikace. Používání udržování v mobilní aplikaci TCP můžou vyprázdnit baterie zařízení pouze rychlejší.

![Vypršení časového limitu TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

Následující části popisují, jak změnit nastavení časového limitu nečinnosti v virtuální počítače a cloudové služby.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Konfigurace časového limitu TCP pro vaše instance úrovni veřejné IP adresy na 15 minut

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

Parametr `IdleTimeoutInMinutes` je volitelný. Pokud není nastaven, je výchozí hodnota časového limitu 4 minuty. Rozsah přijatelný časový limit je 4 až 30 minut.

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

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Nastavit časový limit TCP na sady koncových bodů s vyrovnáváním zatížení

Pokud koncové body jsou součástí sady koncových bodů s vyrovnáváním zatížení, vypršení časového limitu TCP musí být nastavena na sady koncových bodů s vyrovnáváním zatížení. Například:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Změna nastavení časového limitu pro cloudové služby

Azure SDK můžete použít k aktualizaci cloudové služby. Vytváření nastavení koncového bodu pro cloudové služby v souboru .csdef. Aktualizace časového limitu TCP pro nasazení cloudové služby vyžaduje nasazení upgradu. Výjimkou je, pokud je časový limit TCP zadat jenom pro veřejnou IP adresu. Nastavení veřejné IP adresy jsou v souboru .cscfg a můžete je aktualizovat prostřednictvím aktualizací nasazení a upgrade.

Změny .csdef pro koncový bod nastavení jsou tyto:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

Změny .cscfg pro nastavení limitu na veřejné IP adresy jsou tyto:

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

Časový limit nečinnosti TCP můžete nakonfigurovat pomocí rozhraní API správy služby. Ujistěte se, že `x-ms-version` záhlaví je nastaven na verzi `2014-06-01` nebo novější. Aktualizujte konfiguraci zadané Vyrovnávání zatížení sítě vstupní koncové body na všechny virtuální počítače v nasazení.

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

## <a name="next-steps"></a>Další kroky

[Přehled nástroje pro vyrovnávání zatížení interní](load-balancer-internal-overview.md)

[Začněte konfiguraci Vyrovnávání zatížení internetového](load-balancer-get-started-internet-arm-ps.md)

[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)
