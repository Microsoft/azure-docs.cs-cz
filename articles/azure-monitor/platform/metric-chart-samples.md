---
title: Příklad grafu metriky Azure Monitoru
description: Přečtěte si o vizualizaci dat Azure Monitoru.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 1aaeb853a67b36a21a09db57e015029d10cd0c36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660866"
---
# <a name="metric-chart-examples"></a>Příklady grafu metriky 

Platforma Azure nabízí [více než tisíc metrik ,](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)z nichž mnohé mají dimenze. Pomocí [filtrů dimenzí](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), použití [rozdělení](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), řízení typu grafu a úpravy nastavení grafu můžete vytvořit výkonná diagnostická zobrazení a řídicí panely, které poskytují přehled o stavu infrastruktury a aplikací. Tento článek ukazuje některé příklady grafů, které můžete vytvořit pomocí [Průzkumníka metrik](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) a vysvětluje nezbytné kroky ke konfiguraci každého z těchto grafů.

Chcete se podělit o své skvělé grafy příklady se světem? Přispějte na tuto stránku na GitHubu a sdílejte své vlastní příklady grafů zde!

## <a name="website-cpu-utilization-by-server-instances"></a>Využití procesoru webu podle instancí serveru

Tento graf ukazuje, jestli procesor pro službu app služby byl v přijatelném rozsahu a rozdělí ji podle instance k určení, zda zatížení bylo správně distribuováno. Z grafu, že aplikace běžela na jedné instanci serveru před 6:00, můžete vidět a potom se škálovat přidáním další instance.

![Spojnicový graf průměrného procenta procesoru podle instance serveru](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Jak nakonfigurovat tento graf?

Vyberte prostředek služby App Service a najděte metriku **Procento procesoru.** Pak klikněte na **Použít rozdělení** a vyberte dimenzi **Instance.**

## <a name="application-availability-by-region"></a>Dostupnost aplikací podle oblastí

Zobrazte dostupnost aplikace podle oblasti a určete, která zeměpisná umístění mají problémy. Tento graf zobrazuje metriku dostupnosti přehledů aplikací. Můžete vidět, že monitorovaná aplikace nemá žádný problém s dostupností z datového centra Východní USA, ale dochází k problému částečné dostupnosti ze západní USA a východní Asie.

![Graf průměrné dostupnosti podle lokalit](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Jak nakonfigurovat tento graf?

Nejprve musíte zapnout monitorování [dostupnosti Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability) pro váš web. Poté vyberte prostředek Application Insights a vyberte metriku dostupnost. Použít rozdělení na dimenzi **Spustit umístění.**

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Objem transakcí účtu úložiště podle názvu rozhraní API

U prostředku účtu úložiště dochází k nadměrnému objemu transakcí. Metriku transakcí můžete použít k určení, které rozhraní API je zodpovědné za nadměrné zatížení. Všimněte si, že následující graf je nakonfigurován se stejnou dimenzí (název rozhraní API) při filtrování a rozdělení zúžit zobrazení pouze na volání rozhraní API zájmu:

![Pruhový graf transakcí rozhraní API](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Jak nakonfigurovat tento graf?

Ve výběru metrik vyberte účet úložiště a metriku **Transakce.** Přepnutí typu grafu na **pruhový graf**. Klepněte na **tlačítko Použít rozdělení** a vyberte název rozhraní **API**dimenze . Poté klikněte na **filtr Přidat** a znovu vyberte dimenzi názvu **rozhraní API.** V dialogovém okně filtru vyberte v grafu vykreslovat api, která chcete vykreslit.

## <a name="next-steps"></a>Další kroky

* Informace o [sešitech](../../azure-monitor/app/usage-workbooks.md) Azure Monitor
* Další informace o [Průzkumníku metrik](metrics-charts.md)
