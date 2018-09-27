---
title: Plány údržby Azure (Preview) | Dokumentace Microsoftu
description: Plánování údržby umožňuje zákazníkům naplánovat nezbytné plánované údržby události služby Azure SQL Data warehouse používá k zavedení nových funkcí, upgrady a opravy.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/06/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 6fbf914c8035344d33e8d2739fb9d92d5757c3d1
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228296"
---
# <a name="viewing-a-maintenance-schedule"></a>Zobrazení plánu údržby 

## <a name="portal"></a>Portál

Ve výchozím nastavení všechny nově vytvořené instance bude mít 8 hodin definovaná systémem primárními a sekundárními údržbu použity během nasazení, Azure SQL Data Warehouse to lze upravit podle brzy dokončení nasazení. Žádná údržba bude probíhat mimo zadanou údržbu bez předchozího upozornění.
Proveďte následující kroky, chcete-li zobrazit plán údržby, která byla použita k vašemu datovému skladu portálu.

Proveďte následující kroky, chcete-li zobrazit plán údržby, která byla použita k vašemu datovému skladu portálu.
1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2.  Vyberte datový sklad, který chcete zobrazit. 
3.  Vybrané Azure SQL Data Warehouse se otevře v okně přehledu. Plán údržby použít pro vybraný datový sklad se zobrazí pod plán údržby (preview).

![Okno přehledu](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>Další postup
[Další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) o vytváření, zobrazení a Správa výstrah pomocí Azure monitoru.
[Další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) akce Webhooku pro pravidla upozornění protokolů.
[Další informace](https://docs.microsoft.com/azure/service-health/service-health-overview) o Azure Service Health


