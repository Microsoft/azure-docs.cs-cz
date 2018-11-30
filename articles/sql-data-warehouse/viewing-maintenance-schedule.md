---
title: Plány údržby Azure (preview) | Dokumentace Microsoftu
description: Plánování údržby umožňuje zákazníkům naplánovat události nezbytné plánované údržby, které služba Azure SQL Data warehouse používá k zavedení nových funkcí, upgrady a opravy.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 11/27/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 3c683d5819ded4eb3d286969b8c3fc3f0bd42865
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52497517"
---
# <a name="view-a-maintenance-schedule"></a>Zobrazit plán údržby 

## <a name="portal"></a>Portál

Ve výchozím nastavení mají všechny nově vytvořené instance Azure SQL Data Warehouse osmi hodin primární a sekundární údržbu použity během nasazení. Můžete změnit windows jak brzy dokončení nasazení. Žádná údržba bude probíhat mimo zadanou údržbu bez předchozího upozornění.

Chcete-li zobrazit plán údržby, která byla použita k vašemu datovému skladu, proveďte následující kroky:

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2.  Vyberte datový sklad, který chcete zobrazit. 
3.  Vybraný datový sklad se otevře v okně přehledu. Plán údržby, který se použije k datovému skladu se zobrazí pod **plán údržby (preview)**.

![Okno přehledu](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>Další postup
- [Další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) o vytváření, zobrazení a Správa výstrah pomocí Azure monitoru.
- [Další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) akce webhooku pro pravidla upozornění protokolů.
- [Další informace](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups) vytváření a Správa skupin akcí.
- [Další informace](https://docs.microsoft.com/azure/service-health/service-health-overview) o Azure Service Health.


