---
title: Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení Azure | Dokumentace Microsoftu
description: Postup konfigurace distribučního režimu nástroje pro vyrovnávání zatížení Azure pro podporu spřažení se zdrojovou IP adresou.
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
ms.openlocfilehash: cfca7361831734baaf150b3e19b14c7dc88def36
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043566"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení Azure

## <a name="hash-based-distribution-mode"></a>Hodnota hash na základě distribučního režimu

Výchozí režim distribuce pro nástroj pro vyrovnávání zatížení Azure je hodnota hash 5 řazené kolekce členů. Řazené kolekce členů se skládá z Zdrojová IP adresa, zdrojový port, cílová IP adresa, cílový port a typ protokolu. Hodnota hash se používá k mapování provoz do dostupných serverů a algoritmus poskytuje věrnosti pouze v rámci relace přenosu. Pakety, které jsou ve stejné relaci jsou směrované na stejnou instanci datacenter IP (DIP) za koncový bod s vyrovnáváním zatížení. Pokud se klient spustí novou relaci ze stejné Zdrojová IP adresa, zdrojový port změní a způsobí, že provoz přejít k jinému koncovému bodu vyhrazené IP adresy.

![režim základě algoritmu hash distribuce 5 řazené kolekce členů](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Režim spřažení IP zdroje

Nástroj pro vyrovnávání zatížení můžete také konfigurovat pomocí zdrojové IP přidružení distribučního režimu. Tento režim distribuce je označované také jako spřažení relace nebo spřažení klienta IP adresou. 2-řazené kolekce členů (Zdrojová IP adresa a cílová IP adresa) používá režim nebo pokud chcete mapovat datové přenosy do dostupných serverů (Zdrojová IP adresa, cílová IP adresa a protokol typu) hash 3 řazené kolekce členů. Pomocí spřažení se zdrojovou IP adresou, připojení, které jsou spouštěné z ve stejném klientském počítači přejděte na stejný koncový bod vyhrazené IP adresy.

Následující obrázek znázorňuje konfiguraci 2 řazené kolekce členů. Všimněte si, jak běží 2-n-tice prostřednictvím nástroje pro vyrovnávání zatížení do virtuálního počítače 1 (VM1). VM1 je VM2 a VM3 zálohován.

![režim distribuce spřažení relace 2 řazené kolekce členů](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Režim spřažení IP zdroj byl odstraněn nekompatibility mezi Azure Load Balancer a Brána vzdálené plochy (Brána VP). Když použijete tento režim, můžete vytvořit farmu služby Brána VP v jedné cloudové službě.

Jiné scénáře použití není odešlete médium. Probíhá odesílání dat přes UDP, ale rovina řízení se dosahuje prostřednictvím protokolu TCP:

* Klient inicializuje relaci protokolu TCP na veřejnou adresu s vyrovnáváním zatížení a směřuje na konkrétní vyhrazené IP adresy. Kanál zůstane aktivní, aby monitorování stavu připojení.
* Zahájí se nové relace UDP ze stejného počítače klienta na stejný s vyrovnáváním zatížení veřejný koncový bod. Připojení se přesměruje na stejný koncový bod vyhrazené IP adresy jako bylo předchozí připojení TCP. Nahrávání média mohou být provedeny při vysoké propustnosti a přitom řídicí kanál prostřednictvím protokolu TCP.

> [!NOTE]
> Když se změní sadu s vyrovnáváním zatížení odebráním nebo přidání virtuálního počítače, je přepočítány distribuci požadavků od klientů. Nemůže záviset na nová připojení ze stávající klienty končí na stejném serveru. Kromě toho pomocí Zdrojová IP adresa přidružení distribučního režimu může způsobit nerovnost distribuci provozu. Klienti, kteří používají za proxy může považovat za jeden jedinečný klientské aplikace.

## <a name="configure-source-ip-affinity-settings"></a>Konfigurace nastavení spřažení zdrojové IP adresy

U virtuálních počítačů nasazených pomocí Resource Manageru změňte nastavení distribuce nástroje pro vyrovnávání zatížení na existující pravidlo Vyrovnávání zatížení pomocí prostředí PowerShell. Tím se aktualizuje režim distribuce: 

```powershell 
$lb = Get-AzureRmLoadBalancer -Name MyLb -ResourceGroupName MyLbRg 
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp' 
Set-AzureRmLoadBalancer -LoadBalancer $lb 
```

Pro klasické virtuální počítače pomocí prostředí Azure PowerShell můžete změnit nastavení distribuce. K virtuálnímu počítači přidat koncový bod Azure a konfigurace distribučního režimu nástroje pro vyrovnávání zatížení:

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Nastavte hodnotu `LoadBalancerDistribution` – element pro požadovanou velikost Vyrovnávání zatížení. Zadejte sourceIP pro vyrovnávání zatížení 2-řazené kolekce členů (Zdrojová IP adresa a cílová IP adresa). Zadejte sourceIPProtocol 3 řazené kolekce členů (Zdrojová IP adresa, cílová IP adresa a protokol typu) Vyrovnávání zatížení. Zadejte, žádný výchozí chování služby Vyrovnávání zatížení 5 řazené kolekce členů.

Získat konfiguraci koncového bodu distribuce nástroje pro vyrovnávání zatížení režimu pomocí těchto nastavení:

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

Když `LoadBalancerDistribution` prvek není k dispozici, nástroj pro vyrovnávání zatížení Azure používá výchozí algoritmus 5 řazené kolekce členů.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Konfigurace distribučního režimu na sady koncových bodů s vyrovnáváním zatížení

Když koncové body jsou součástí sady koncových bodů s vyrovnáváním zatížení, režim distribuce musí být nakonfigurovaná na sady koncových bodů s vyrovnáváním zatížení:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Konfigurovat režim distribuce pro koncové body cloudových služeb

K aktualizaci svojí cloudové služby pomocí sady Azure SDK for .NET 2.5. Nastavení koncového bodu pro cloudové služby se provádí v souboru .csdef. Pokud chcete aktualizovat distribučního režimu nástroje pro vyrovnávání zatížení pro nasazení Cloud Services, je nutné upgradovat nasazení.

Tady je příklad .csdef změn nastavení koncového bodu:

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

Následující příklad ukazuje, jak změnit konfiguraci distribučního režimu nástroje pro vyrovnávání zatížení pro zadanou sadu s vyrovnáváním zatížení v nasazení. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Změnit režim distribuce pro nasazené sady s vyrovnáváním zatížení

Chcete-li změnit existující konfiguraci nasazení použijte model nasazení Azure classic. Přidat `x-ms-version` hlavičku a hodnotu verze 2014-09-01 nastavte nebo novější.

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

Jako výš, nastavte `LoadBalancerDistribution` element sourceIP spřažení 2 řazené kolekce členů, sourceIPProtocol k přidružení 3 řazené kolekce členů nebo žádné bez přidružení (5 řazené kolekce členů spřažení).

#### <a name="response"></a>Odpověď

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Další postup

* [Přehled služby Azure Internal Load Balancer](load-balancer-internal-overview.md)
* [Začínáme s konfigurací nástroje pro vyrovnávání zatížení přístupem k Internetu](load-balancer-get-started-internet-arm-ps.md)
* [Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)
