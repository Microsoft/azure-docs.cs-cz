---
title: Vizualizace dat Průzkumník dat Azure
description: Seznamte se s různými způsoby, jak můžete vizualizovat data Průzkumník dat Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 32ccc5ad236c87d3609798a8432db14ee440d067
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064559"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Vizualizace dat s využitím Azure Průzkumník dat 

Azure Průzkumník dat je rychlá a vysoce škálovatelná služba průzkumu dat pro data protokolů a telemetrie, která slouží k vytváření komplexních analytických řešení pro velké objemy dat. Azure Průzkumník dat se integruje s různými nástroji vizualizace, takže můžete vizualizovat data a sdílet výsledky napříč vaší organizací. Tato data je možné transformovat na užitečné poznatky, abyste měli dopad na vaši firmu.

Vizualizace dat a vytváření sestav je důležitým krokem v procesu analýzy dat. Azure Průzkumník dat podporuje mnoho služeb BI, takže můžete použít ten, který nejlépe vyhovuje vašemu scénáři a rozpočtu.

## <a name="kusto-query-language-visualizations"></a>Vizualizace dotazovacího jazyka Kusto

Dotazovací jazyk Kusto [`render operator`](/azure/kusto/query/renderoperator) nabízí různé vizualizace, jako jsou tabulky, výsečové grafy a pruhové grafy, a znázorňuje tak výsledky dotazu. Vizualizace dotazů jsou užitečné při detekci anomálií a předpovědi, strojovém učení a dalších.

## <a name="power-bi"></a>Power BI

Azure Průzkumník dat poskytuje možnost připojení k [Power BI](https://powerbi.microsoft.com) pomocí různých metod: 

  * [Integrovaný nativní konektor Power BI](/azure/data-explorer/power-bi-connector)

  * [Import dotazů z Azure Průzkumník dat do Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [Dotaz SQL](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure Průzkumník dat poskytuje možnost připojit se k [aplikaci Microsoft Excel](https://products.office.com/excel) pomocí [integrovaného nativního konektoru aplikace Excel](excel-connector.md)nebo [importovat dotaz](excel-blank-query.md) z Azure Průzkumník dat do Excelu.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) poskytuje modul plug-in Azure Průzkumník dat, který umožňuje vizualizovat data z Azure Průzkumník dat. Můžete [nastavit Azure Průzkumník dat jako zdroj dat pro Grafana a potom vizualizovat data](/azure/data-explorer/grafana). 

## <a name="odbc-connector"></a>Konektor ODBC

Azure Průzkumník dat poskytuje [konektor ODBC (Open Database Connectivity)](connect-odbc.md) , aby se všechny aplikace, které podporují rozhraní ODBC, mohly připojit k Azure Průzkumník dat.

## <a name="tableau"></a>Tableau

Azure Průzkumník dat poskytuje možnost připojit se k [Tableau](https://www.tableau.com) pomocí [konektoru ODBC](/azure/data-explorer/connect-odbc) a pak [vizualizovat data v Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Azure Průzkumník dat poskytuje možnost připojit se k [Qlik](https://www.qlik.com) pomocí [konektoru ODBC](/azure/data-explorer/connect-odbc) a pak vytvořit řídicí panely a vizualizace dat Qlik. Pomocí následujícího videa se můžete seznámit s tím, jak vizualizovat data Azure Průzkumník dat pomocí Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure Průzkumník dat poskytuje možnost připojit se k [Sisense](https://www.sisense.com) pomocí konektoru JDBC. Můžete [nastavit Azure Průzkumník dat jako zdroj dat pro Sisense a potom vizualizovat data](/azure/data-explorer/sisense).

## <a name="redash"></a>Redash

K vytváření řídicích panelů a vizualizace dat můžete použít [pomlčku](https://redash.io/) . [Nastavte Azure Průzkumník dat jako zdroj dat pro přerušování a pak Vizualizujte data](/azure/data-explorer/redash).