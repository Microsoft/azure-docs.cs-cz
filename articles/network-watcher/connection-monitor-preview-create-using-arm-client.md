---
title: Vytvoření náhledu monitorování připojení – ARMClient
titleSuffix: Azure Network Watcher
description: Naučte se vytvářet monitorování připojení (Preview) pomocí ARMClient.
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
ms.openlocfilehash: 7d35799cd73ff4d065cb58189f2325dc4dac6840
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87567896"
---
# <a name="create-a-connection-monitor-preview-using-the-armclient"></a>Vytvoření monitorování připojení (Preview) pomocí ARMClient

Naučte se vytvořit monitorování připojení (Preview) a monitorovat komunikaci mezi prostředky pomocí nástroje ARMClient. Podporuje hybridní nasazení cloudu a Azure.

## <a name="before-you-begin"></a>Než začnete 

Ve sledováních připojení, které vytvoříte v monitorování připojení (Preview), můžete přidat místní počítače a virtuální počítače Azure jako zdroje. Tato sledování připojení také mohou monitorovat připojení k koncovým bodům. Koncové body můžou být na Azure nebo na jakékoli jiné adrese URL nebo IP adrese.

Monitorování připojení (Preview) obsahuje následující entity:

* **Prostředek sledování připojení** – prostředek Azure specifický pro oblast. Všechny následující entity jsou vlastnosti prostředku monitorování připojení.
* **Koncový bod** – zdroj nebo cíl, který se účastní kontrol připojení. Mezi příklady koncových bodů patří virtuální počítače Azure, místní agenti, adresy URL a IP adresy.
* **Konfigurace testu** – konfigurace specifická pro protokol pro test. V závislosti na zvoleném protokolu můžete definovat port, prahové hodnoty, frekvenci testování a další parametry.
* **Testovací skupina** – skupina, která obsahuje zdrojové koncové body, cílové koncové body a konfigurace testu. Monitorování připojení může obsahovat více než jednu skupinu testů.
* **Test** – kombinace zdrojového koncového bodu, cílového koncového bodu a konfigurace testu. Test je nejpřesnější úroveň, při které jsou dostupná data monitorování. Data monitorování zahrnují procento kontrol, které selhaly, a čas odezvy (RTT).

    ![Diagram znázorňující monitorování připojení, definování vztahu mezi testovacími skupinami a testy](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-sample-arm-template"></a>Postup vytvoření s ukázkovou šablonou ARM

Pomocí následujícího kódu vytvořte monitorování připojení pomocí ARMClient.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '<FQDN of your on-premises agent>'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

 {

name: 'vm2',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

   },

{

name: 'azure portal'

address: '<URL>'

   },

 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

Tady je příkaz nasazení:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

## <a name="description-of-properties"></a>Popis vlastností

* connectionMonitorName – název prostředku monitorování připojení

* ID DÍLČÍho předplatného, kde chcete vytvořit monitorování připojení

* NW-Network Watcher ID prostředku, ve kterém bude vytvořeno CM 

* umístění – oblast, ve které se bude monitor připojení vytvořit

* Koncové body
    * název – jedinečný název pro každý koncový bod
    * resourceId – pro koncové body Azure ID prostředku odkazuje na ID prostředku Azure Resource Manager (ARM) pro virtuální počítače. U koncových bodů mimo Azure odkazuje ID prostředku na ID prostředku ARM pro Log Analytics pracovní prostor propojený s agenty mimo Azure.
    * Adresa – platí pouze v případě, že buď není zadáno ID prostředku, nebo pokud je ID prostředku Log Analytics pracovního prostoru. Pokud se používá s ID prostředku Log Analytics, odkazuje se na plně kvalifikovaný název domény agenta, který se dá použít k monitorování. Pokud se používá bez ID prostředku, může to být adresa URL nebo IP adresa libovolného veřejného koncového bodu.
    * filtr – pro koncové body mimo Azure použijte filtr k výběru agentů z Log Analytics pracovního prostoru, který se použije pro monitorování v prostředku monitorování připojení. Pokud nejsou filtry nastavené, můžete k monitorování použít všechny agenty patřící do Log Analyticsho pracovního prostoru.
        * typ – nastavte typ jako "adresa agenta".
        * Adresa – nastaví adresu jako plně kvalifikovaný název domény místního agenta.

* Testovací skupiny
    * Název – pojmenujte testovací skupinu.
    * testConfigurations-test konfigurace na základě toho, které zdrojové koncové body se připojují k cílovým koncovým bodům
    * zdroje – vyberte z koncových bodů vytvořených výše. Zdrojové koncové body založené na Azure musí mít nainstalované rozšíření Azure Network Watcher a zdrojové koncové body založené na Azure musí být nainstalované haveAzure Log Analytics agent. Chcete-li nainstalovat agenta pro zdroj, přečtěte si téma [Instalace agentů monitorování](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents).
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
* Maximální počet konfigurací testu na monitorování připojení: 20 přes ARMClient

## <a name="next-steps"></a>Další kroky

* Naučte [se analyzovat data monitorování a nastavovat upozornění](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts) .
* Zjistěte [, jak diagnostikovat problémy ve vaší síti](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network) .
