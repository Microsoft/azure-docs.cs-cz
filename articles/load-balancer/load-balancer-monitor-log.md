---
title: Monitorování operací, událostí a čítačů pro veřejný základní Load Balancer
titleSuffix: Azure Load Balancer
description: Naučte se povolit události výstrah a protokolování stavu sondy pro veřejný základní Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: f24ab2c646757f0241748336243b0d5f977d081c
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698321"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Protokoly služby Azure Monitor pro veřejný Load Balancer úrovně Basic

Pomocí různých typů protokolů v Azure můžete spravovat a řešit základní nástroje pro vyrovnávání zatížení. K některým z těchto protokolů se dá dostat prostřednictvím portálu. Protokoly můžou být streamované do centra událostí nebo do pracovního prostoru Log Analytics. Všechny protokoly se dají extrahovat z úložiště objektů BLOB v Azure a zobrazovat v různých nástrojích, jako je Excel a Power BI.  Další informace o různých typech protokolů najdete v níže uvedeném seznamu.

* **Protokoly aktivit:** Pomocí [možnosti Zobrazit protokoly aktivit můžete monitorovat akce v prostředcích](../azure-resource-manager/management/view-activity-logs.md) a zobrazit všechny aktivity odeslané do vašich předplatných Azure a jejich stav. Protokoly aktivit jsou ve výchozím nastavení povolené a dají se zobrazit v Azure Portal.
* **Protokoly událostí výstrah:** Pomocí tohoto protokolu můžete zobrazit výstrahy vyvolané nástrojem pro vyrovnávání zatížení. Stav nástroje pro vyrovnávání zatížení se shromáždí každých pět minut. Tento protokol je zapsán pouze v případě, že je vyvolána událost upozornění nástroje pro vyrovnávání zatížení.
* **Protokoly sondy stavu:** Pomocí tohoto protokolu můžete zobrazit problémy zjištěné sondou stavu, jako je počet instancí ve fondu back-end, který nepřijímá požadavky z nástroje pro vyrovnávání zatížení z důvodu selhání sondy stavu. Do tohoto protokolu se zapisuje, když dojde ke změně stavu sondy stavu.

