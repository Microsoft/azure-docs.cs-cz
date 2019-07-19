---
title: Monitorování operací, událostí a čítačů pro veřejný základní Load Balancer
titlesuffix: Azure Load Balancer
description: Naučte se povolit události výstrah a protokolování stavu sondy pro veřejný základní Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2018
ms.author: allensu
ms.openlocfilehash: 1995ad5e8179fdee11e960c2ad0e7c03602ebd31
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274802"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Protokoly Azure Monitor pro veřejný základní Load Balancer

>[!IMPORTANT] 
>Azure Load Balancer podporuje dva různé typy: Basic a Standard. Tento článek popisuje Load Balancer úrovně Basic. Další informace o Standard Load Balancer naleznete v tématu [Standard Load Balancer Overview](load-balancer-standard-overview.md) , který zpřístupňuje telemetrii prostřednictvím multidimenzionálních metrik v Azure monitor.

Pomocí různých typů protokolů v Azure můžete spravovat a řešit základní nástroje pro vyrovnávání zatížení. K některým z těchto protokolů se dá dostat prostřednictvím portálu. Všechny protokoly se dají extrahovat z úložiště objektů BLOB v Azure a zobrazovat v různých nástrojích, jako je Excel a PowerBI. Další informace o různých typech protokolů najdete v níže uvedeném seznamu.

