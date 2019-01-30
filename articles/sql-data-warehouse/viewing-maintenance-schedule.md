---
title: Plány údržby Azure (preview) | Dokumentace Microsoftu
description: Plánování údržby umožňuje zákazníkům naplánovat události nezbytné plánované údržby, které služba Azure SQL Data warehouse používá k zavedení nových funkcí, upgrady a opravy.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/27/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 61537a4cb8ca1e6e98f2fa04959861eb03480fe9
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247251"
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
- [Další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) vytváření a Správa skupin akcí.
- [Další informace](https://docs.microsoft.com/azure/service-health/service-health-overview) o Azure Service Health.


