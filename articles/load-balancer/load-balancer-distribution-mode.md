---
title: Konfigurace distribučního režimu Azure Load Balancer
titleSuffix: Azure Load Balancer
description: V tomto článku začnete s konfigurací distribučního režimu pro Azure Load Balancer podporovat spřažení zdrojové IP adresy.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 4e4fa9819343c90b5639fc485af8acb48968d62e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695652"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Konfigurace režimu distribuce pro Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Režim distribuce založený na hodnotě hash

Výchozí režim distribuce pro Azure Load Balancer využívá hodnotu hash, kterou tvoří řazená kolekce pěti členů. 

Řazená kolekce členů se skládá z těchto:
* **Zdrojová IP adresa**
* **Zdrojový port**
* **Cílová IP adresa**
* **Cílový port**
* **Typ protokolu**

Hodnota hash se používá k mapování provozu na dostupné servery. Algoritmus poskytuje vytrvalost jenom v rámci relace přenosu. Pakety, které jsou ve stejné relaci, se přesměrují na stejnou IP adresu datového centra za koncovým bodem s vyrovnáváním zatížení. Když klient spustí novou relaci ze stejné zdrojové IP adresy, změní se zdrojový port a způsobí, že provoz přejde na jiný koncový bod datového centra.

![Distribuční režim s pěti řazenými kolekcemi členů založený na hodnotě hash](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Režim spřažení zdrojové IP adresy

Nástroj pro vyrovnávání zatížení se dá nakonfigurovat taky pomocí distribučního režimu spřažení zdrojové IP adresy. Tento režim distribuce se také označuje jako spřažení relací nebo spřažení klientských IP adres. Režim používá k mapování provozu na dostupné servery dvě řazené kolekce členů (zdrojová IP adresa a cílová IP adresa) nebo tři řazené kolekce členů (zdrojová adresa IP, cílová IP adresa a typ protokolu). Při použití spřažení zdrojového protokolu IP se připojení spouštěná ze stejného klientského počítače přejdou na stejný koncový bod datového centra.

Následující obrázek znázorňuje konfiguraci se dvěma řazenými kolekcemi členů. Všimněte si, jak se dvě řazené kolekce členů spustí prostřednictvím nástroje pro vyrovnávání zatížení, do virtuálního počítače 1 (VM1). VM1 je pak zálohovaný pomocí VM2 a VM3.

![Distribuční režim spřažení relace se dvěma řazenými kolekcemi členů](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Režim spřažení zdrojové IP adresy řeší nekompatibilitu mezi Azure Load Balancer a Brána vzdálené plochy (Brána VP). Pomocí tohoto režimu můžete vytvořit Brána VPovou farmu v jedné cloudové službě.

Dalším scénářem použití je nahrávání médií. Nahrávání dat probíhá prostřednictvím protokolu UDP, ale Řídicí rovina se dosahuje prostřednictvím protokolu TCP:

* Klient spustí relaci TCP na veřejné adrese s vyrovnáváním zatížení a přesměruje na konkrétní DIP. Kanál zůstane aktivní a monitoruje stav připojení.
* Ve stejném klientském koncovém bodu s vyrovnáváním zatížení je spuštěná nová relace UDP ze stejného klientského počítače. Připojení je směrováno na stejný koncový bod DIP jako předchozí připojení TCP. Nahrávání médií je možné spustit při vysoké propustnosti a přitom zachovat řídicí kanál prostřednictvím protokolu TCP.

> [!NOTE]
> Dojde-li ke změně sady s vyrovnáváním zatížení odebráním nebo přidáním virtuálního počítače, bude přepočítána distribuce požadavků klientů. Nemůžete záviset na nových připojeních od stávajících klientů po ukončení na stejném serveru. Kromě toho může použití distribučního režimu spřažení zdrojového protokolu IP způsobit nerovnost distribuce provozu. Klienti, kteří se spouštějí za proxy servery, se mohou zobrazit jako jedna jedinečná klientská aplikace.

## <a name="configure-source-ip-affinity-settings"></a>Konfigurovat nastavení spřažení zdrojové IP adresy

### <a name="azure-portal"></a>portál Azure

Konfiguraci režimu distribuce můžete změnit úpravou pravidla vyrovnávání zatížení na portálu.

1. Přihlaste se k Azure Portal a vyhledejte skupinu prostředků obsahující nástroj pro vyrovnávání zatížení, který chcete změnit kliknutím na **skupiny prostředků**.
2. Na obrazovce Přehled nástroje pro vyrovnávání zatížení klikněte v části **Nastavení** na **pravidla vyrovnávání zatížení** .
3. Na obrazovce pravidla vyrovnávání zatížení klikněte na pravidlo vyrovnávání zatížení, u kterého chcete změnit režim distribuce.
4. V rámci pravidla se režim distribuce změní změnou rozevíracího seznamu **trvalá relace** .  Dostupné jsou tyto možnosti:
    
    * **Žádný (založený na hodnotě hash)** – určuje, že úspěšné požadavky ze stejného klienta můžou být zpracovávány jakýmkoli virtuálním počítačem.
    * **IP adresa klienta (spřažení zdrojové IP adresy 2 – řazená kolekce členů)** – určuje, že úspěšné požadavky ze stejné IP adresy klienta budou zpracovávány stejným virtuálním počítačem.
    * **IP adresa klienta a protokol (přidružení zdrojové IP adresy 3 – řazená kolekce členů)** – určuje, že po jednom virtuálním počítači bude zpracována úspěšná žádost ze stejné kombinace IP adresy klienta a protokolu.

5. Zvolte režim distribuce a pak klikněte na **Uložit**.

### <a name="azure-powershell"></a>Azure PowerShell

Pro virtuální počítače nasazené s Správce prostředků použijte PowerShell ke změně nastavení distribuce nástroje pro vyrovnávání zatížení pro existující pravidlo vyrovnávání zatížení. Následující příkaz aktualizuje distribuční režim: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

U klasických virtuálních počítačů použijte Azure PowerShell ke změně nastavení distribuce. Přidání koncového bodu Azure do virtuálního počítače a konfigurace distribučního režimu nástroje pro vyrovnávání zatížení:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Nastavte hodnotu `LoadBalancerDistribution` prvku pro požadovanou velikost vyrovnávání zatížení. Zadejte sourceIP pro vyrovnávání zatížení se dvěma řazenými kolekcemi členů (zdrojová IP adresa a cílová IP adresa). Pro vyrovnávání zatížení zadejte sourceIPProtocol pro tři řazené kolekce členů (zdrojová adresa IP, cílová IP adresa a typ protokolu). Pro výchozí chování při vyrovnávání zatížení s pěti řazenými kolekcemi členů zadejte None.

Pomocí těchto nastavení načtěte konfiguraci distribučního režimu nástroje pro vyrovnávání zatížení koncového bodu:

```azurepowershell
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
```

Pokud `LoadBalancerDistribution` prvek není k dispozici, Azure Load Balancer používá výchozí algoritmus pět-řazené kolekce členů.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Konfigurace distribučního režimu pro sadu koncových bodů s vyrovnáváním zatížení

Pokud jsou koncové body součástí sady koncových bodů s vyrovnáváním zatížení, musí být distribuční režim nakonfigurován v sadě koncových bodů s vyrovnáváním zatížení:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Konfigurace distribučního režimu pro koncové body Cloud Services

Aktualizujte svou cloudovou službu pomocí sady Azure SDK pro .NET 2,5. Nastavení koncového bodu pro Cloud Services jsou vytvořena v souboru. csdef. Pro aktualizaci distribučního režimu nástroje pro vyrovnávání zatížení pro nasazení Cloud Services se vyžaduje upgrade nasazení.

Tady je příklad. csdef změn pro nastavení koncového bodu:

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

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Změnit režim distribuce pro nasazenou sadu s vyrovnáváním zatížení

Pro změnu existující konfigurace nasazení použijte model nasazení Azure Classic. Přidejte `x-ms-version` hlavičku a nastavte hodnotu na verze 2014-09-01 nebo novější.

#### <a name="request"></a>Žádost

```http
POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
Content-Type: application/xml
```

```xml
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
```

Jak je popsáno výše, nastavte `LoadBalancerDistribution` element tak, aby sourceIP pro spřažení se dvěma řazenými kolekcemi členů, sourceIPProtocol pro spřažení se třemi řazenými kolekcemi členů, nebo žádný pro žádné spřažení (pět vztahů mezi řazenými kolekcemi členů).

#### <a name="response"></a>Odpověď

```http
HTTP/1.1 202 Accepted
Cache-Control: no-cache
Content-Length: 0
Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
x-ms-servedbyregion: ussouth2
x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
Date: Thu, 16 Oct 2014 22:49:21 GMT
```

## <a name="next-steps"></a>Další kroky

* [Azure Load Balancer – přehled](load-balancer-overview.md)
* [Začínáme s konfigurací internetového nástroje pro vyrovnávání zatížení](quickstart-load-balancer-standard-public-powershell.md)
* [Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)