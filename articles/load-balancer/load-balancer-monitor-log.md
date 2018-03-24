---
title: Sledování operací, události a čítače pro vyrovnávání zatížení | Microsoft Docs
description: Zjistěte, jak povolit události výstrah a sběru dat stavu stav protokolování pro nástroj pro vyrovnávání zatížení Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: 56656d74-0241-4096-88c8-aa88515d676d
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: dabf4bcae957559978e731636bb13554f1a68b73
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="log-analytics-for-azure-load-balancer"></a>Log Analytics pro Azure Load Balancer

>[!NOTE] 
>Azure Load Balancer podporuje dva různé typy: Basic a Standard. Tento článek popisuje Load Balancer úrovně Basic. Další informace o standardní nástroje pro vyrovnávání zatížení najdete v tématu [nástroj pro vyrovnávání zatížení přehled](load-balancer-standard-overview.md).

Různé typy protokolů v Azure můžete použít ke správě a odstraňování potíží nástroje pro vyrovnávání zatížení. Některé z těchto protokolů jsou přístupné prostřednictvím portálu. Všechny protokoly můžete extrahovat z Azure blob storage a zobrazit v různých nástrojů, jako je například aplikace Excel a PowerBI. Další informace o různých typech protokolů z níže uvedeného seznamu.

