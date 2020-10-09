---
title: Sestavení integrovaných řešení
description: Nástroje řešení a partneři, kteří se integrují s synapse fondem SQL
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 16cfdfb475ce21ed4b51dc9140e59df701363f27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201000"
---
# <a name="integrate-other-services-with-a-synapse-sql-pool-data-warehouse"></a>Integrace dalších služeb s synapse fondem SQL (datový sklad)

Schopnost fondu SQL ve službě Azure synapse Analytics umožňuje uživatelům integraci s mnoha ostatními službami v Azure. Pomocí synapse SQL můžete vytvořit datový sklad prostřednictvím svého prostředku fondu SQL, který pak může využívat několik dalších služeb, z nichž některé zahrnují:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Další informace o integračních službách v rámci Azure najdete v článku o [partnerech pro integraci](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI

Power BI Integration umožňuje kombinovat výpočetní výkon datového skladu s dynamickým generováním sestav a vizualizací Power BI. Integrace Power BI v současné době zahrnuje:

* **Přímé připojení**: pokročilejší připojení s logickým přenosem na datový sklad zřízený pomocí fondu SQL. Zásobník zajišťuje rychlejší analýzy ve větším měřítku.
* **Otevřít v Power BI**: tlačítko otevřít v Power BI předává informace o instanci Power BI pro zjednodušený způsob připojení.

Další informace najdete v tématu věnovaném [integraci s Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)nebo v [dokumentaci k Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory poskytuje uživatelům spravovanou platformu pro vytváření komplexních extrakcí a načítání kanálů. Integrace fondu SQL s Azure Data Factory zahrnuje:

* **Uložené procedury**: orchestrace provádění uložených procedur.
* **Kopírovat**: k přesunu dat do fondu SQL použijte ADF. Tato operace může v rámci pokrývání použít standardní mechanismus pro přesun dat nebo základ.

Další informace najdete v tématu věnovaném [integraci s Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning je plně spravovaná analytická služba, která umožňuje vytvářet komplikované modely pomocí velké sady prediktivních nástrojů. Fond SQL je podporován jako zdroj i cíl pro tyto modely a má následující funkce:

* **Číst data:** Škálujte modely jednotek pomocí T-SQL s fondem SQL.
* **Zapisovat data:** Potvrďte změny ze všech modelů zpátky do fondu SQL.

Další informace najdete v tématu věnovaném [integraci s Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Azure Stream Analytics je složitá plně spravovaná infrastruktura pro zpracování a využívání dat událostí generovaných z centra událostí Azure.  Integrace s fondem SQL umožňuje efektivně zpracovávat a ukládat streamovaná data společně s relačními daty, což umožňuje hlubší a pokročilejší analýzu.  

* **Výstup úlohy:** Odeslání výstupu z Stream Analytics úloh přímo do fondu SQL.

Další informace najdete v tématu věnovaném [integraci s Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).