> [!IMPORTANT]
> **Protokoly událostí sondy stavu nejsou v současné době funkční a jsou uvedeny ve [známých problémech pro Azure Load Balancer](whats-new.md#known-issues).** Protokoly jsou k dispozici pouze pro prostředky nasazené v modelu nasazení Správce prostředků. Protokoly pro prostředky v modelu nasazení Classic nemůžete použít. Další informace o modelech nasazení najdete v tématu [Principy nasazení Správce prostředků a klasického nasazení](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Povolit protokolování

Protokolování aktivit je u každého prostředku Správce prostředků povolené automaticky. Povolte protokolování událostí a sondy stavu, abyste mohli začít shromažďovat data dostupná přes tyto protokoly. K povolení protokolování použijte následující postup.

Přihlaste se na [Azure Portal](https://portal.azure.com). Pokud ještě nemáte Nástroj pro vyrovnávání zatížení, vytvořte před pokračováním [Nástroj pro vyrovnávání zatížení](./quickstart-load-balancer-standard-public-portal.md) .

1. Na portálu klikněte na **skupiny prostředků**.
2. Vyberte **\<resource-group-name>** , kde je nástroj pro vyrovnávání zatížení.
3. Vyberte svůj Load Balancer.
4. Vyberte **Activity log**  >  **nastavení diagnostiky** protokolu aktivit.
5. V podokně **nastavení diagnostiky** v části **nastavení diagnostiky** vyberte **+ Přidat nastavení diagnostiky**.
6. V podokně vytvoření **nastavení diagnostiky** do pole **název** zadejte **myLBDiagnostics** .
7. Pro **nastavení diagnostiky** máte tři možnosti.  Můžete zvolit jednu, dvě nebo všechny tři a nakonfigurovat každou z vašich požadavků:
   * **Archivace do účtu úložiště**
   * **Streamování do centra událostí**
   * **Odeslání do Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Archivovat v účtu úložiště
    Budete potřebovat účet úložiště, který se pro tento proces už vytvořil.  Informace o vytvoření účtu úložiště najdete v tématu [Vytvoření účtu úložiště](../storage/common/storage-account-create.md?tabs=azure-portal) .

    1. Zaškrtněte políčko vedle **archivace na účet úložiště**.
    2. Vyberte **Konfigurovat** a otevřete tak podokno **Vybrat účet úložiště** .
    3. V rozevíracím seznamu vyberte **předplatné** , ve kterém se vytvořil váš účet úložiště.
    4. V části **účet úložiště** v rozevíracím seznamu vyberte název svého účtu úložiště.
    5. Vyberte OK.

    ### <a name="stream-to-an-event-hub"></a>Streamovat do centra událostí
    Pro tento proces budete potřebovat již vytvořenou centrum událostí.  Informace o vytvoření centra událostí najdete v tématu [rychlý Start: vytvoření centra událostí pomocí Azure Portal](../event-hubs/event-hubs-create.md)

    1. Zaškrtněte políčko vedle **streamu do centra událostí** .
    2. Vyberte **Konfigurovat** a otevřete tak podokno **Vybrat centrum událostí** .
    3. V rozevíracím seznamu vyberte **předplatné** , ve kterém se vaše centrum událostí vytvořilo.
    4. V rozevíracím seznamu **Vyberte obor názvů centra událostí** .
    5. V rozevíracím seznamu **Vyberte název zásady centra událostí** .
    6. Vyberte OK.

    ### <a name="send-to-log-analytics"></a>Odeslání do Log Analytics
    Pro tento proces budete potřebovat pracovní prostor Log Analytics vytvořený a nakonfigurovaný.  Pokud chcete vytvořit pracovní prostor Log Analytics, přečtěte si téma [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../azure-monitor/learn/quick-create-workspace.md)

    1. Zaškrtněte políčko vedle **odeslat Log Analytics**.
    2. V rozevíracím seznamu vyberte **předplatné** , ve kterém je váš pracovní prostor Log Analytics.
    3. V rozevíracím seznamu vyberte **pracovní prostor Log Analytics** .


8. Pod oddílem **protokol** v podokně **nastavení diagnostiky** zaškrtněte políčko vedle obou:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Pod oddílem **metrika** v podokně **nastavení diagnostiky** zaškrtněte políčko vedle:
   * **AllMetrics**

11. Ověřte, zda je vše v pořádku, a v horní části podokna vytvořit **nastavení diagnostiky** klikněte na **Uložit** .

## <a name="activity-log"></a>Protokol aktivit

Protokol aktivit je ve výchozím nastavení vygenerován. Protokoly se uchovávají po 90 dnech v úložišti protokolů událostí Azure. Další informace o těchto protokolech najdete v článku [zobrazení protokolů aktivit pro monitorování akcí v prostředcích](../azure-resource-manager/management/view-activity-logs.md) .

## <a name="archive-to-storage-account-logs"></a>Archivace do protokolů účtu úložiště

### <a name="alert-event-log"></a>Protokol událostí výstrah

Tento protokol se vygeneruje jenom v případě, že jste ho povolili na základě nástroje pro vyrovnávání zatížení. Události se zaznamenávají ve formátu JSON a ukládají se do účtu úložiště, který jste zadali při povolování protokolování. V následujícím příkladu je událost.

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

Výstup JSON zobrazuje vlastnost *EventName* , která popíše důvod, proč Nástroj pro vyrovnávání zatížení vytvořil výstrahu. V takovém případě se vygenerovala výstraha z důvodu vyčerpání portů TCP způsobených omezeními zdrojového překladu IP adres (SNAT).

### <a name="health-probe-log"></a>Protokol sondy stavu

Tento protokol se vygeneruje jenom v případě, že jste ho povolili na jednotlivých nástrojích pro vyrovnávání zatížení, jak je popsáno výše. Data se ukládají do účtu úložiště, který jste zadali při povolování protokolování. Vytvoří se kontejner s názvem Insights-logs-loadbalancerprobehealthstatus a zaprotokoluje se následující data:

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

Výstup JSON se zobrazí v poli vlastnosti základní informace o stavu sondy. Vlastnost *dipDownCount* zobrazuje celkový počet instancí na back-endu, které nepříjemují síťový provoz z důvodu nezdařených odpovědí na testy.

### <a name="view-and-analyze-the-activity-log"></a>Zobrazení a analýza protokolu aktivit

Data protokolu aktivit můžete zobrazit a analyzovat pomocí kterékoli z následujících metod:

* **Nástroje Azure:** Načtěte informace z protokolu aktivit prostřednictvím Azure PowerShell, rozhraní příkazového řádku Azure (CLI), Azure REST API nebo Azure Portal. Podrobné pokyny pro jednotlivé metody jsou podrobně popsané v článku [operace auditu s správce prostředků](../azure-resource-manager/management/view-activity-logs.md) .
* **Power BI:** Pokud ještě nemáte účet [Power BI](https:// .microsoft.com/pricing) , můžete si ho vyzkoušet zdarma. Pomocí [balíčku obsahu protokoly auditování Azure pro Power BI](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)můžete analyzovat data pomocí předem nakonfigurovaných řídicích panelů nebo můžete přizpůsobit zobrazení podle svých požadavků.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Zobrazit a analyzovat sondu stavu a protokol událostí

Připojte se k účtu úložiště a načtěte položky protokolu JSON pro protokoly událostí a stavu testu. Po stažení souborů JSON je můžete převést na CSV a zobrazit v Excelu, Power BI nebo jakémkoli jiném nástroji pro vizualizaci dat.

> [!TIP]
> Pokud znáte Visual Studio a máte představu, jak u konstant a proměnných v jazyce C# měnit hodnoty, můžete použít [nástroje pro převedení protokolů](https://github.com/Azure-Samples/networking-dotnet-log-converter), které jsou k dispozici na GitHubu.

## <a name="stream-to-an-event-hub"></a>Streamovat do centra událostí
Když se diagnostické informace streamují do centra událostí, dá se použít k centralizované analýze protokolů v nástroji SIEM třetí strany s integrací Azure Monitor. Další informace najdete v tématu [streamování dat monitorování Azure do centra událostí](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration) .

## <a name="send-to-log-analytics"></a>Odeslání do Log Analytics
Prostředky v Azure můžou mít své diagnostické informace odesílány přímo do Log Analytics pracovního prostoru, kde můžete provádět složité dotazy na informace pro řešení potíží a analýzy.  Další informace najdete v tématu [shromáždění protokolů prostředků Azure v pracovním prostoru Log Analytics v Azure monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)

## <a name="next-steps"></a>Další kroky

[Porozumění testům nástroje pro vyrovnávání zatížení](load-balancer-custom-probe-overview.md)