* **Protokoly auditu:** K zobrazení všech operací odeslaných do vašich předplatných Azure a jejich stavu můžete použít [protokoly auditu Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (dřív označované jako operační protokoly). Protokoly auditu jsou ve výchozím nastavení povolené a dají se zobrazit v Azure Portal.
* **Protokoly událostí výstrah:** Pomocí tohoto protokolu můžete zobrazit výstrahy vyvolané nástrojem pro vyrovnávání zatížení. Stav nástroje pro vyrovnávání zatížení se shromáždí každých pět minut. Tento protokol je zapsán pouze v případě, že je vyvolána událost upozornění nástroje pro vyrovnávání zatížení.
* **Protokoly sondy stavu:** Pomocí tohoto protokolu můžete zobrazit problémy zjištěné sondou stavu, jako je počet instancí ve fondu back-end, který nepřijímá požadavky z nástroje pro vyrovnávání zatížení z důvodu selhání sondy stavu. Do tohoto protokolu se zapisuje, když dojde ke změně stavu sondy stavu.

> [!IMPORTANT]
> Protokoly Azure Monitor aktuálně fungují pouze pro veřejné základní nástroje pro vyrovnávání zatížení. Protokoly jsou k dispozici pouze pro prostředky nasazené v modelu nasazení Správce prostředků. Protokoly pro prostředky v modelu nasazení Classic nemůžete použít. Další informace o modelech nasazení najdete v tématu [Principy nasazení Správce prostředků a klasického nasazení](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Povolit protokolování

Protokolování auditu se automaticky povolí u každého prostředku Správce prostředků. Aby bylo možné začít shromažďovat data dostupná prostřednictvím těchto protokolů, je nutné povolit protokolování událostí a sondy stavu. K povolení protokolování použijte následující postup.

Přihlaste se k [Azure Portal](https://portal.azure.com). Pokud ještě nemáte Nástroj pro vyrovnávání zatížení, vytvořte před pokračováním [Nástroj pro vyrovnávání zatížení](load-balancer-get-started-internet-arm-ps.md) .

1. Na portálu klikněte na **Procházet**.
2. Vyberte nástroje pro vyrovnávání **zatížení**.

    ![portál – Nástroj pro vyrovnávání zatížení](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Vyberte existující > Nástroje pro vyrovnávání zatížení > **všechna nastavení**.
4. Na pravé straně dialogového okna pod názvem nástroje pro vyrovnávání zatížení přejděte na **monitorování**a klikněte na **Diagnostika**.

    ![portál – Nástroj pro vyrovnávání zatížení – nastavení](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. V podokně **Diagnostika** v části **stav**vyberte **zapnuto**.
6. Klikněte na **účet úložiště**.
7. V části **protokoly**vyberte existující účet úložiště nebo vytvořte nový. Pomocí posuvníku určíte, kolik dní se má v protokolech událostí ukládat data události. 
8. Klikněte na **Uložit**.

Diagnostika se uloží do Table Storage v zadaném účtu úložiště. Pokud se protokoly neukládají, je to proto, že nejsou vytvářeny žádné relevantní protokoly.

![portál – diagnostické protokoly](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> Protokoly auditu nevyžadují samostatný účet úložiště. Použití úložiště pro protokolování událostí a testování stavu bude účtovat poplatky za služby.

## <a name="audit-log"></a>Protokol auditu

Protokol auditu se vygeneruje ve výchozím nastavení. Protokoly se uchovávají po 90 dnech v úložišti protokolů událostí Azure. Další informace o těchto protokolech najdete v článku [zobrazení událostí a protokolů auditu](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="alert-event-log"></a>Protokol událostí výstrah

Tento protokol se vygeneruje jenom v případě, že jste ho povolili na základě nástroje pro vyrovnávání zatížení. Události se zaznamenávají ve formátu JSON a ukládají se do účtu úložiště, který jste zadali při povolování protokolování. Následuje příklad události.

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

Výstup JSON zobrazuje vlastnost *EventName* , která popíše důvod, proč Nástroj pro vyrovnávání zatížení vytvořil výstrahu. V tomto případě byla vygenerovaná výstraha způsobená vyčerpáním portů TCP způsobeným omezeními zdrojového překladu IP adres (SNAT).

## <a name="health-probe-log"></a>Protokol sondy stavu

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

Výstup JSON se zobrazí v poli vlastnosti základní informace o stavu sondy. Vlastnost *dipDownCount* zobrazuje celkový počet instancí na back-endu, které nepříjemují síťový provoz z důvodu nezdařených odpovědí testu.

## <a name="view-and-analyze-the-audit-log"></a>Zobrazit a analyzovat protokol auditu

Data protokolu auditu můžete zobrazit a analyzovat pomocí kterékoli z následujících metod:

* **Nástroje Azure:** Načtěte informace z protokolů auditu prostřednictvím Azure PowerShell, rozhraní příkazového řádku Azure (CLI), Azure REST API nebo portálu Azure Preview. Podrobné pokyny pro jednotlivé metody jsou podrobně popsané v článku [operace auditu s správce prostředků](../azure-resource-manager/resource-group-audit.md) .
* **Power BI:** Pokud ještě nemáte účet [Power BI](https://powerbi.microsoft.com/pricing) , můžete si ho vyzkoušet zdarma. Pomocí [balíčku obsahu protokoly auditování Azure pro Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs)můžete analyzovat data pomocí předem nakonfigurovaných řídicích panelů nebo můžete přizpůsobit zobrazení podle svých požadavků.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Zobrazit a analyzovat sondu stavu a protokol událostí

Musíte se připojit ke svému účtu úložiště a načíst záznamy protokolu JSON pro protokoly událostí a stavu testu. Po stažení souborů JSON je můžete převést na CSV a zobrazit v Excelu, PowerBI nebo jakémkoli jiném nástroji pro vizualizaci dat.

> [!TIP]
> Pokud znáte Visual Studio a máte představu, jak u konstant a proměnných v jazyce C# měnit hodnoty, můžete použít [nástroje pro převedení protokolů](https://github.com/Azure-Samples/networking-dotnet-log-converter), které jsou k dispozici na GitHubu.

## <a name="additional-resources"></a>Další zdroje

* [Vizualizujte protokoly auditu Azure pomocí Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) Blogový příspěvek.
* Umožňuje [Zobrazit a analyzovat protokoly auditu Azure v Power BI a další](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) Blogový příspěvek.

## <a name="next-steps"></a>Další postup

[Porozumění testům nástroje pro vyrovnávání zatížení](load-balancer-custom-probe-overview.md)
