---
title: Monitorování operací, událostí a čítačů pro veřejný Nástroj pro vyrovnávání zatížení
titleSuffix: Azure Load Balancer
description: Naučte se, jak povolit protokolování pro Azure Load Balancer.
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
ms.openlocfilehash: 59359cdd3e3c035d4cb6789295d41bb3908019bb
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785816"
---
# <a name="azure-monitor-logs-for-azure-standard-load-balancer"></a>Protokoly Azure Monitor pro Azure Standard Load Balancer

Ke správě a odstraňování potíží s Azure Standard Load Balancer můžete použít různé typy protokolů Azure Monitor. Protokoly můžou být streamované do centra událostí nebo do pracovního prostoru Log Analytics. Můžete extrahovat všechny protokoly z Azure Blob Storage a zobrazit je v nástrojích, jako je Excel a Power BI. 

Typy protokolů jsou:

* **Protokoly aktivit:** Můžete zobrazit všechny aktivity odesílané do vašich předplatných Azure spolu s jejich stavem. Další informace najdete v tématu [zobrazení protokolů aktivit pro monitorování akcí u prostředků](../azure-resource-manager/management/view-activity-logs.md). Protokoly aktivit jsou ve výchozím nastavení povolené a dají se zobrazit v Azure Portal. Tyto protokoly jsou k dispozici pro Azure Basic Load Balancer i Standard Load Balancer.
* **Standard Load Balancer metriky:** Tento protokol můžete použít k dotazování na metriky exportované jako protokoly pro Standard Load Balancer. Tyto protokoly jsou k dispozici pouze pro Standard Load Balancer.

