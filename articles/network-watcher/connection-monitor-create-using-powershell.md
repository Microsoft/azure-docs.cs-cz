---
title: Vytvoření monitorování připojení – PowerShell
titleSuffix: Azure Network Watcher
description: Naučte se vytvářet monitorování připojení pomocí PowerShellu.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: fa8b2d967a336343d23c5f6aa4477ebcf2396407
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949033"
---
# <a name="create-a-connection-monitor-using-powershell"></a>Vytvoření monitorování připojení pomocí PowerShellu

Naučte se vytvářet monitorování připojení, abyste mohli monitorovat komunikaci mezi prostředky pomocí PowerShellu.

## <a name="before-you-begin"></a>Než začnete 

V okně Sledování připojení, které vytvoříte v monitorování připojení, můžete přidat místní počítače a virtuální počítače Azure jako zdroje. Tato sledování připojení také mohou monitorovat připojení k koncovým bodům. Koncové body můžou být na Azure nebo na jakékoli jiné adrese URL nebo IP adrese.

Monitorování připojení zahrnuje následující entity:

* **Prostředek sledování připojení** – prostředek Azure specifický pro oblast. Všechny následující entity jsou vlastnosti prostředku monitorování připojení.
* **Koncový bod** – zdroj nebo cíl, který se účastní kontrol připojení. Mezi příklady koncových bodů patří virtuální počítače Azure, místní agenti, adresy URL a IP adresy.
* **Konfigurace testu** – konfigurace specifická pro protokol pro test. V závislosti na zvoleném protokolu můžete definovat port, prahové hodnoty, frekvenci testování a další parametry.
* **Testovací skupina** – skupina, která obsahuje zdrojové koncové body, cílové koncové body a konfigurace testu. Monitorování připojení může obsahovat více než jednu skupinu testů.
* **Test** – kombinace zdrojového koncového bodu, cílového koncového bodu a konfigurace testu. Test je nejpřesnější úroveň, při které jsou dostupná data monitorování. Data monitorování zahrnují procento kontrol, které selhaly, a čas odezvy (RTT).

    ![Diagram znázorňující monitorování připojení, definování vztahu mezi testovacími skupinami a testy](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-powershell"></a>Postup vytvoření pomocí prostředí PowerShell

Pomocí následujících příkazů vytvořte monitorování připojení pomocí prostředí PowerShell.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>Popis vlastností

* connectionMonitorName – název prostředku monitorování připojení

* ID DÍLČÍho předplatného, kde chcete vytvořit monitorování připojení

* NW-Network Watcher ID prostředku, ve kterém bude vytvořeno CM 

* umístění – oblast, ve které se bude monitor připojení vytvořit

* Koncové body
    * název – jedinečný název pro každý koncový bod
    * resourceId – pro koncové body Azure ID prostředku odkazuje na ID prostředku Azure Resource Manager pro virtuální počítače. U koncových bodů mimo Azure odkazuje ID prostředku na ID prostředku Azure Resource Manageru pro Log Analytics pracovní prostor, který je propojený s agenty mimo Azure.
    * Adresa – platí pouze v případě, že buď není zadáno ID prostředku, nebo pokud je ID prostředku Log Analytics pracovního prostoru. Pokud se používá s ID prostředku Log Analytics, odkazuje se na plně kvalifikovaný název domény agenta, který se dá použít k monitorování. Pokud se používá bez ID prostředku, může to být adresa URL nebo IP adresa libovolného veřejného koncového bodu.
    * filtr – pro koncové body mimo Azure použijte filtr k výběru agentů z Log Analytics pracovního prostoru, který se použije pro monitorování v prostředku monitorování připojení. Pokud nejsou filtry nastavené, můžete k monitorování použít všechny agenty patřící do Log Analyticsho pracovního prostoru.
        * typ – nastavte typ jako "adresa agenta".
        * Adresa – nastaví adresu jako plně kvalifikovaný název domény místního agenta.

* Testovací skupiny
    * Název – pojmenujte testovací skupinu.
    * testConfigurations-test konfigurace na základě toho, které zdrojové koncové body se připojují k cílovým koncovým bodům
    * zdroje – vyberte z koncových bodů vytvořených výše. Zdrojové koncové body založené na Azure musí mít nainstalované rozšíření Azure Network Watcher a zdrojové koncové body založené na Azure musí být nainstalované haveAzure Log Analytics agent. Chcete-li nainstalovat agenta pro zdroj, přečtěte si téma [Instalace agentů monitorování](./connection-monitor-overview.md#install-monitoring-agents).
    * cíle – vybrat z koncových bodů vytvořených výše. Můžete monitorovat připojení k virtuálním počítačům Azure nebo libovolnému koncovému bodu (veřejné IP adresy, adresy URL nebo plně kvalifikovanému názvu domény) jejich zadáním jako cíle. V jedné skupině testů můžete přidat virtuální počítače Azure, adresy URL pro Office 365, adresy URL Dynamics 365 a vlastní koncové body.
    * zakázat – pomocí tohoto pole zakážete monitorování pro všechny zdroje a cíle, které určuje skupina testů.

* Konfigurace testů
    * Název – název konfigurace testu.
    * testFrequencySec – určete, jak často budou zdroje testovat cíle nástroje pro otestování na zadaném protokolu a portu. Můžete zvolit 30 sekund, 1 minutu, 5 minut, 15 minut nebo 30 minut. Zdroje budou testovat připojení k cílům na základě hodnoty, kterou zvolíte. Pokud například vyberete 30 sekund, zdroje budou kontrolovat připojení k cíli nejméně jednou za 30 sekund.
    * protokol – můžete zvolit TCP, ICMP, HTTP nebo HTTPS. V závislosti na protokolu můžete provést několik konfigurací specifických pro protokol.
        * preferHTTPS – určuje, jestli se má použít HTTPS přes HTTP
        * Port – zadejte cílový port, který chcete vybrat.
        * disableTraceRoute – to platí pro testovací skupiny, jejichž protokol je TCP nebo ICMP. Zastavuje zdroje z vyhledávání topologie a času směrování po směrování.
    * successThreshold – prahové hodnoty můžete nastavit u následujících síťových parametrů:
        * checksFailedPercent – nastaví procento kontrol, které mohou selhat, když zdroje kontrolují připojení k cílům pomocí zadaných kritérií. V případě protokolu TCP nebo ICMP může být Procento neúspěšných kontrol rovno procentům ztráty paketů. V případě protokolu HTTP toto pole představuje procento požadavků HTTP, které neobdržely odpověď.
        * roundTripTimeMs – doba odezvy v milisekundách, po kterou mohou dlouhé zdroje trvat pro připojení k cíli prostřednictvím konfigurace testu.

## <a name="scale-limits"></a>Omezení škálování

Monitory připojení mají následující meze škálování:

* Maximální počet monitorování připojení na předplatné na oblast: 100
* Maximální počet testovacích skupin na monitorování připojení: 20
* Maximální počet zdrojů a cílů na monitorování připojení: 100
* Maximální počet konfigurací testu na monitorování připojení: 20

## <a name="next-steps"></a>Další kroky

* Naučte [se analyzovat data monitorování a nastavovat upozornění](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts) .
* Zjistěte [, jak diagnostikovat problémy ve vaší síti](./connection-monitor-overview.md#diagnose-issues-in-your-network) .