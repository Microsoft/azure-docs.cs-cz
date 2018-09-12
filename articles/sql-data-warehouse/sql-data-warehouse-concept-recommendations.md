---
title: Doporučení – koncepty služby SQL Data Warehouse | Dokumentace Microsoftu
description: Další informace o doporučení SQL Data Warehouse a jak se generují
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/27/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 57bce631a570f549d46a9b0beefcb5adce4decfc
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44380110"
---
# <a name="sql-data-warehouse-recommendations"></a>Doporučení pro SQL Data Warehouse

Tento článek popisuje doporučení obsluhuje SQL Data Warehouse pomocí Azure Advisoru.  

SQL Data Warehouse poskytuje doporučení, aby váš datový sklad je trvale optimalizován pro výkon. Datový sklad doporučení jsou úzce integrovaná s [Azure Advisoru](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) , kde přinášejí osvědčené postupy přímo v rámci [webu Azure portal](https://aka.ms/Azureadvisor). SQL Data Warehouse analyzuje aktuální stav služby data warehouse, shromažďuje telemetrická data a zařízení Surface doporučení pro aktivní úlohy na denní bázi. Scénáře podporovaná data warehouse doporučení jsou popsány níže spolu s jak použít doporučené akce.

Pokud máte nějakou zpětnou vazbu na Poradce pro SQL Data Warehouse nebo setkáte s problémy, kontaktujte [ sqldwadvisor@service.microsoft.com ](mailto:sqldwadvisor@service.microsoft.com).   

Klikněte na tlačítko [tady](https://aka.ms/Azureadvisor) zkontrolujte svá doporučení ještě dnes! Aktuálně tato funkce se vztahuje na Gen2 pouze datové sklady. 

## <a name="data-skew"></a>Nerovnoměrná distribuce dat

Nerovnoměrná distribuce dat může způsobit další data pohybu nebo prostředků problémová místa při spuštění vaší úlohy. Následující dokumentace popisuje zobrazit identifikovat Nerovnoměrná distribuce dat a zabránit ho tak, že vyberete optimální distribučního klíče.

- [Zjistit a odebrat Nerovnoměrná distribuce](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Ne nebo jsou zastaralé statistiky

Budete mít statistiky neoptimální může vážně ovlivnit výkon dotazů, protože to může způsobit optimalizátoru dotazů SQL Data Warehouse ke generování plány neoptimální dotazů. Následující dokumentace popisuje osvědčené postupy týkající se vytváření a aktualizaci statistiky:

- [Vytváření a aktualizaci statistik tabulek](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Pokud chcete zobrazit seznam ovlivněných tabulek pomocí těchto doporučení, spusťte následující příkaz [skriptu T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Advisor nepřetržitě spouští stejný skript T-SQL ke generování těchto doporučení.
