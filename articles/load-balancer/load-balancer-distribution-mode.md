---
title: Konfigurace režimu distribuce Azure Load Balancer
titleSuffix: Azure Load Balancer
description: V tomto článku můžete začít konfigurovat režim distribuce pro Azure Load Balancer pro podporu zdrojové spřažení IP.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 5c50186692438be5d0922cd329c28e665310e5c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023527"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Konfigurace distribučního režimu pro Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Režim distribuce založený na hash

Výchozí režim distribuce pro Azure Load Balancer využívá hodnotu hash, kterou tvoří řazená kolekce pěti členů. 

Řazená kolekce členů se skládá z:
* **Zdrojová IP adresa**
* **Zdrojový port**
* **Cílová IP adresa**
* **Cílový port**
* **Typ protokolu**

Soubor hash se používá k mapování provozu na dostupné servery. Algoritmus poskytuje lepivost pouze v rámci relace přenosu. Pakety, které jsou ve stejné relaci jsou směrovány na stejné IP datového centra za koncový bod s vyrovnáváním zatížení. Když klient spustí novou relaci ze stejné zdrojové IP adresy, zdrojový port se změní a způsobí, že provoz přejde do jiného koncového bodu datového centra.

![Režim distribuce založené na hash na základě pěti řasenek](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Režim spřažení ip adres zdroje

Výlohy na zatížení lze také nakonfigurovat pomocí režimu distribuce spřažení ip zdrojové ip adresy. Tento režim distribuce se také označuje jako spřažení relací nebo spřažení klientských IP adres. Režim používá dvě řazené kolekce členů (zdrojová IP a cílová IP) nebo tři řazené kolekce členů (zdrojová IP adresa, cílová IP adresa a typ protokolu) k mapování provozu na dostupné servery. Pomocí zdrojové spřažení IP se připojení, která jsou spuštěna ze stejného klientského počítače, přejdou do stejného koncového bodu datového centra.

Následující obrázek znázorňuje konfiguraci se dvěma řazemi do řazených členů. Všimněte si, jak dvě řazené kolekce členů procházejí nástroj pro vyrovnávání zatížení do virtuálního počítače 1 (VM1). VM1 je pak zálohována VM2 a VM3.

![Režim distribuce spřažení dvou řazených členů řazené kolekce členů](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Režim spřažení zdrojových IP adres řeší nekompatibilitu mezi nástrojem Azure Load Balancer a bránou vzdálené plochy (Brána VP). Pomocí tohoto režimu můžete vytvořit farmu služby Brána VP v jedné cloudové službě.

Dalším scénářem použití je nahrání média. Nahrávání dat se děje prostřednictvím UDP, ale řídicí rovina je dosaženo prostřednictvím TCP:

* Klient spustí relaci Protokolu TCP na veřejnou adresu s vyrovnáváním zatížení a je přesměrován na konkrétní dip. Kanál je ponechán aktivní pro sledování stavu připojení.
* Nová relace UDP ze stejného klientského počítače je spuštěna do stejného veřejného koncového bodu s vyrovnáváním zatížení. Připojení je směrováno do stejného koncového bodu DIP jako předchozí připojení TCP. Nahrávání médií lze provést s vysokou propustností při zachování řídicího kanálu prostřednictvím protokolu TCP.

> [!NOTE]
> Když se sada s vyrovnáváním zatížení změní odebráním nebo přidáním virtuálního počítače, přepočítá se distribuce požadavků klientů. Nemůžete se spolehnout na nová připojení od stávajících klientů, abyste skončili na stejném serveru. Navíc pomocí zdrojové ip spřažení distribuční režim může způsobit nerovnoměrné rozdělení provozu. Klienti, kteří běží za proxy servery může být považován za jednu jedinečnou klientskou aplikaci.

## <a name="configure-source-ip-affinity-settings"></a>Konfigurace nastavení spřažení protokolu IP

### <a name="azure-portal"></a>portál Azure

Konfiguraci distribučního režimu můžete změnit úpravou pravidla vyrovnávání zatížení na portálu.

1. Přihlaste se k portálu Azure a vyhledejte skupinu prostředků obsahující likvidátor zatížení, který chcete změnit, kliknutím na **skupiny prostředků**.
2. Na obrazovce přehledu nástroje pro vyrovnávání zatížení klikněte na **pravidla vyrovnávání zatížení** v části **Nastavení**.
3. Na obrazovce pravidla vyrovnávání zatížení klikněte na pravidlo vyrovnávání zatížení, které chcete změnit režim distribuce.
4. Podle pravidla se režim distribuce změní změnou rozevíracího pole **Trvalosti relace.**  K dispozici jsou následující možnosti:
    
    * **Žádný (na základě hash)** - Určuje, že následné požadavky od stejného klienta mohou být zpracovány libovolným virtuálním počítačem.
    * **IP klienta (zdrojová spřažení IP 2-n-tice)** - Určuje, že následné požadavky ze stejné ip adresy klienta budou zpracovány stejným virtuálním počítačem.
    * **IP a protokol klienta (zdrojová spřažení IP 3-n-tice)** - Určuje, že následné požadavky ze stejné ip adresy klienta a kombinace protokolu budou zpracovány stejným virtuálním počítačem.

5. Zvolte režim distribuce a klepněte na tlačítko **Uložit**.

### <a name="azure-powershell"></a>Azure PowerShell

U virtuálních počítačů nasazených ve Správci prostředků můžete pomocí prostředí PowerShell změnit nastavení distribuce nástroje pro vyrovnávání zatížení na existujícím pravidle vyrovnávání zatížení. Následující příkaz aktualizuje distribuční režim: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

U klasických virtuálních počítačů můžete pomocí Azure PowerShellu změnit nastavení distribuce. Přidejte koncový bod Azure do virtuálního počítače a nakonfigurujte režim distribuce nástroje pro vyrovnávání zatížení:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Nastavte hodnotu `LoadBalancerDistribution` prvku pro požadovanou hodnotu vyrovnávání zatížení. Zadejte sourceIP pro dvě n-tice (zdrojová IP a cílová IP) vyrovnávání zatížení. Zadejte sourceIPProtocol pro tři n-tice (zdrojová IP adresa, cílová IP adresa a typ protokolu) vyrovnávání zatížení. Zadejte žádné pro výchozí chování pěti řazených členů systému vyrovnávání zatížení.

Načtěte konfiguraci režimu distribučního režimu nástroje pro vyrovnávání zatížení koncového bodu pomocí těchto nastavení:

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

Když `LoadBalancerDistribution` prvek není k dispozici, Azure Balancer používá výchozí algoritmus pěti řazených členů.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Konfigurace distribučního režimu v sadě koncových bodů s vyrovnáváním zatížení

Pokud jsou koncové body součástí sady koncových bodů s vyrovnáváním zatížení, musí být režim distribuce nakonfigurován na sadě koncových bodů s vyrovnáváním zatížení:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Konfigurace distribučního režimu pro koncové body cloudových služeb

K aktualizaci cloudové služby použijte azure sdk pro .NET 2.5. Nastavení koncového bodu pro cloudové služby se provádí v souboru .csdef. Chcete-li aktualizovat režim distribuce vykladače zatížení pro nasazení cloudových služeb, je vyžadován upgrade nasazení.

Zde je příklad změn .csdef pro nastavení koncového bodu:

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

Následující příklad ukazuje, jak překonfigurovat režim distribuce vyrovnávání zatížení pro zadanou sadu s vyrovnáváním zatížení v nasazení. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Změna režimu distribuce pro nasazenou sadu s vyrovnáváním zatížení

Pomocí modelu klasického nasazení Azure můžete změnit existující konfiguraci nasazení. Přidejte `x-ms-version` záhlaví a nastavte hodnotu na verzi 2014-09-01 nebo novější.

#### <a name="request"></a>Žádost

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
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

Jak již bylo popsáno, nastavte `LoadBalancerDistribution` prvek sourceIP pro dvě n-tice spřažení, sourceIPProtocol pro tři n-tice spřažení nebo žádný pro žádné spřažení (pět n-tice spřažení).

#### <a name="response"></a>Odpověď

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Další kroky

* [Přehled interního nástroje pro vyrovnávání zatížení Azure](load-balancer-internal-overview.md)
* [Začínáme s konfigurací internetového zařízení pro vyrovnávání zatížení](quickstart-create-standard-load-balancer-powershell.md)
* [Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)
