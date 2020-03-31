---
title: Monitorování operací, událostí a čítačů pro veřejný nástroj pro vyrovnávání zatížení
titleSuffix: Azure Load Balancer
description: Zjistěte, jak povolit události výstrah a protokolování stavu sondy pro veřejný základní nástroj pro vyrovnávání zatížení
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: allensu
ms.openlocfilehash: 0a21af683d9fa7849d3e96c545983c9f40a8d4c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935321"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Protokoly služby Azure Monitor pro veřejný Load Balancer úrovně Basic

>[!IMPORTANT]
>Azure Load Balancer podporuje dva různé typy: Basic a Standard. Tento článek popisuje Load Balancer úrovně Basic. Další informace o standardní masce vyrovnávání zatížení najdete v [tématu Standardní přehled nástroje pro vyrovnávání zatížení,](load-balancer-standard-overview.md) který zpřístupňuje telemetrii prostřednictvím vícerozměrných metrik v Azure Monitoru.

Můžete použít různé typy protokolů v Azure ke správě a řešení potíží základní vyrovnávání zatížení. Některé z těchto protokolů lze přistupovat prostřednictvím portálu. Protokoly lze streamovat do centra událostí nebo do pracovního prostoru Analýzy protokolů. Všechny protokoly lze extrahovat z úložiště objektů blob Azure a zobrazit v různých nástrojích, jako je Excel a Power BI.  Další informace o různých typech protokolů naleznete v seznamu níže.

* **Protokoly aktivit:** Pomocí [protokolů aktivit zobrazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) můžete sledovat akce na prostředky k zobrazení všech aktivit odeslaných do předplatného Azure a jejich stavu. Protokoly aktivit jsou ve výchozím nastavení povolené a lze je zobrazit na webu Azure Portal.
* **Protokoly událostí výstrah:** Tento protokol můžete použít k zobrazení výstrah y vyvolaná vykladačem zatížení. Stav nástroj pro vyrovnávání zatížení se shromažďuje každých pět minut. Tento protokol je zapsán pouze v případě, že je vyvolána událost výstrahy vyrovnávání zatížení.
* **Protokoly sondy stavu:** Tento protokol můžete použít k zobrazení problémů zjištěných sondou stavu, jako je například počet instancí v back-endovém fondu, které nepřijímají požadavky z doby provyrovnávání zatížení z důvodu selhání sondy stavu. Tento protokol je zapsán, pokud dojde ke změně stavu sondy stavu.

