---
title: Monitorování operací, událostí a čítačů pro veřejný základní Load Balancer
titleSuffix: Azure Load Balancer
description: Naučte se, jak povolit protokolování pro Azure Load Balancer
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
ms.openlocfilehash: 6742723e24df83ac8112e224f1999f116ab82c94
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572775"
---
# <a name="azure-monitor-logs-for-the-standard-azure-load-balancer"></a>Protokoly Azure Monitor pro standardní Azure Load Balancer

Pomocí různých typů protokolů v Azure můžete spravovat a řešit standardní nástroje pro vyrovnávání zatížení. Protokoly můžou být streamované do centra událostí nebo do pracovního prostoru Log Analytics. Všechny protokoly se dají extrahovat z úložiště objektů BLOB v Azure a zobrazovat v různých nástrojích, jako je Excel a Power BI.  Další informace o různých typech protokolů najdete v níže uvedeném seznamu.

* **Protokoly aktivit:** Pomocí [možnosti Zobrazit protokoly aktivit můžete monitorovat akce v prostředcích](../azure-resource-manager/management/view-activity-logs.md) a zobrazit všechny aktivity odeslané do vašich předplatných Azure a jejich stav. Protokoly aktivit jsou ve výchozím nastavení povolené a dají se zobrazit v Azure Portal. Tyto protokoly jsou k dispozici pro služby Vyrovnávání zatížení Basic i Standard.
* **Standard Load Balancer metriky:** Tento protokol můžete použít k dotazování na metriky exportované jako protokoly pro standardní Azure Load Balancer. Tyto protokoly jsou dostupné jenom pro standardní nástroje pro vyrovnávání zatížení.

