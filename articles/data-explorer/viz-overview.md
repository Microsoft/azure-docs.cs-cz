---
title: Vizualizace dat Průzkumníka dat Azure
description: Seznamte se s různými způsoby vizualizace dat Průzkumníka dat Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 7b1c7825beb769b610d661cb9644fc3f3919d548
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139058"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Vizualizace dat pomocí Azure Data Exploreru 

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat pro data protokolu a telemetrie, která se používá k vytváření komplexních analytických řešení pro obrovské množství dat. Azure Data Explorer se integruje s různými vizualizačními nástroji, takže si můžete vizualizovat data a sdílet výsledky v celé organizaci. Tato data lze transformovat do užitečných přehledů, aby to mělo dopad na vaši firmu.

Vizualizace dat a vytváření sestav jsou důležitým krokem v procesu analýzy dat. Azure Data Explorer podporuje mnoho služeb BI, takže můžete použít tu, která nejlépe vyhovuje vašemu scénáři a rozpočtu.

## <a name="kusto-query-language-visualizations"></a>Vizualizace dotazovacího jazyka Kusto

Dotazovací jazyk [`render operator`](/azure/kusto/query/renderoperator) Kusto nabízí různé vizualizace, jako jsou tabulky, výsečové grafy a pruhové grafy, které zobrazují výsledky dotazu. Vizualizace dotazů jsou užitečné při zjišťování a prognózování anomálií, strojovém učení a další.

## <a name="power-bi"></a>Power BI

Azure Data Explorer poskytuje možnost připojení k [Power BI](https://powerbi.microsoft.com) pomocí různých metod: 

  * [Integrovaný nativní konektor Power BI](/azure/data-explorer/power-bi-connector)

  * [Dotazovat se na import z Azure Data Exploreru do Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [Dotaz SQL](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure Data Explorer poskytuje možnost připojení k [Microsoft Excelu](https://products.office.com/excel) pomocí [integrovaného nativního excelového konektoru](excel-connector.md)nebo [importu dotazu](excel-blank-query.md) z Azure Data Exploreru do Excelu.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) poskytuje plugin Azure Data Explorer, který umožňuje vizualizovat data z Průzkumníka dat Azure. Azure [Data Explorer nastavíte jako zdroj dat pro Grafana a pak vizualizovat data](/azure/data-explorer/grafana). 

## <a name="kibana"></a>Kibana

Azure Data Explorer poskytuje možnost připojení ke [Kibana (stránka Discover)](https://www.elastic.co/guide/en/kibana/6.8/discover.html) pomocí K2Bridge, open source konektoru. Azure [Data Explorer nastavíte jako zdroj dat pro Kibana a pak data vizualizujete](/azure/data-explorer/k2bridge).

## <a name="odbc-connector"></a>Konektor ODBC

Azure Data Explorer poskytuje [konektor připojení k otevřené databázi (ODBC),](connect-odbc.md) takže se k Průzkumníku dat Azure může připojit jakákoli aplikace, která podporuje ROZHRANÍ ODBC.

## <a name="tableau"></a>Tableau

Azure Data Explorer poskytuje možnost připojení k [Tableau](https://www.tableau.com) pomocí [konektoru ODBC](/azure/data-explorer/connect-odbc) a pak [vizualizovat data v Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Azure Data Explorer poskytuje možnost připojení k [Qlik](https://www.qlik.com) pomocí [konektoru ODBC](/azure/data-explorer/connect-odbc) a pak vytvořit řídicí panely Qlik Sense a vizualizovat data. Pomocí následujícího videa se můžete naučit vizualizovat data Průzkumníka dat Azure pomocí Qliku. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure Data Explorer poskytuje možnost připojení k [Sisense](https://www.sisense.com) pomocí konektoru JDBC. Azure [Data Explorer nastavíte jako zdroj dat pro Sisense a pak data vizualizujete](/azure/data-explorer/sisense).

## <a name="redash"></a>Redash

[Pomocí funkce Redash](https://redash.io/) můžete vytvářet řídicí panely a vizualizovat data. [Nastavte Azure Data Explorer jako zdroj dat pro Redash a pak vizualizovat data](/azure/data-explorer/redash).