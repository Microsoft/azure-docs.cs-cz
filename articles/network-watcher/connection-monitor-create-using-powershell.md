---
title: Vytvoření monitorování připojení – PowerShell
titleSuffix: Azure Network Watcher
description: Naučte se vytvořit monitorování připojení pomocí PowerShellu.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 7f175d82b650871437a506ea4513f0ae28360f68
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833027"
---
# <a name="create-a-connection-monitor-by-using-powershell"></a>Vytvoření monitorování připojení pomocí PowerShellu

> [!IMPORTANT]
> Od 1. července 2021 nebudete moci přidat nové testy do existujícího pracovního prostoru nebo povolit nový pracovní prostor v Network Performance Monitor. V monitorování připojení (Classic) taky nebude možné přidat nová monitorování připojení. Můžete nadále používat testy a monitory připojení vytvořené před 1. července 2021. Pokud chcete minimalizovat přerušení služby na vaše aktuální úlohy, [migrujte testy z Network Performance Monitor ](migrate-to-connection-monitor-from-network-performance-monitor.md) nebo  [migrujte z monitoru připojení (Classic)](migrate-to-connection-monitor-from-connection-monitor-classic.md) na nové monitorování připojení v Azure Network Watcher před 29. února 2024.


Naučte se používat funkci monitorování připojení služby Azure Network Watcher k monitorování komunikace mezi prostředky.


## <a name="before-you-begin"></a>Než začnete

V okně Sledování připojení, které vytvoříte pomocí monitorování připojení, můžete přidat místní počítače a virtuální počítače Azure jako zdroje. Tato sledování připojení také mohou monitorovat připojení k koncovým bodům. Koncové body můžou být na Azure nebo na jakékoli jiné adrese URL nebo IP adrese.

Monitorování připojení zahrnuje následující entity:

