---
title: Vytvoření monitorování připojení – šablona ARM
titleSuffix: Azure Network Watcher
description: Naučte se vytvářet monitorování připojení pomocí nástroje ARMClient.
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
ms.openlocfilehash: 46bdaf932d4224bf97b46e7713d49d815ca1bcdd
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832993"
---
# <a name="create-a-connection-monitor-using-the-arm-template"></a>Vytvoření monitorování připojení pomocí šablony ARM

> [!IMPORTANT]
> Od 1. července 2021 nebudete moci přidat nové testy do existujícího pracovního prostoru nebo povolit nový pracovní prostor v Network Performance Monitor. V monitorování připojení (Classic) taky nebude možné přidat nová monitorování připojení. Můžete nadále používat testy a monitory připojení vytvořené před 1. července 2021. Pokud chcete minimalizovat přerušení služby na vaše aktuální úlohy, [migrujte testy z Network Performance Monitor ](migrate-to-connection-monitor-from-network-performance-monitor.md) nebo  [migrujte z monitoru připojení (Classic)](migrate-to-connection-monitor-from-connection-monitor-classic.md) na nové monitorování připojení v Azure Network Watcher před 29. února 2024.

Naučte se vytvářet monitorování připojení, abyste mohli monitorovat komunikaci mezi prostředky pomocí nástroje ARMClient. Podporuje hybridní nasazení cloudu a Azure.


## <a name="before-you-begin"></a>Než začnete 

V okně Sledování připojení, které vytvoříte v monitorování připojení, můžete přidat místní počítače a virtuální počítače Azure jako zdroje. Tato sledování připojení také mohou monitorovat připojení k koncovým bodům. Koncové body můžou být na Azure nebo na jakékoli jiné adrese URL nebo IP adrese.

Monitorování připojení zahrnuje následující entity:

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

name: 'endpoint_workspace_machine',

type: 'MMAWorkspaceMachine',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

//Example 1: Choose a machine

address : '<non-Azure machine FQDN>'
}

//Example 2: Select IP from chosen machines

address : '<non-Azure machine FQDN>

"scope": {
      "include": [
            {
                  "address": "<IP belonging to machine chosen above>"  
        }
       ]
      }
   }    
   
name: 'endpoint_workspace_network',

type: 'MMAWorkspaceNetwork',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

 coverage level : 'high', //Optional
 
 //Include subnets. You can also exclude IPs from subnet to exclude from monitoring
 
 scope: {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28
            },
            {
                  "address": "<subnet 2 mask>" 
            }
      ],
      "exclude": [
            { 
            "address" : "<ip-from-included-subnets-that-should-be-excluded>"
        }
      ]
     }
},

//Use a Azure VM as an endpoint
{

name: 'endpoint_virtualmachine',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

//Use an Azure VNET or Subnet as an endpoint

 {

name: 'endpoint_vnet_subnet',

resourceId: '<resource id of VNET or subnet'
coverage level: 'high' //Optional

//Scope is optional.

  "scope": {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28 .This subnet should match with any existing subnet in vnet
            }
      ],
    "exclude": [
            {
                  "address": "<ip-from-included-subnets-that-should-be-excluded>" // If used with include, IP should be part of the subnet defined above. Without include, this could be any address within vnet range or any specific subnet range as a whole.
            }
      ]
  }
   },

//Endpoint as a URL
{

name: 'azure portal'

address: '<URL>'

   },

//Endpoint as an IP 
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
    
     port: '<port of choice>'
  
    preferHTTPS: true // If port chosen is not 80 or 443
    
    method: 'GET', //Choose GET or POST
    
    path: '/', //Specify path for request
         
    requestHeaders: [
            {
              "name": "Content-Type",
              "value": "appication/json"
            }
          ],
          
    validStatusCodeRanges: [ "102", "200-202", "3xx" ], //Samples
          
    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, 
   {

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
    * resourceId – pro koncové body Azure ID prostředku odkazuje na ID prostředku Azure Resource Manager pro virtuální počítače. U koncových bodů mimo Azure odkazuje ID prostředku na ID prostředku Azure Resource Manager pro pracovní prostor Log Analytics propojený s agenty mimo Azure.
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
    
        * preferHTTPS – určuje, jestli se má používat protokol HTTPS přes protokol HTTP, pokud se používá port ani 80, ani 443.
        * Port – zadejte cílový port, který chcete vybrat.
        * disableTraceRoute – to platí pro testovací konfigurace, jejichž protokol je TCP nebo ICMP. Zastavuje zdroje z vyhledávání topologie a času směrování po směrování.
        * Metoda – používá se pro testovací konfigurace, jejichž protokol je HTTP. Vyberte metodu žádosti HTTP – buď GET, nebo POST.
        * cesta – zadejte parametry cesty, které se mají připojit k adrese URL.
        * validStatusCodes – vyberte příslušné stavové kódy. Pokud kód odpovědi neodpovídá tomuto seznamu, zobrazí se diagnostická zpráva.
        * requestHeaders hostitele – zadejte vlastní řetězce hlaviček žádostí, které se předají do cílového umístění.
        
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

* Naučte [se analyzovat data monitorování a nastavovat upozornění](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts) .
* Zjistěte [, jak diagnostikovat problémy ve vaší síti](./connection-monitor-overview.md#diagnose-issues-in-your-network) .