> [!IMPORTANT]
> **Protokoly událostí sondy stavu a Load Balancer výstrah nejsou v současné době funkční a jsou uvedeny ve [známých problémech pro Azure Load Balancer](whats-new.md#known-issues).** 

> [!IMPORTANT]
> Protokoly jsou k dispozici pouze pro prostředky nasazené v modelu nasazení Správce prostředků. Protokoly pro prostředky v modelu nasazení Classic nemůžete použít. Další informace o modelech nasazení najdete v tématu [Principy nasazení Správce prostředků a klasického nasazení](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Povolit protokolování

Protokolování aktivit je u každého prostředku Správce prostředků povolené automaticky. Povolte protokolování událostí a sondy stavu, abyste mohli začít shromažďovat data dostupná přes tyto protokoly. K povolení protokolování použijte následující postup.

Přihlaste se k [portálu Azure Portal](https://portal.azure.com). Pokud ještě nemáte Nástroj pro vyrovnávání zatížení, vytvořte před pokračováním [Nástroj pro vyrovnávání zatížení](./quickstart-load-balancer-standard-public-portal.md) .

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


8.  Pod oddílem **metrika** v podokně **nastavení diagnostiky** zaškrtněte políčko u: **AllMetrics**

9. Ověřte, zda je vše v pořádku, a v horní části podokna vytvořit **nastavení diagnostiky** klikněte na **Uložit** .

## <a name="activity-log"></a>Protokol aktivit

Protokol aktivit je ve výchozím nastavení vygenerován. Dá se nakonfigurovat tak, aby se vyexportoval na úrovni předplatného podle [pokynů v tomto článku](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log). Další informace o těchto protokolech najdete v článku [zobrazení protokolů aktivit pro monitorování akcí v prostředcích](../azure-resource-manager/management/view-activity-logs.md) .

### <a name="view-and-analyze-the-activity-log"></a>Zobrazení a analýza protokolu aktivit

Data protokolu aktivit můžete zobrazit a analyzovat pomocí kterékoli z následujících metod:

* **Nástroje Azure:** Načtěte informace z protokolu aktivit prostřednictvím Azure PowerShell, rozhraní příkazového řádku Azure (CLI), Azure REST API nebo Azure Portal. Podrobné pokyny pro jednotlivé metody jsou podrobně popsané v článku [operace auditu s správce prostředků](../azure-resource-manager/management/view-activity-logs.md) .
* **Power BI:** Pokud ještě nemáte účet [Power BI](https://powerbi.microsoft.com/pricing) , můžete si ho vyzkoušet zdarma. Pomocí [integrace protokolů auditu Azure pro Power BI](https://powerbi.microsoft.com/integrations/azure-audit-logs/)můžete analyzovat data pomocí předem nakonfigurovaných řídicích panelů nebo můžete přizpůsobit zobrazení podle svých požadavků.

## <a name="metrics-as-logs"></a>Metriky jako protokoly
Použití metrik k protokolování funkcí exportu poskytovaných Azure Monitor můžete exportovat Load Balancer metriky. Tyto metriky vygenerují záznam protokolu pro každý minutový interval vzorkování.

Metrika pro export protokolu je povolená na úrovni jednotlivých prostředků. Tyto protokoly můžete povolit tak, že v okně nastavení diagnostiky kliknete na filtrovat podle skupiny prostředků a vyberete Load Balancer chcete povolit export metrik pro. Pokud je stránka nastavení diagnostiky Load Balancer nahoru, vyberte AllMetrics a exportujte vhodné metriky jako protokoly.

Omezení exportu metrik najdete v části [omezení](#limitations) v tomto článku.

### <a name="view-and-analyze-metrics-as-logs"></a>Zobrazit a analyzovat metriky jako protokoly
Po povolení AllMetrics v nastavení diagnostiky vašeho Standard Load Balancer, pokud používáte centrum událostí nebo Log Analytics pracovní prostor, tyto protokoly se naplní v tabulce AzureMonitor. Pokud exportujete do úložiště, připojte se k účtu úložiště a načtěte položky protokolu JSON pro protokoly událostí a stavu testu. Po stažení souborů JSON je můžete převést na CSV a zobrazit v Excelu, Power BI nebo jakémkoli jiném nástroji pro vizualizaci dat. 

> [!TIP]
> Pokud znáte Visual Studio a máte představu, jak u konstant a proměnných v jazyce C# měnit hodnoty, můžete použít [nástroje pro převedení protokolů](https://github.com/Azure-Samples/networking-dotnet-log-converter), které jsou k dispozici na GitHubu.

## <a name="stream-to-an-event-hub"></a>Streamovat do centra událostí
Když se diagnostické informace streamují do centra událostí, dá se použít k centralizované analýze protokolů v nástroji SIEM třetí strany s integrací Azure Monitor. Další informace najdete v tématu [streamování dat monitorování Azure do centra událostí](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration) .

## <a name="send-to-log-analytics"></a>Odeslání do Log Analytics
Prostředky v Azure můžou mít své diagnostické informace odesílány přímo do Log Analytics pracovního prostoru, kde můžete provádět složité dotazy na informace pro řešení potíží a analýzy.  Další informace najdete v tématu [shromáždění protokolů prostředků Azure v pracovním prostoru Log Analytics v Azure monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)

## <a name="limitations"></a>Omezení
Při použití funkce metriky k protokolování funkcí exportu pro nástroje pro vyrovnávání zatížení existují v současné době tato omezení:
* Metriky se aktuálně zobrazují s použitím interních názvů, pokud jsou exportované jako protokoly, mapování v níže uvedené tabulce.
* Dimenze metriky se nezachovají. Například s metrikami jako DipAvailability (stav sondy stavu) nebudete moci rozdělit nebo zobrazit podle IP adresy back-endu.
* Používané porty SNAT a přidělené metriky portů SNAT nejsou aktuálně k dispozici pro export jako protokoly.

## <a name="next-steps"></a>Další kroky
* [Zkontrolujte dostupné metriky pro váš Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)
* [Vytváření a testování dotazů za Azure Monitor instrukcí](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
* Poskytněte nám svůj názor na tento článek nebo Load Balancer funkce pomocí níže uvedených odkazů.
