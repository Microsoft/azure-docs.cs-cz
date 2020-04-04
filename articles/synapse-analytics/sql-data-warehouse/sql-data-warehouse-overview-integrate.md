---
title: Vytváření integrovaných řešení
description: Nástroje řešení a partneři, kteří se integrují s fondem Synapse SQL.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2f6e091b6e0285bea5fef9e4d0be40faec936c6b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633146"
---
# <a name="integrate-other-services-with-a-sql-analytics-data-warehouse"></a>Integrace dalších služeb s datovým skladem SQL Analytics

Funkce SQL Analytics v rámci Azure Synapse Analytics umožňuje uživatelům integrovat se s mnoha dalšími službami v Azure. Pomocí sql analytics můžete vytvořit datový sklad prostřednictvím prostředku fondu SQL, který pak může využívat několik dalších služeb, z nichž některé zahrnují:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Další informace týkající se integračních služeb v rámci Azure najděte článek [Partneři integrace.](sql-data-warehouse-partner-data-integration.md)

## <a name="power-bi"></a>Power BI

Integrace Power BI umožňuje kombinovat výpočetní výkon datového skladu s dynamickým vytvářením sestav a vizualizací Power BI. Integrace Power BI v současné době zahrnuje:

* **Přímé připojení**: Pokročilejší připojení s logickým pushdown proti datovému skladu zřízeného pomocí fondu SQL. Pushdown poskytuje rychlejší analýzu ve větším měřítku.
* **Otevřít v Power BI:** Tlačítko Otevřít v Power BI předá informace o instanci Power BI pro zjednodušený způsob připojení.

Další informace najdete [v tématu Integrace s Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)nebo dokumentace k Power [BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory poskytuje uživatelům spravovanou platformu pro vytváření složitých kanálů extrakce a načítání. Integrace fondu SQL s Azure Data Factory zahrnuje:

* **Uložené procedury**: Orchestrate provádění uložené procedury.
* **Kopie**: Přesunutí dat do fondu SQL pomocí adf. Tato operace můžete použít ADF standardní mechanismus pohybu dat nebo PolyBase pod kryty.

Další informace najdete [v tématu Integrace s Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning je plně spravovaná analytická služba, která umožňuje vytvářet složité modely pomocí velké sady prediktivních nástrojů. Fond SQL je podporován jako zdroj i cíl pro tyto modely a má následující funkce:

* **Číst data:** Jednotky modely ve velkém měřítku pomocí T-SQL proti fondu SQL.
* **Zapsat data:** Potvrzení změn z libovolného modelu zpět do fondu SQL.

Další informace najdete [v tématu Integrace s Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Azure Stream Analytics je komplexní, plně spravovaná infrastruktura pro zpracování a využití dat událostí generovaných z Centra událostí Azure.  Integrace s fondem SQL umožňuje efektivní zpracování a ukládání streamovaných dat spolu s relačními daty, což umožňuje hlubší a pokročilejší analýzu.  

* **Výstup úlohy:** Odesílejte výstup z úloh Stream Analytics přímo do fondu SQL.

Další informace najdete [v tématu Integrace s Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).
