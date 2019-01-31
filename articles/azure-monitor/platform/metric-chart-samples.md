---
title: Ukázky v Azure Monitor grafu metriky
description: Další informace o vizualizaci dat monitorování Azure.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: bbfeb428d38c23955df4497242184499349aecf9
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55303061"
---
# <a name="metric-chart-samples"></a>Ukázky grafu metriky

Platforma Azure nabízí [tisíc metrik](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported), mnoho, u nichž dimenze. S použitím [dimenze filtry](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), použití [rozdělení](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)řízení typ grafu a úpravě nastavení grafu můžete vytvářet výkonné diagnostické zobrazení a řídicí panely, které poskytují přehled o stavu vaší infrastruktury i aplikací. Tento článek popisuje některé příklady grafy, které můžete vytvářet pomocí [Průzkumníka metrik](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) a vysvětluje kroky potřebné ke konfiguraci každé z těchto grafů.

Chcete sdílet grafy skvělé příklady s ostatními? Přispívat na tuto stránku na Githubu a sdílet své vlastní příklady grafu tady!

## <a name="website-cpu-utilization-by-server-instances"></a>Využití webu využití procesoru podle instancí serverů

Tento graf zobrazuje Pokud CPU pro službu App Service v přípustném rozsahu a analyzují se podle instance k určení, zda byl správně distribuovat zatížení. Je vidět z grafu, který aplikace běžely na jednu instanci serveru před 6: 00 a potom vertikálně navýšila tak, že přidáte jinou instancí.

![Spojnicový graf průměrné procento využití procesoru podle instance serveru](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Jak nakonfigurovat tento graf?

Vyberte prostředek App Service a najít **procento využití procesoru** metriku. Potom klikněte na **použít rozdělení** a vyberte **Instance** dimenze.

## <a name="application-availability-by-region"></a>Dostupnost aplikace podle oblasti

Zobrazení vaší aplikace dostupnost podle oblasti k identifikaci, která geografické umístění došlo k potížím. Tento graf zobrazuje metriky dostupnosti Application Insights. Uvidíte, že monitorovanou aplikaci nemá žádný problém s dostupností mezi datovým centrem východní USA, ale dochází k potížím částečné dostupnost v oblastech západní USA a východní Asie.

![Graf, průměrná dostupnost podle umístění](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Jak nakonfigurovat tento graf?

Nejdřív potřeba zapnout [dostupnosti Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability) monitorování pro váš web. Poté vyberte prostředek Application Insights a vyberte metriku dostupnosti. Použít rozdělení na **spustit umístění** dimenze.

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Objemu transakcí účtu úložiště pomocí názvu rozhraní API

Váš prostředek účtu úložiště dochází k nadměrné objemu transakcí. Metrika transakce můžete zjistit, jaká rozhraní API je zodpovědný za nadměrné zatížení. Všimněte si, že následující graf má nakonfigurovanou stejnou dimenzi (název rozhraní API) filtrování a rozdělení můžete zúžit zobrazení na pouze volání rozhraní API zájmu:

![Pruhový graf u transakcí rozhraní API](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Jak nakonfigurovat tento graf?

V dialogu pro výběr metrik vyberte svůj účet úložiště a **transakce** metriku. Přepínač typu grafu **pruhový graf**. Klikněte na tlačítko **použít rozdělení** a vyberte dimenze **název rozhraní API**. Potom klikněte na **přidat filtr** a vybrat **název rozhraní API** dimenze ještě jednou. V dialogovém okně Filtr vyberte rozhraní API, který chcete vykreslit v grafu.

## <a name="next-steps"></a>Další postup

* Další informace o Azure Monitor [sešity](../../azure-monitor/app/usage-workbooks.md)
* Další informace o [Průzkumník metrik](metrics-charts.md)