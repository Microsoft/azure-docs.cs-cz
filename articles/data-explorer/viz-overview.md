---
title: Vizualizace dat Azure Průzkumník dat
description: Další informace o různých způsobech, jak můžete vizualizovat data Průzkumník dat Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 85c37b6d626fc9942f5df956e738431d2727d282
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481831"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Vizualizace dat pomocí Průzkumníka dat služby Azure 

Průzkumník služby Azure Data je služba pro zkoumání dat rychlé a vysoce škálovatelné pro data protokolů a telemetrická data, která se používá k vytvoření komplexní analytická řešení pro velké objemy dat. Průzkumník služby Azure Data se integruje s různé nástroje pro vizualizace, takže můžete vizualizovat data a sdílet výsledky napříč vaší organizací. Je možné transformovat tato data na užitečné přehledy, aby se dopad na vaši firmu.

Vizualizace dat a vytváření sestav je kritickým krokem v procesu analýzy data. Průzkumník služby Azure Data podporuje mnoho služeb BI, abyste mohli používat ten, který nejlépe odpovídá vaší situaci a rozpočet.

* Vizualizace Průzkumník dat Azure: Pomocí dotazovacího jazyka Kusto [ `render operator` ](/azure/kusto/query/renderoperator) nabízí různých typů vizualizace ke znázornění výsledků dotazu. Vizualizace dotazů jsou užitečné při detekci anomálií a Prognózování, strojové učení a další.

* [Power BI](https://powerbi.microsoft.com): Průzkumník dat Azure poskytuje možnost připojení k Power BI pomocí různých metod: 

  * [Integrované nativní konektor Power BI](/azure/data-explorer/power-bi-connector)

  * [Import dotazu z Průzkumníka služby Azure Data do Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [Příkaz jazyka SQL](/azure/data-explorer/power-bi-sql-query).

* [Microsoft Excel](https://products.office.com/excel): Průzkumník služby Azure Data poskytuje možnosti pro připojení k Excelu pomocí integrovaného konektoru nativní aplikace Excel nebo importovat dotaz z Průzkumníka služby Azure Data do aplikace Excel.

* [Grafana](https://grafana.com): Grafana poskytuje modulu plug-in Průzkumník dat Azure, která umožňuje vizualizovat data z Průzkumníka služby Azure Data. Můžete [nastavení Průzkumníka služby Azure Data jako zdroj dat pro Grafana a pak data vizualizovat](/azure/data-explorer/grafana)

* [Sisense](https://www.sisense.com): Průzkumník služby Azure Data obsahuje funkce pro připojení k Sisense pomocí konektoru rozhraní JDBC. Můžete [nastavení Průzkumníka služby Azure Data jako zdroj dat pro Sisense a pak data vizualizovat](/azure/data-explorer/sisense).

* [Tableau](https://www.tableau.com): Průzkumník služby Azure Data poskytuje možnosti pro připojení k Tableau pomocí [konektor ODBC a vizualizace dat v Tableau](/azure/data-explorer/connect-odbc).

* [Qlik](https://www.qlik.com): Průzkumník služby Azure Data poskytuje možnosti pro připojení k Qlik pomocí [konektor ODBC](/azure/data-explorer/connect-odbc).