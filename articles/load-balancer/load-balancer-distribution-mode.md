---
title: Konfigurace pro vyrovnávání zatížení Azure režim distribuce | Microsoft Docs
description: Postup konfigurace režim distribuce nástroje pro vyrovnávání zatížení Azure pro podporu spřažení IP zdroje.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 7df27a4d-67a8-47d6-b73e-32c0c6206e6e
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ae793bad9cef86158418eb87e0c38ee0370a6bd2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30176971"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Nakonfigurujte distribuční režim pro vyrovnávání zatížení Azure

## <a name="hash-based-distribution-mode"></a>Režim distribuce na základě hodnoty hash

Výchozí režim distribuce nástroje pro vyrovnávání zatížení Azure je hodnota hash 5 řazené kolekce členů. N-tice se skládá z zdrojové IP adresy, zdrojového portu, cílové adresy IP, cílový port a typ protokolu. Hodnota hash se používá k mapování provoz do dostupných serverů a algoritmus poskytuje věrnosti pouze v rámci relace přenosu. Pakety, které jsou ve stejné relaci jsou směrované na stejnou instanci datacenter IP (DIP) za vyrovnáváním zatížení koncového bodu. Když se klient spustí novou relaci ze stejné zdrojové IP adresy, zdrojového portu změny a způsobí, že provoz přejít k jinému koncovému bodu vyhrazené IP adresy.

![režim na základě hodnoty hash distribuce 5 řazené kolekce členů](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Režim spřažení IP zdroje

Nástroj pro vyrovnávání zatížení můžete nakonfigurovat také pomocí režim distribuce zdrojové IP spřažení. Tento režim distribuce se taky říká spřažení relace nebo spřažení IP klienta. Režim využívá 2-n-tice (zdrojové IP adresy a cílovou IP adresu) nebo 3 řazené kolekce členů (zdrojová adresa IP, cílovou IP adresu a protokolu typ) hash pro mapování provoz do dostupných serverů. Pomocí zdrojové IP spřažení připojení, která se spouští z ve stejném klientském počítači přejděte na stejný koncový bod vyhrazené IP adresy.

Následující obrázek znázorňuje konfiguraci 2 řazené kolekce členů. Všimněte si, jak 2-n-tice spustí pomocí nástroje pro vyrovnávání zatížení do virtuálního počítače 1 (VM1). VM1 se zálohuje virtuálního počítače 2 a VM3.

![režim distribuce spřažení relace 2 řazené kolekce členů](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Režim spřažení IP zdroj byl odstraněn nekompatibilitou nástroj pro vyrovnávání zatížení Azure a Brána vzdálené plochy (Brána VP). Pomocí tohoto režimu, můžete vytvořit farmu služby Brána VP v jednom cloudové službě.

Další scénář případu využití je nahrávání média. Nahrání dat se nakonfigurují UDP, ale rovině řízení je dosaženo pomocí TCP:

* Klient inicializuje relaci TCP na veřejnou adresu Vyrovnávání zatížení sítě a směřuje na konkrétní vyhrazené IP adresy. Kanál zůstane aktivní, monitorování stavu připojení.
* Novou relaci UDP ze stejného počítače klienta se zahájí na stejné Vyrovnávání zatížení sítě veřejný koncový bod. Připojení se přesměruje na stejný koncový bod vyhrazené IP adresy jako bylo předchozí připojení TCP. Nahrávání média můžete provedeny na vysokou propustnost při zachování řídicí kanál prostřednictvím TCP.

> [!NOTE]
> Při změně sady vyrovnáváním zatížení odebráním nebo přidání virtuálního počítače je přepočítávány distribuci požadavky klientů. Nemůže záviset na nové připojení z existující klienti skončili na stejném serveru. Kromě toho pomocí zdrojové IP adresy režim distribuce spřažení může způsobit jako nerovné distribučního provozu. Klienti používající za proxy může považovat za jeden jedinečných klientských aplikací.

## <a name="configure-source-ip-affinity-settings"></a>Konfigurace nastavení spřažení zdrojové IP adresy

Ke změně nastavení časového limitu pro virtuální počítače, použijte prostředí Azure PowerShell. Přidání koncového bodu Azure k virtuálnímu počítači a nakonfigurujte režim distribuce nástroje pro vyrovnávání zatížení:

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Nastavte hodnotu `LoadBalancerDistribution` element pro požadovanou velikost Vyrovnávání zatížení. Zadejte sourceIP pro vyrovnávání zatížení 2-n-tice (zdrojové IP adresy a cílovou IP adresu). Zadejte sourceIPProtocol 3 řazené kolekce členů (zdrojová adresa IP, cílovou IP adresu a protokolu typ) Vyrovnávání zatížení. Zadejte žádná pro výchozí chování Vyrovnávání zatížení 5 řazené kolekce členů.

Načíst konfiguraci režimu vyrovnávání distribuční koncový bod zatížení pomocí těchto nastavení:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

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
    LoadBalancerDistribution : sourceIP

Když `LoadBalancerDistribution` element není k dispozici, nástroj pro vyrovnávání zatížení Azure používá výchozí algoritmus 5 řazené kolekce členů.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Nakonfigurovat režim distribuce na sady koncových bodů s vyrovnáváním zatížení

U koncových bodů jsou součástí sady koncových bodů s vyrovnáváním zatížení, musí být režim distribuce nastaveny na sady koncových bodů s vyrovnáváním zatížení:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Konfigurovat režim distribuce pro koncové body cloudové služby

Sada Azure SDK pro .NET 2.5 použijte k aktualizaci cloudové služby. Nastavení koncového bodu pro cloudové služby jsou vytvářeny v souboru .csdef. K aktualizaci režim distribuce nástroje pro vyrovnávání zatížení pro nasazení cloudové služby, je požadovaná nasazení upgradu.

Tady je příklad změny .csdef pro koncový bod nastavení:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
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

## <a name="api-example"></a>Příklad rozhraní API

Následující příklad ukazuje, jak znovu nakonfigurovat režim distribuce nástroje pro vyrovnávání zatížení pro zadanou sadu Vyrovnávání zatížení v nasazení. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Změnit režim distribuce pro nasazené sady s vyrovnáváním zatížení

Chcete-li změnit existující konfiguraci nasazení pomocí modelu nasazení Azure classic. Přidat `x-ms-version` hlavičku a hodnotu verze 2014-09-01 nastavte nebo novější.

#### <a name="request"></a>Žádost

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

Jako výše popsané, můžete nastavit `LoadBalancerDistribution` element sourceIP pro spřažení 2 řazené kolekce členů, sourceIPProtocol pro spřažení 3 řazené kolekce členů nebo žádná pro bez přidružení (5 řazené kolekce členů spřažení).

#### <a name="response"></a>Odpověď

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Další postup

* [Přehled služby Azure interní nástroj pro vyrovnávání zatížení](load-balancer-internal-overview.md)
* [Začínáme s konfigurace vyrovnávání zatížení internetového](load-balancer-get-started-internet-arm-ps.md)
* [Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)