> [!IMPORTANT]
> Protokoly Azure Monitor aktuálně funguje pouze pro veřejné základní vyrovnávání zatížení. Protokoly jsou k dispozici pouze pro prostředky nasazené v modelu nasazení Správce prostředků. Protokoly nelze použít pro prostředky v modelu klasického nasazení. Další informace o modelech nasazení naleznete v [tématu Principy nasazení správce prostředků a klasického nasazení](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Povolit protokolování

Protokolování aktivit je u každého prostředku Správce prostředků povolené automaticky. Povolit protokolování sondy událostí a stavu a začít shromažďovat data dostupná prostřednictvím těchto protokolů. Protokolování povolte následujícími kroky.

Přihlaste se k [portálu Azure](https://portal.azure.com). Pokud ještě nemáte vytáčítek zatížení, vytvořte před [pokračováním vyvyřič zatížení.](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal)

1. Na portálu klikněte na **Položky Skupiny prostředků**.
2. Vyberte název ** \<skupiny prostředků>** kde je váš balancer.
3. Vyberte svůj balancer.
4. Vyberte **možnost Sledování** > **nastavení diagnostiky**.
5. V podokně **Nastavení diagnostiky** vyberte v části **Nastavení diagnostiky** **možnost + Přidat diagnostické nastavení**.
6. V podokně vytvoření **nastavení diagnostiky** zadejte **myLBDiagnostics** do pole **Název.**
7. Máte tři možnosti pro **nastavení diagnostiky**.  Můžete si vybrat jednu, dvě nebo všechny tři a nakonfigurovat každý pro vaše požadavky:
   * **Archivovat do účtu úložiště**
   * **Streamování do centra událostí**
   * **Odeslání do Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Archivovat v účtu úložiště
    Budete potřebovat účet úložiště, který už byl pro tento proces vytvořen.  Pokud chcete vytvořit účet úložiště, [přečtěte si tématika Vytvoření účtu úložiště.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. Zaškrtněte políčko vedle **položky Archivovat účet úložiště**.
    2. Vyberte **Konfigurovat,** **chcete-li** otevřít podokno Vybrat účet úložiště.
    3. V rozbalovacím poli vyberte **předplatné,** ve kterém byl váš účet úložiště vytvořen.
    4. V rozbalovacím poli vyberte název svého účtu úložiště v části **Účet úložiště.**
    5. Vyberte OK.

    ### <a name="stream-to-an-event-hub"></a>Streamovat do centra událostí
    Budete potřebovat centrum událostí, které již bylo pro tento proces vytvořeno.  Pokud chcete vytvořit centrum událostí, přečtěte si [úvodní příručku: Vytvoření centra událostí pomocí portálu Azure Portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

    1. Zaškrtněte políčko vedle **streamu do centra událostí.**
    2. Vyberte **Konfigurovat,** chcete-li otevřít podokno **Select event hub.**
    3. V rozbalovacím poli vyberte **Předplatné,** ve kterém bylo centrum událostí vytvořeno.
    4. V rozbalovacím poli **vyberte obor názvů centra událostí.**
    5. V rozbalovacím poli **vyberte název zásad centra událostí.**
    6. Vyberte OK.

    ### <a name="send-to-log-analytics"></a>Odeslání do Log Analytics
    Pro tento proces už budete muset mít vytvořený a nakonfigurovaný pracovní prostor analýzy protokolů.  Pokud chcete vytvořit pracovní prostor Analýzy protokolů, přečtěte si témat [u tématu Vytvoření pracovního prostoru Analýzy protokolů na webu Azure Portal.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

    1. Zaškrtněte políčko vedle **možnosti Odeslat do analýzy protokolů**.
    2. Vrozbalněte **předplatné,** ve kterém se v rozbalovacím poli nachází pracovní prostor Analýzy protokolů.
    3. V rozbalovacím poli vyberte **pracovní prostor Analýzy protokolů.**


8. Pod oddílem **LOG** v podokně **Nastavení diagnostiky** zaškrtněte políčko vedle obou:
   * **Událost LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Pod částí **METRIKA** v podokně **Nastavení diagnostiky** zaškrtněte políčko vedle:
   * **AllMetrics**

11. Ověřte, zda vše vypadá správně, a v horní části podokna Vytvořit **nastavení diagnostiky** klikněte na **Uložit.**

## <a name="activity-log"></a>Protokol aktivit

Protokol aktivit je ve výchozím nastavení generován. Protokoly se uchovávají po dobu 90 dnů v úložišti protokolů událostí Azure. Další informace o těchto protokolech najdete [v zobrazení protokolů aktivit sledovat akce na zdroje](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) článku.

## <a name="archive-to-storage-account-logs"></a>Archivovat do protokolů účtů úložiště

### <a name="alert-event-log"></a>Protokol událostí výstrahy

Tento protokol je generován pouze v případě, že jste povolili na základě provynace zatížení. Události jsou zaznamenány ve formátu JSON a uloženy v účtu úložiště, který jste zadali při povolení protokolování. Následující příklad je události.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

Výstup JSON zobrazuje vlastnost *eventname,* která bude popisovat důvod pro vyrovnávání zatížení vytvořil výstrahu. V tomto případě byla vygenerována výstraha z důvodu vyčerpání portu TCP způsobeného zdrojovými limity NAT IP (SNAT).

### <a name="health-probe-log"></a>Protokol sondy stavu

Tento protokol je generován pouze v případě, že jste povolili na základě provyrovnávání zatížení, jak je podrobně popsáno výše. Data jsou uložena v účtu úložiště, který jste zadali při povolení protokolování. Je vytvořen kontejner s názvem Insights-logs-loadbalanceprobehealthstatus a jsou zaznamenána následující data:

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

Výstup JSON zobrazuje v poli vlastností základní informace o stavu sondy. Vlastnost *dipDownCount* zobrazuje celkový počet instancí v back-endu, které nepřijímají síťový provoz z důvodu neúspěšných odpovědí sondy.

### <a name="view-and-analyze-the-activity-log"></a>Zobrazení a analýza protokolu aktivit

Data protokolu aktivit můžete zobrazit a analyzovat pomocí některé z následujících metod:

* **Nástroje Azure:** Načtěte informace z protokolu aktivit prostřednictvím Azure PowerShellu, rozhraní Api (Azure Command Line Interface), rozhraní AZURE REST API nebo portálu Azure. Podrobné pokyny pro každou metodu jsou podrobně popsány v [článku Audit operace s Správcem prostředků.](../azure-resource-manager/management/view-activity-logs.md)
* **Power BI:** Pokud ještě nemáte účet [Power BI,](https:// .microsoft.com/pricing) můžete si ho vyzkoušet zdarma. Pomocí [balíčku obsahu Protokoly auditu Azure pro Power BI](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)můžete analyzovat data pomocí předem nakonfigurovaných řídicích panelů nebo si můžete přizpůsobit zobrazení tak, aby vyhovovala vašim požadavkům.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Zobrazení a analýza sondy stavu a protokolu událostí

Připojte se k účtu úložiště a načtěte položky protokolu JSON pro protokoly sondy událostí a stavu. Po stažení souborů JSON je můžete převést na CSV a zobrazit je v Excelu, Power BI nebo v jakémkoli jiném nástroji pro vizualizaci dat.

> [!TIP]
> Pokud znáte Visual Studio a máte představu, jak u konstant a proměnných v jazyce C# měnit hodnoty, můžete použít [nástroje pro převedení protokolů](https://github.com/Azure-Samples/networking-dotnet-log-converter), které jsou k dispozici na GitHubu.

## <a name="stream-to-an-event-hub"></a>Streamovat do centra událostí
Když se diagnostické informace streamují do centra událostí, lze je použít pro centralizovanou analýzu protokolů v nástroji SIEM jiného výrobce s integrací Azure Monitor. Další informace najdete v [tématu Stream Azure data monitorování do centra událostí](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Odeslání do Log Analytics
Prostředky v Azure můžou mít svoje diagnostické informace odesílané přímo do pracovního prostoru Analýzy protokolů, kde se dají spustit složité dotazy s informacemi pro řešení potíží a analýzu.  Další informace najdete [v tématu Shromažďování protokolů prostředků Azure v pracovním prostoru Log Analytics ve službě Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)

## <a name="next-steps"></a>Další kroky

[Porozumění testům nástroje pro vyrovnávání zatížení](load-balancer-custom-probe-overview.md)
