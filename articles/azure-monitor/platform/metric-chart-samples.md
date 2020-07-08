---
title: Příklad grafu Azure Monitor metriky
description: Seznamte se s vizualizací dat Azure Monitor.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 00935762a5e19ec47074021aff59992fd3b801bf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83797451"
---
# <a name="metric-chart-examples"></a>Příklady grafu metriky 

Platforma Azure nabízí [více než tisíc metrik](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported), z nichž mnoho má rozměry. Pomocí [filtrů dimenzí](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), použití [rozdělení](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), řízení typu grafu a úpravy nastavení grafu můžete vytvářet výkonné diagnostické pohledy a řídicí panely, které poskytují přehled o stavu vaší infrastruktury a aplikací. Tento článek ukazuje několik příkladů grafů, které můžete vytvořit pomocí [Průzkumník metrik](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) a vysvětluje kroky nezbytné ke konfiguraci každého z těchto grafů.

Chcete sdílet skvělé grafy jako příklady na celém světě? Přispívat na tuto stránku na GitHubu a sdílejte příklady vlastních grafů.

## <a name="website-cpu-utilization-by-server-instances"></a>Využití procesoru webem instancemi serveru

Tento graf znázorňuje, zda byl procesor pro App Service v přijatelném rozsahu, a rozdělí ho podle instance, aby bylo možné zjistit, zda bylo zatížení správně distribuováno. V grafu si můžete zobrazit, že aplikace běžela na jedné instanci serveru před 6. potom se navýšení škáluje přidáním jiné instance.

![Spojnicový graf průměrného procenta procesoru podle instance serveru](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Jak nakonfigurovat tento graf?

Vyberte prostředek App Service a najděte procento metriky **procesoru** . Pak klikněte na **použít rozdělení** a vyberte dimenzi **instance** .

## <a name="application-availability-by-region"></a>Dostupnost aplikace podle oblasti

Podívejte se na dostupnost vaší aplikace podle oblasti a určete, která geografická umístění mají problémy. Tento graf znázorňuje Application Insights metriku dostupnosti. Můžete vidět, že monitorovaná aplikace nemá žádný problém s dostupností z Východní USA datacentra, ale u Západní USA existuje problém s částečnou dostupností a Východní Asie.

![Graf průměrné dostupnosti podle umístění](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Jak nakonfigurovat tento graf?

Nejdřív musíte zapnout monitorování [dostupnosti Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability) pro váš web. Pak vyberte prostředek Application Insights a vyberte metriku dostupnosti. Použijte rozdělení na dimenzi **umístění běhu** .

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Objem transakcí účtu úložiště podle názvu rozhraní API

U prostředku účtu úložiště dochází k nadbytečnému objemu transakcí. Metriku transakcí můžete použít k určení, které rozhraní API zodpovídá za nadměrné zatížení. Všimněte si, že následující graf je nakonfigurovaný se stejnou dimenzí (názvem rozhraní API) při filtrování a rozdělení pro zúžení zobrazení jenom na volání rozhraní API v zájmu:

![Pruhový graf transakcí rozhraní API](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Jak nakonfigurovat tento graf?

V okně pro výběr metriky vyberte svůj účet úložiště a metriku **transakcí** . Přepnout typ grafu na **sloupcový graf** Klikněte na **použít rozdělení** a vyberte **název rozhraní API**pro dimenzi. Pak klikněte na **Přidat filtr** a znovu vyberte dimenzi **název rozhraní API** . V dialogovém okně Filtr vyberte rozhraní API, která chcete vykreslit v grafu.

## <a name="next-steps"></a>Další kroky

* Další informace o Azure Monitor [sešitech](../../azure-monitor/platform/workbooks-overview.md)
* Další informace o [Průzkumníkovi metrik](metrics-charts.md)
