---
title: Vizualizace dat Azure Průzkumník dat
description: Další informace o různých způsobech, jak můžete vizualizovat data Průzkumník dat Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: d1c73d8eb65ed5d67d5250b4a3bca3b80450001e
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536721"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Vizualizace dat pomocí Průzkumníka dat služby Azure 

Průzkumník služby Azure Data je služba pro zkoumání dat rychlé a vysoce škálovatelné pro data protokolů a telemetrická data, která se používá k vytvoření komplexní analytická řešení pro velké objemy dat. Průzkumník služby Azure Data se integruje s různé nástroje pro vizualizace, takže můžete vizualizovat data a sdílet výsledky napříč vaší organizací. Je možné transformovat tato data na užitečné přehledy, aby se dopad na vaši firmu.

Vizualizace dat a vytváření sestav je kritickým krokem v procesu analýzy data. Průzkumník služby Azure Data podporuje mnoho služeb BI, abyste mohli používat ten, který nejlépe odpovídá vaší situaci a rozpočet.

## <a name="kusto-query-language-visualizations"></a>Vizualizace jazyk dotaz Kusto

Dotazovací jazyk Kusto [ `render operator` ](/azure/kusto/query/renderoperator) nabízí různé vizualizace, jako například tabulky, výsečové grafy a pruhové grafy ke znázornění výsledků dotazu. Vizualizace dotazů jsou užitečné při detekci anomálií a Prognózování, strojové učení a další.

## <a name="power-bi"></a>Power BI

Průzkumník služby Azure Data poskytuje možnosti pro připojení k [Power BI](https://powerbi.microsoft.com) pomocí různých metod: 

  * [Integrované nativní konektor Power BI](/azure/data-explorer/power-bi-connector)

  * [Import dotazu z Průzkumníka služby Azure Data do Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [Dotaz SQL](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Průzkumník služby Azure Data poskytuje možnosti pro připojení k [aplikace Microsoft Excel](https://products.office.com/excel) pomocí předdefinovaných nativní konektor Excelový nebo importujete dotaz z Průzkumníka služby Azure Data do aplikace Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) poskytuje modulu plug-in Průzkumník dat Azure, která umožňuje vizualizovat data z Průzkumníka služby Azure Data. Můžete [nastavení Průzkumníka služby Azure Data jako zdroj dat pro Grafana a pak data vizualizovat](/azure/data-explorer/grafana). 

## <a name="odbc-connector"></a>Konektor ODBC

Průzkumník služby Azure Data poskytuje [připojení ODBC (Open Database) konektoru](connect-odbc.md) tak můžete připojit každou aplikaci, která podporuje rozhraní ODBC do Průzkumníku dat Azure.

## <a name="tableau"></a>Tableau

Průzkumník služby Azure Data poskytuje možnosti pro připojení k [Tableau](https://www.tableau.com) pomocí [konektor ODBC](/azure/data-explorer/connect-odbc) a potom [data vizualizovat v Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Průzkumník služby Azure Data poskytuje možnosti pro připojení k [Qlik](https://www.qlik.com) pomocí [konektor ODBC](/azure/data-explorer/connect-odbc) a Qlik Sense vytváření řídicích panelů a vizualizace. Pomocí následující video, další k vizualizaci dat Průzkumník dat Azure s Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Průzkumník služby Azure Data poskytuje možnosti pro připojení k [Sisense](https://www.sisense.com) pomocí konektoru rozhraní JDBC. Můžete [nastavení Průzkumníka služby Azure Data jako zdroj dat pro Sisense a pak data vizualizovat](/azure/data-explorer/sisense).