> [!IMPORTANT]
> Protokoly událostí sondy stavu a Load Balancer výstrah nejsou v současné době funkční a jsou uvedeny ve [známých problémech pro Azure Load Balancer](whats-new.md#known-issues). 

> [!IMPORTANT]
> Protokoly jsou k dispozici pouze pro prostředky nasazené v modelu nasazení Azure Resource Manager. Protokoly pro prostředky v modelu nasazení Classic nemůžete použít. Další informace o modelech nasazení najdete v tématu [Principy nasazení Správce prostředků a klasického nasazení](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Povolit protokolování

Protokolování aktivit je u každého prostředku Správce prostředků povolené automaticky. Povolte protokolování událostí a sondy stavu, abyste mohli začít shromažďovat data dostupná přes tyto protokoly. Použijte k tomu následující postup:

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Pokud ještě nemáte Nástroj pro vyrovnávání zatížení, vytvořte před pokračováním [Nástroj pro vyrovnávání zatížení](./quickstart-load-balancer-standard-public-portal.md) .
1. Na portálu vyberte **skupiny prostředků**.
2. Vyberte **\<resource-group-name>** , kde je nástroj pro vyrovnávání zatížení.
3. Vyberte svůj Load Balancer.
4. Vyberte   >  **nastavení diagnostiky** protokolu aktivit.
5. V podokně **nastavení diagnostiky** v části **nastavení diagnostiky** vyberte **+ Přidat nastavení diagnostiky**.
6. V podokně vytvoření **nastavení diagnostiky** do pole **název** zadejte **myLBDiagnostics** .
7. Pro **nastavení diagnostiky** máte tři možnosti. Můžete zvolit jednu, dvě nebo všechny tři a nakonfigurovat každou z těchto požadavků:

   * **Archivujte do účtu úložiště**. Budete potřebovat účet úložiště, který se pro tento proces už vytvořil. Informace o vytvoření účtu úložiště najdete v tématu [Vytvoření účtu úložiště](../storage/common/storage-account-create.md?tabs=azure-portal).
     1. Zaškrtněte políčko **archivovat do účtu úložiště** .
     2. Vyberte **Konfigurovat** a otevřete tak podokno **Vybrat účet úložiště** .
     3. V rozevíracím seznamu **předplatné** vyberte předplatné, ve kterém se vytvořil váš účet úložiště.
     4. V rozevíracím seznamu **účet úložiště** vyberte název svého účtu úložiště.
     5. Vyberte **OK**.

   * **Streamování do centra událostí**. Pro tento proces budete potřebovat již vytvořenou centrum událostí. Informace o vytvoření centra událostí najdete v tématu [rychlý Start: vytvoření centra událostí pomocí Azure Portal](../event-hubs/event-hubs-create.md).
     1. Zaškrtněte políčko **datový proud do centra událostí** .
     2. Vyberte **Konfigurovat** a otevřete tak podokno **Vybrat centrum událostí** .
     3. V rozevíracím seznamu **předplatné** vyberte předplatné, ve kterém se vaše centrum událostí vytvořilo.
     4. V rozevíracím seznamu **Vybrat obor názvů centra událostí** vyberte obor názvů.
     5. V rozevíracím seznamu **Vybrat název zásad centra událostí** vyberte název.
     6. Vyberte **OK**.

   * **Odeslat do Log Analytics**. Pro tento proces budete potřebovat pracovní prostor Log Analytics vytvořený a nakonfigurovaný. Chcete-li vytvořit pracovní prostor Log Analytics, přečtěte si téma [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../azure-monitor/learn/quick-create-workspace.md).
     1. Zaškrtněte políčko **Poslat do Log Analytics**.
     2. V rozevíracím seznamu **předplatné** vyberte předplatné, ve kterém je váš pracovní prostor Log Analytics.
     3. V rozevíracím seznamu **Log Analytics pracovní prostor** vyberte pracovní prostor.

8. V části **metrika** v podokně **nastavení diagnostiky** zaškrtněte políčko **AllMetrics** .

9. Ověřte, zda je vše v pořádku, a pak vyberte **Uložit** v horní části podokna vytváření **nastavení diagnostiky** .

## <a name="view-and-analyze-the-activity-log"></a>Zobrazení a analýza protokolu aktivit

Protokol aktivit je ve výchozím nastavení vygenerován. Můžete ji nakonfigurovat tak, aby se vyexportovali na úrovni předplatného, a to podle [pokynů uvedených v tomto článku](../azure-monitor/platform/activity-log.md). Další informace o těchto protokolech najdete v článku [zobrazení protokolů aktivit pro monitorování akcí v prostředcích](../azure-resource-manager/management/view-activity-logs.md) .

Data protokolu aktivit můžete zobrazit a analyzovat pomocí některé z následujících metod:

* **Nástroje Azure:** Načtěte informace z protokolu aktivit prostřednictvím Azure PowerShell, Azure CLI, Azure REST API nebo Azure Portal. Podrobné pokyny pro jednotlivé metody najdete v článku [operace auditu s správce prostředků](../azure-resource-manager/management/view-activity-logs.md) .
* **Power BI:** Pokud ještě nemáte účet [Power BI](https://powerbi.microsoft.com/pricing) , můžete si ho vyzkoušet zdarma. Díky [integraci protokolů auditu Azure pro Power BI](https://powerbi.microsoft.com/integrations/azure-audit-logs/)můžete analyzovat data pomocí předkonfigurovaných řídicích panelů. Můžete si také přizpůsobit zobrazení podle svých požadavků.

## <a name="view-and-analyze-metrics-as-logs"></a>Zobrazit a analyzovat metriky jako protokoly
Pomocí funkce exportu v Azure Monitor můžete exportovat metriky Load Balancer. Tyto metriky vygenerují záznam protokolu pro každý interval vzorkování s jedním minutou.

Export metriky na protokoly je povolený na úrovni jednotlivých prostředků. Postup povolení těchto protokolů:

1. Přejít do podokna **nastavení diagnostiky** .
1. Filtrovat podle skupiny prostředků a potom vyberte instanci Load Balancer, pro kterou chcete povolit export metrik. 
1. Pokud je stránka nastavení diagnostiky pro Load Balancer nahoru, vyberte **AllMetrics** a exportujte vhodné metriky jako protokoly.

Omezení exportu metriky najdete v části [omezení](#limitations) v tomto článku.

Pokud v nastavení diagnostiky Standard Load Balancer povolíte **AllMetrics** , pokud používáte centrum událostí nebo Log Analytics pracovní prostor, tyto protokoly se naplní v tabulce **AzureMonitor** . 

Pokud exportujete do úložiště, připojte se k účtu úložiště a načtěte položky protokolu JSON pro protokoly událostí a stavu testu. Po stažení souborů JSON je můžete převést na CSV a zobrazit je v Excelu, Power BI nebo jakémkoli jiném nástroji pro vizualizaci dat. 

> [!TIP]
> Pokud jste obeznámeni se sadou Visual Studio a základními koncepty úprav hodnot konstant a proměnných v jazyce C#, můžete použít [Nástroje pro převod protokolů](https://github.com/Azure-Samples/networking-dotnet-log-converter) , které jsou k dispozici na GitHubu.

## <a name="stream-to-an-event-hub"></a>Streamovat do centra událostí
Když se diagnostické informace streamují do centra událostí, můžete je použít pro centralizovanou analýzu protokolů v nástroji SIEM partnera s integrací Azure Monitor. Další informace najdete v tématu [streamování dat monitorování Azure do centra událostí](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

## <a name="send-to-log-analytics"></a>Odeslání do Log Analytics
Diagnostické informace o prostředcích v Azure můžete odesílat přímo do pracovního prostoru Log Analytics. V tomto pracovním prostoru můžete spouštět složité dotazy na informace pro řešení potíží a analýzu. Další informace najdete v tématu [shromáždění protokolů prostředků Azure v pracovním prostoru Log Analytics v Azure monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).

## <a name="limitations"></a>Omezení
Funkce exportu metriky do protokolu pro Azure Load Balancer má následující omezení:
* Metriky se při exportu jako protokolů v tuto chvíli zobrazují prostřednictvím interních názvů. Mapování najdete v níže uvedené tabulce.
* Dimenze metriky se nezachovají. Například s metrikami, jako je **DipAvailability** (stav sondy stavu), nebudete moci rozdělit ani zobrazit na back-endové IP adrese.
* Metriky pro použité porty SNAT a přidělené porty SNAT nejsou aktuálně k dispozici pro export jako protokoly.

## <a name="next-steps"></a>Další kroky
* [Zkontrolujte dostupné metriky pro nástroj pro vyrovnávání zatížení.](./load-balancer-standard-diagnostics.md)
* [Vytváření a testování dotazů podle následujících pokynů Azure Monitor](../azure-monitor/log-query/log-query-overview.md)