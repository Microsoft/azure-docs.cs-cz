---
title: Monitorování operací, události a čítače pro veřejný Load balancer úrovně Basic | Dokumentace Microsoftu
description: Zjistěte, jak povolit oznámení události a sběru dat stavu stav protokolování pro veřejný Load balancer úrovně Basic
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
ms.date: 08/10/2018
ms.author: kumud
ms.openlocfilehash: 5c196a24d5cc9e4343762e55b5885c1264c2fcad
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42055281"
---
# <a name="log-analytics-for-public-basic-load-balancer"></a>Log analytics pro veřejný Load balancer úrovně Basic

>[!IMPORTANT] 
>Azure Load Balancer podporuje dva různé typy: Basic a Standard. Tento článek popisuje Load Balancer úrovně Basic. Další informace o Load balanceru úrovně Standard najdete v tématu [Load balanceru úrovně Standard přehled](load-balancer-standard-overview.md) která zveřejní telemetrická data prostřednictvím vícedimenzionálních metrik ve službě Azure Monitor.

Správa a řešení potíží s základní nástroje pro vyrovnávání zatížení můžete použít různé typy protokolů v Azure. Některé z těchto protokolů můžete přistupovat prostřednictvím portálu. Všechny protokoly můžete extrahují z úložiště objektů blob v Azure a zobrazit v různých nástrojů, jako je například aplikace Excel a Power BI. Další informace o různých typech protokoly ze seznamu níže.

