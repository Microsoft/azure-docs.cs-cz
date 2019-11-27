---
title: Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení Azure
titleSuffix: Azure Load Balancer
description: V tomto článku začnete s konfigurací distribučního režimu pro Azure Load Balancer podporovat spřažení zdrojové IP adresy.
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
ms.openlocfilehash: ddccd02e7157792d942309ae4f74933322f246f9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225377"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Hodnota hash na základě distribučního režimu

Výchozím režimem distribuce pro Azure Load Balancer je hodnota hash s pěti řazenými kolekcemi členů. 

Řazená kolekce členů se skládá z těchto:
* **Zdrojová IP adresa**
* **Zdrojový port**
* **Cílová IP adresa**
* **Cílový port**
* **Typ protokolu**

Hodnota hash se používá k mapování provozu na dostupné servery. Algoritmus poskytuje vytrvalost jenom v rámci relace přenosu. Pakety, které jsou ve stejné relaci, se přesměrují na stejnou IP adresu datového centra za koncovým bodem s vyrovnáváním zatížení. Když klient spustí novou relaci ze stejné zdrojové IP adresy, změní se zdrojový port a způsobí, že provoz přejde na jiný koncový bod datového centra.

![Distribuční režim s pěti řazenými kolekcemi členů založený na hodnotě hash](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Režim spřažení IP zdroje

Nástroj pro vyrovnávání zatížení se dá nakonfigurovat taky pomocí distribučního režimu spřažení zdrojové IP adresy. Tento režim distribuce je označované také jako spřažení relace nebo spřažení klienta IP adresou. Režim používá k mapování provozu na dostupné servery dvě řazené kolekce členů (zdrojová IP adresa a cílová IP adresa) nebo tři řazené kolekce členů (zdrojová adresa IP, cílová IP adresa a typ protokolu). Při použití spřažení zdrojového protokolu IP se připojení spouštěná ze stejného klientského počítače přejdou na stejný koncový bod datového centra.

Následující obrázek znázorňuje konfiguraci se dvěma řazenými kolekcemi členů. Všimněte si, jak se dvě řazené kolekce členů spustí prostřednictvím nástroje pro vyrovnávání zatížení, do virtuálního počítače 1 (VM1). VM1 je VM2 a VM3 zálohován.

![Distribuční režim spřažení relace se dvěma řazenými kolekcemi členů](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Režim spřažení IP zdroj byl odstraněn nekompatibility mezi Azure Load Balancer a Brána vzdálené plochy (Brána VP). Když použijete tento režim, můžete vytvořit farmu služby Brána VP v jedné cloudové službě.

Jiné scénáře použití není odešlete médium. Probíhá odesílání dat přes UDP, ale rovina řízení se dosahuje prostřednictvím protokolu TCP:

* Klient spustí relaci TCP na veřejné adrese s vyrovnáváním zatížení a přesměruje na konkrétní DIP. Kanál zůstane aktivní, aby monitorování stavu připojení.
* Ve stejném klientském koncovém bodu s vyrovnáváním zatížení je spuštěná nová relace UDP ze stejného klientského počítače. Připojení se přesměruje na stejný koncový bod vyhrazené IP adresy jako bylo předchozí připojení TCP. Nahrávání média mohou být provedeny při vysoké propustnosti a přitom řídicí kanál prostřednictvím protokolu TCP.

> [!NOTE]
> Když se změní sadu s vyrovnáváním zatížení odebráním nebo přidání virtuálního počítače, je přepočítány distribuci požadavků od klientů. Nemůže záviset na nová připojení ze stávající klienty končí na stejném serveru. Kromě toho pomocí Zdrojová IP adresa přidružení distribučního režimu může způsobit nerovnost distribuci provozu. Klienti, kteří používají za proxy může považovat za jeden jedinečný klientské aplikace.

## <a name="configure-source-ip-affinity-settings"></a>Konfigurace nastavení spřažení zdrojové IP adresy

### <a name="azure-portal"></a>portál Azure

Konfiguraci režimu distribuce můžete změnit úpravou pravidla vyrovnávání zatížení na portálu.

1. Přihlaste se k Azure Portal a vyhledejte skupinu prostředků obsahující nástroj pro vyrovnávání zatížení, který chcete změnit kliknutím na **skupiny prostředků**.
2. Na obrazovce Přehled nástroje pro vyrovnávání zatížení klikněte v části **Nastavení**na **pravidla vyrovnávání zatížení** .
3. Na obrazovce pravidla vyrovnávání zatížení klikněte na pravidlo vyrovnávání zatížení, u kterého chcete změnit režim distribuce.
4. V rámci pravidla se režim distribuce změní změnou rozevíracího seznamu **trvalá relace** .  K dispozici jsou následující možnosti:
    
    * **Žádný (založený na hodnotě hash)** – určuje, že úspěšné požadavky ze stejného klienta můžou být zpracovávány jakýmkoli virtuálním počítačem.
    * **IP adresa klienta (spřažení zdrojové IP adresy 2 – řazená kolekce členů)** – určuje, že úspěšné požadavky ze stejné IP adresy klienta budou zpracovávány stejným virtuálním počítačem.
    * **IP adresa klienta a protokol (přidružení zdrojové IP adresy 3 – řazená kolekce členů)** – určuje, že po jednom virtuálním počítači bude zpracována úspěšná žádost ze stejné kombinace IP adresy klienta a protokolu.

5. Zvolte režim distribuce a pak klikněte na **Uložit**.

### <a name="azure-powershell"></a>Azure Powershell

Pro virtuální počítače nasazené s Správce prostředků použijte PowerShell ke změně nastavení distribuce nástroje pro vyrovnávání zatížení pro existující pravidlo vyrovnávání zatížení. Následující příkaz aktualizuje distribuční režim: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Pro klasické virtuální počítače pomocí prostředí Azure PowerShell můžete změnit nastavení distribuce. K virtuálnímu počítači přidat koncový bod Azure a konfigurace distribučního režimu nástroje pro vyrovnávání zatížení:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Nastavte hodnotu prvku `LoadBalancerDistribution` pro požadovanou velikost vyrovnávání zatížení. Zadejte sourceIP pro vyrovnávání zatížení se dvěma řazenými kolekcemi členů (zdrojová IP adresa a cílová IP adresa). Pro vyrovnávání zatížení zadejte sourceIPProtocol pro tři řazené kolekce členů (zdrojová adresa IP, cílová IP adresa a typ protokolu). Pro výchozí chování při vyrovnávání zatížení s pěti řazenými kolekcemi členů zadejte None.

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

Pokud `LoadBalancerDistribution` prvek není k dispozici, Azure Load Balancer používá výchozí algoritmus pět-Tuple.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Konfigurace distribučního režimu na sady koncových bodů s vyrovnáváním zatížení

Když koncové body jsou součástí sady koncových bodů s vyrovnáváním zatížení, režim distribuce musí být nakonfigurovaná na sady koncových bodů s vyrovnáváním zatížení:

```azurepowershell-interactive
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

Chcete-li změnit existující konfiguraci nasazení použijte model nasazení Azure classic. Přidejte hlavičku `x-ms-version` a nastavte hodnotu na verze 2014-09-01 nebo novější.

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

Jak je popsáno výše, nastavte prvek `LoadBalancerDistribution` tak, aby sourceIP pro spřažení dvou řazených kolekcí členů, sourceIPProtocol pro spřažení se třemi řazenými kolekcemi členů, nebo žádné bez spřažení (pět vztahů mezi řazenými kolekcemi členů).

#### <a name="response"></a>Odpověď

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Další kroky

* [Přehled interních Load Balancer Azure](load-balancer-internal-overview.md)
* [Začínáme s konfigurací internetového nástroje pro vyrovnávání zatížení](load-balancer-get-started-internet-arm-ps.md)
* [Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)