* **Protokoly auditu:** můžete použít [protokoly auditu Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (dříve označované jako operační protokoly) Chcete-li zobrazit všechny operace odeslání vašich předplatných Azure a jejich stav. Protokoly auditu jsou ve výchozím nastavení povolené a lze zobrazit na portálu Azure.
* **Výstrahy protokoly událostí:** tento protokol můžete zobrazit výstrahy vyvolané nástroje pro vyrovnávání zatížení. Stav nástroje pro vyrovnávání zatížení je shromažďovaných každých pět minut. Tento protokol je zapsán pouze pokud je vyvolána o událost výstrahy nástroje pro vyrovnávání zatížení.
* **Stav testu protokoly:** tento protokol můžete použít k zobrazení problémů zjištěných váš test stavu, jako je počet instancí ve vašem fondu back-end, které nejsou přijímání požadavků z nástroje pro vyrovnávání zatížení z důvodu selhání kontroly stavu. Tento protokol je zapsán do, když dojde ke změně v stav testu.

> [!IMPORTANT]
> Analýza protokolu aktuálně funguje pouze pro internetové nástroje pro vyrovnávání zatížení. Protokoly jsou dostupné pouze pro prostředky nasazené v modelu nasazení Resource Manager. Protokoly nelze použít pro prostředky v modelu nasazení classic. Další informace o modelech nasazení najdete v tématu [nasazení Resource Manager principy a nasazení classic](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Povolit protokolování

Pro každý prostředek Resource Manager je automaticky povolené protokolování auditu. Budete muset povolit události a stav testu protokolování spustit shromažďování dat, které jsou k dispozici prostřednictvím tyto protokoly. Pomocí následujících kroků povolte protokolování.

Přihlaste se do [portál Azure](http://portal.azure.com). Pokud ještě nemáte nástroj pro vyrovnávání zatížení [vytvořit nástroj pro vyrovnávání zatížení](load-balancer-get-started-internet-arm-ps.md) než budete pokračovat.

1. Na portálu, klikněte na tlačítko **Procházet**.
2. Vyberte **nástroje pro vyrovnávání zatížení**.

    ![portál – nástroj na Vyrovnávání zatížení](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Vyberte existující pro vyrovnávání zatížení >> **všechna nastavení**.
4. Na pravé straně dialogového okna pod názvem služby Vyrovnávání zatížení, přejděte na **monitorování**, klikněte na tlačítko **diagnostiky**.

    ![portál – nastavení vyrovnávání zátěže](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. V **diagnostiky** podokně v části **stav**, vyberte **na**.
6. Klikněte na tlačítko **účet úložiště**.
7. V části **protokoly**, vybrat existující účet úložiště, nebo vytvořte novou. Posuvníkem určit, kolik dní, po který data událostí se uloží v protokolech událostí. 
8. Klikněte na **Uložit**.

    ![Portál – protokolů diagnostiky](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> Protokoly auditu nevyžadují, aby účet samostatného úložiště. Použití úložiště pro události a stav testu protokolování bude platit poplatky služby.

## <a name="audit-log"></a>Protokol auditu

Ve výchozím nastavení je generování protokolu auditu. Protokoly se zachovají 90 dní v úložišti Azure a protokoly událostí. Další informace o tyto protokoly načtením [zobrazení událostí a protokolů auditování](../monitoring-and-diagnostics/insights-debugging-with-events.md) článku.

## <a name="alert-event-log"></a>Upozornění v protokolu událostí

Tento protokol je generovaný pouze v případě, že jste povolili na na základě nástroje pro vyrovnávání zatížení. Události jsou zaznamenána ve formátu JSON a uložený v účtu storage, kterou jste zadali při jste povolili protokolování. Následuje příklad události.

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

JSON výstup ukazuje *eventname* vlastnost, která popíše z důvodu nástroj pro vyrovnávání zatížení vytvořit výstrahu. V takovém případě se výstrahy generované z důvodu vyčerpání port TCP způsobené zdrojové IP NAT omezení (překládat pomocí SNAT).

## <a name="health-probe-log"></a>Stav testu protokolu

Tento protokol je generovaný pouze v případě, že jste povolili na za zatížení vyrovnávání základ popsaných výše. Data je uložený v účtu úložiště, kterou jste zadali při jste povolili protokolování. Je-li vytvořit kontejner s názvem "insights loadbalancerprobehealthstatus protokoly, a se protokolují tato data:

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

Výstup JSON obsahuje v poli vlastnosti základní informace o stav testu. *DipDownCount* vlastnost se zobrazuje celkový počet instancí v back-end, které nejsou přijímá síťový provoz z důvodu selhání testu odpovědi.

## <a name="view-and-analyze-the-audit-log"></a>Zobrazit a analyzovat protokol auditu

Můžete zobrazit a analyzovat data protokolu auditu pomocí kteréhokoli z následujících metod:

* **Nástroje Azure:** načítat informace z protokolů auditu prostřednictvím prostředí Azure PowerShell, rozhraní příkazového řádku Azure (CLI), REST API služby Azure nebo portálu Azure preview. Podrobné pokyny pro jednotlivé metody jsou podrobně popsané na [auditovat operace s Resource Managerem](../azure-resource-manager/resource-group-audit.md) článku.
* **Power BI:** Pokud již nemáte [Power BI](https://powerbi.microsoft.com/pricing) účet, můžete zkusit je zdarma. Pomocí [obsahu protokoly auditu Azure pack pro Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), můžete analyzovat svá data pomocí předem nakonfigurovaných řídicí panely, nebo můžete přizpůsobit zobrazení podle svých potřeb.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Zobrazení a analýza protokolů událostí a test stavu

Budete muset připojit k účtu úložiště a načítat položky protokolu JSON pro kontrolu protokolů událostí a stavu. Jakmile si stáhnout soubory JSON, můžete je převést na sdílený svazek clusteru a zobrazení v aplikaci Excel, PowerBI nebo jakýkoli jiný nástroj pro vizualizaci dat.

> [!TIP]
> Pokud jste obeznámeni s Visual Studio a základní koncepty změna hodnoty konstanty a proměnné v jazyce C#, můžete použít [protokolu nástroje Převaděč](https://github.com/Azure-Samples/networking-dotnet-log-converter) dostupné z Githubu.

## <a name="additional-resources"></a>Další zdroje informací:

* [Vaše protokoly auditu Azure s Power BI vizualizovat](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) příspěvku na blogu.
* [Zobrazení a analýza protokolů auditu Azure v Power BI a další](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) příspěvku na blogu.

## <a name="next-steps"></a>Další postup

[Porozumění testům nástroje pro vyrovnávání zatížení](load-balancer-custom-probe-overview.md)