* **Protokoly auditu:** můžete použít [protokolů auditu Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (dříve označované jako provozní protokoly) Chcete-li zobrazit všechny operace odeslání vašich předplatných Azure a jejich stav. Protokoly auditu jsou ve výchozím nastavení povolená a můžete zobrazit na webu Azure Portal.
* **Upozornění protokolů událostí:** tento protokol můžete zobrazit výstrahy vyvolané nástroje pro vyrovnávání zatížení. Stav nástroje pro vyrovnávání zatížení shromažďovaných každých pět minut. Tento protokol je zapsán pouze pokud je vyvolána na událost výstrahy nástroje pro vyrovnávání zatížení.
* **Protokoly sond stavu:** tohoto protokolu můžete použít k zobrazení problémů zjištěných vaše sondu stavu, jako je počet instancí v back-endového fondu, které nepřijímá požadavky z nástroje pro vyrovnávání zatížení z důvodu selhání testu stavu. Tento protokol je zapsán do, když dojde ke změně v sonda stavu.

> [!IMPORTANT]
> Protokolovat analytics aktuálně funguje pouze pro veřejné základní nástroje pro vyrovnávání zatížení. Protokoly jsou dostupné jenom pro prostředky nasazené v modelu nasazení Resource Manager. Protokoly nelze použít pro prostředky v modelu nasazení classic. Další informace o modelech nasazení najdete v tématu [vysvětlení Resource Manager a klasické nasazení](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Povolit protokolování

Protokolování auditu je automaticky povolené pro všechny prostředku Resource Manageru. Je potřeba povolit události a stav testu protokolování shromažďování dat, které jsou k dispozici prostřednictvím těchto protokolů. Následujícím postupem povolíte protokolování.

Přihlaste se do [webu Azure portal](http://portal.azure.com). Pokud ještě nemáte nástroj pro vyrovnávání zatížení [vytvořit nástroj pro vyrovnávání zatížení](load-balancer-get-started-internet-arm-ps.md) předtím, než budete pokračovat.

1. Na portálu klikněte na tlačítko **Procházet**.
2. Vyberte **nástroje pro vyrovnávání zatížení**.

    ![portálu - nástroj pro vyrovnávání zatížení](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Vyberte existující pro vyrovnávání zatížení >> **všechna nastavení**.
4. Na pravé straně dialogu pod názvem nástroje pro vyrovnávání zatížení, přejděte na **monitorování**, klikněte na tlačítko **diagnostiky**.

    ![nastavení nástroje pro vyrovnávání zatížení – portál](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. V **diagnostiky** podokně v části **stav**vyberte **na**.
6. Klikněte na tlačítko **účtu úložiště**.
7. V části **protokoly**, vyberte existující účet úložiště nebo vytvořte novou. Chcete-li zjistit, kolik dní, po který data událostí se uloží v protokolech událostí pomocí posuvníku. 
8. Klikněte na **Uložit**.

Diagnostika se uloží ve službě Table Storage v zadaný účet úložiště. Pokud protokoly se neuloží, bude to, že žádné odpovídající protokoly jsou generovaná.

![Portál – protokoly diagnostiky](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> Protokoly auditu nevyžadují samostatný účet úložiště. Použití úložiště pro události a stav protokolování testu budou účtovat poplatky službu.

## <a name="audit-log"></a>Protokol auditu

Standardně se generuje protokol auditu. Protokoly jsou zachovány po dobu 90 dnů v úložišti protokoly událostí Azure. Další informace o těchto protokolů najdete [zobrazení událostí a protokolů auditu](../monitoring-and-diagnostics/insights-debugging-with-events.md) článku.

## <a name="alert-event-log"></a>Upozornění protokolu událostí

Tento protokol se vygeneruje pouze tehdy, pokud jste ho povolili na základě nástroje pro vyrovnávání zatížení. Události jsou protokolovány ve formátu JSON a uložený v účtu úložiště, které jste zadali při povolování protokolování. Následuje příklad události.

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

Výstup ve formátu JSON ukazuje *eventname* vlastnost, která popisuje příčinu nástroje pro vyrovnávání zatížení vytvoří výstrahu. Výstrahy vygenerované v tomto případě byla vyčerpání portů TCP způsobené zdroj překladu IP adres omezení (SNAT).

## <a name="health-probe-log"></a>Protokol sondy stavu

Tento protokol se vygeneruje pouze tehdy, pokud jste ho povolili na základě zatížení nástroje pro vyrovnávání popsané výše. Data se ukládají v účtu úložiště, které jste zadali při povolování protokolování. Vytvořit kontejner s názvem "insights loadbalancerprobehealthstatus protokoly" a se protokolují tato data:

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

Ve výstupu JSON zobrazí v poli vlastnosti základní informace o stavu testu. *DipDownCount* vlastnost zobrazuje celkový počet instancí na back-end, který síťový provoz kvůli neúspěšným odpovědím sondy nepřijímá.

## <a name="view-and-analyze-the-audit-log"></a>Zobrazení a analýza protokolů auditu

Můžete zobrazit a analyzovat data protokolů auditu pomocí kteréhokoli z následujících metod:

* **Nástroje Azure:** načítat informace z protokolů auditu prostřednictvím Azure Powershellu, rozhraní příkazového řádku Azure (CLI), rozhraní REST API služby Azure nebo na portálu Azure preview. Podrobné pokyny pro jednotlivé metody jsou podrobně popsané na [Audit operací pomocí Resource Manageru](../azure-resource-manager/resource-group-audit.md) článku.
* **Power BI:** Pokud již nemáte [Power BI](https://powerbi.microsoft.com/pricing) účet, můžete zkusit ho zdarma. Použití [obsahu protokoly auditování Azure pro Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), můžete analyzovat svá data pomocí předem nakonfigurovaných řídicích panelů nebo si můžete přizpůsobit zobrazení tak, aby vyhovoval vašim požadavkům.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Zobrazení a analýza sondu stavu a protokolu událostí

Budete muset připojit ke svému účtu úložiště a načtení položky protokolu JSON pro protokoly událostí a stavu testu. Po stažení souborů JSON je je převést na sdílený svazek clusteru a v Excelu, Power BI nebo jakýkoli jiný nástroj pro vizualizaci dat zobrazení.

> [!TIP]
> Pokud znáte Visual Studio a máte představu, jak u konstant a proměnných v jazyce C# měnit hodnoty, můžete použít [nástroje pro převedení protokolů](https://github.com/Azure-Samples/networking-dotnet-log-converter), které jsou k dispozici na GitHubu.

## <a name="additional-resources"></a>Další zdroje informací:

* [Vizualizace protokolů auditu Azure pomocí Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogový příspěvek.
* [Zobrazení a analýza protokolů auditu Azure v Power BI a další](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogový příspěvek.

## <a name="next-steps"></a>Další postup

[Porozumění testům nástroje pro vyrovnávání zatížení](load-balancer-custom-probe-overview.md)