* **Prostředek monitorování připojení**: prostředek Azure specifický pro oblast. Všechny následující entity jsou vlastnosti prostředku monitorování připojení.
* **Koncový bod**: zdroj nebo cíl, který se účastní kontrol připojení. Mezi příklady koncových bodů patří virtuální počítače Azure, místní agenti, adresy URL a IP adresy.
* **Konfigurace testu**: konfigurace specifická pro protokol pro test. V závislosti na zvoleném protokolu můžete definovat port, prahové hodnoty, frekvenci testování a další parametry.
* **Testovací skupina**: skupina, která obsahuje zdrojové koncové body, cílové koncové body a konfigurace testu. Monitorování připojení může obsahovat více než jednu skupinu testů.
* **Test**: kombinace zdrojového koncového bodu, cílového koncového bodu a konfigurace testu. Test je nejpřesnější úroveň, při které jsou dostupná data monitorování. Data monitorování zahrnují procento kontrol, které selhaly, a čas odezvy (RTT).

    ![Diagram znázorňující monitorování připojení a definování vztahu mezi testovacími skupinami a testy.](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-a-connection-monitor"></a>Postup vytvoření monitorování připojení

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

* **ConnectionMonitorName**: název prostředku monitorování připojení.

* **Sub**: ID předplatného pro předplatné, ve kterém chcete vytvořit monitorování připojení.

* **NW**: Network Watcher ID prostředku, ve kterém se vytvoří monitor připojení.

* **Umístění**: oblast, ve které se vytvoří monitor připojení.

* **Koncové body**
    * **Název**: jedinečný název pro každý koncový bod.
    * **ID prostředku**: pro koncové body Azure odkazuje ID prostředku na id prostředku Azure Resource Manager pro virtuální počítače. U koncových bodů mimo Azure odkazuje ID prostředku na ID prostředku Azure Resource Manager pro pracovní prostor Log Analytics propojený s agenty mimo Azure.
    * **Adresa**: platí pouze v případě, že není zadáno ID prostředku nebo pokud je ID prostředku v pracovním prostoru Log Analytics. Pokud se používá bez ID prostředku, může to být adresa URL nebo IP adresa libovolného veřejného koncového bodu. Pokud se používá s ID prostředku Log Analytics, vztahuje se to na plně kvalifikovaný název domény agenta monitorování.
    * **Filtr**: u koncových bodů mimo Azure použijte filtry k výběru agentů monitorování z pracovního prostoru Log Analytics v prostředku monitorování připojení. Pokud nejsou filtry nastavené, můžete k monitorování použít všechny agenty patřící do Log Analytics pracovního prostoru.
        * **Typ**: nastavte **adresu agenta**.
        * **Adresa**: nastaví se jako plně kvalifikovaný název domény vašeho místního agenta.

* **Testovací skupiny**
    * **Název**: pojmenujte testovací skupinu.
    * **Zdroje**: vyberte z koncových bodů, které jste vytvořili dříve. Zdrojové koncové body založené na Azure musí mít nainstalované rozšíření Azure Network Watcher; zdrojové koncové body, které nejsou založené na Azure, musí mít nainstalovaného agenta Azure Log Analytics. Chcete-li nainstalovat agenta pro zdroj, přečtěte si téma [Instalace agentů monitorování](./connection-monitor-overview.md#install-monitoring-agents).
    * **Cílová umístění**: vyberte z koncových bodů, které jste vytvořili dříve. Můžete monitorovat připojení k virtuálním počítačům Azure nebo libovolnému koncovému bodu (veřejné IP adresy, adresy URL nebo plně kvalifikovanému názvu domény) jejich zadáním jako cíle. V jedné skupině testů můžete přidat virtuální počítače Azure, adresy URL pro Office 365, adresy URL Dynamics 365 a vlastní koncové body.
    * **Zakázat**: zakáže monitorování pro všechny zdroje a cíle, které určuje testovací skupina.

* **Konfigurace testů**
    * **Název**: pojmenujte konfiguraci testu.
    * **TestFrequencySec**: Určete, jak často se mají ve zdrojích odeslat příkazy pro zadání adresy pro odeslání na protokol a port. Můžete zvolit 30 sekund, 1 minutu, 5 minut, 15 minut nebo 30 minut. Zdroje otestují připojení k cílům na základě hodnoty, kterou zvolíte. Pokud například vyberete 30 sekund, zdroje kontrolují připojení k cílovému umístění nejméně jednou za 30 sekund.
    * **Protokol**: Vyberte TCP, ICMP, http nebo HTTPS. V závislosti na protokolu můžete také vybrat následující konfigurace specifické pro protokol:
        * **preferHTTPS**: Určete, jestli se má používat HTTPS přes HTTP.
        * **port**: Zadejte cílový port, který chcete vybrat.
        * **disableTraceRoute**: Zastavte zdroje z vyhledávání topologie a času směrování po směrování. To platí pro testovací skupiny pomocí TCP nebo ICMP.
        * **Metoda**: Vyberte metodu požadavku HTTP (Get nebo post). To platí pro testovací konfigurace pomocí protokolu HTTP.
        * **cesta**: zadejte parametry cesty, které se mají připojit k adrese URL.
        * **validStatusCodes**: vyberte příslušné stavové kódy. Pokud se kód odpovědi neshoduje, zobrazí se diagnostická zpráva.
        * **requestHeaders hostitele**: zadejte vlastní řetězce hlaviček žádostí, které přecházejí do cíle.
    * **Prahová hodnota úspěšnosti**: Nastavte prahové hodnoty u následujících síťových parametrů:
        * **checksFailedPercent**: nastavte procento kontrol, které mohou selhat, když zdroje kontrolují připojení k cílům pomocí zadaných kritérií. V případě protokolu TCP nebo ICMP se procento neúspěšných kontrol může rovnat procentu ztráty paketů. V případě protokolu HTTP toto pole představuje procento požadavků HTTP, které neobdržely odpověď.
        * **roundTripTimeMs**: Nastavte, jak dlouho můžou zdroje trvat pro připojení k cíli v konfiguraci testu v milisekundách.

## <a name="scale-limits"></a>Omezení škálování

Monitory připojení mají následující meze škálování:

* Maximální počet monitorování připojení na předplatné na oblast: 100
* Maximální počet testovacích skupin na monitorování připojení: 20
* Maximální počet zdrojů a cílů na monitorování připojení: 100
* Maximální počet konfigurací testu na monitorování připojení: 20

## <a name="next-steps"></a>Další kroky

* Naučte [se analyzovat data monitorování a nastavovat upozornění](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts).
* Naučte [se diagnostikovat problémy ve vaší síti](./connection-monitor-overview.md#diagnose-issues-in-your-network).
