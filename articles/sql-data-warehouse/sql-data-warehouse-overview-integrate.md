---
title: Sestavení integrovaných řešení
description: Nástroje řešení a partneři, kteří se integrují s datovým skladem zřízeným pomocí SQL Analytics.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 8b38b50401c50ecfb9cd37c53ad013ca05e126c0
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153294"
---
# <a name="integrate-other-services-with-a-sql-analytics-data-warehouse"></a>Integrace dalších služeb s datovým skladem SQL Analytics 
Schopnost SQL Analytics v rámci služby Azure synapse Analytics umožňuje uživatelům integraci s mnoha ostatními službami v Azure. Pomocí SQL Analytics můžete vytvořit datový sklad prostřednictvím svého prostředku fondu SQL, který pak může využívat několik dalších služeb, z nichž některé zahrnují:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Další informace o integračních službách v Azure najdete v článku o [partnerech pro integraci](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
Power BI Integration umožňuje kombinovat výpočetní výkon datového skladu s dynamickým generováním sestav a vizualizací Power BI. Power BI integrace v současné době zahrnuje:

* **Přímé připojení**: pokročilejší připojení s logickým přenosem na datový sklad zřízený pomocí fondu SQL. Přenos přenosů zajišťuje rychlejší analýzu ve větším měřítku.
* **Otevřít v Power BI**: tlačítko otevřít v Power BI předává informace o instanci Power BI pro zjednodušený způsob připojení.

Další informace najdete v tématu věnovaném [integraci s Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)nebo v [dokumentaci k Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory poskytuje uživatelům spravovanou platformu pro vytváření komplexních extrakcí a načítání kanálů. Integrace fondu SQL s Azure Data Factory zahrnuje:

* **Uložené procedury**: orchestrace provádění uložených procedur.
* **Kopírovat**: k přesunu dat do fondu SQL použijte ADF. Tato operace může v rámci pokrývání použít standardní mechanismus pro přesun dat nebo základ. 

Další informace najdete v tématu věnovaném [integraci s Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning je plně spravovaná analytická služba, která umožňuje vytvářet komplikované modely pomocí velké sady prediktivních nástrojů. Fond SQL je podporován jako zdroj i cíl pro tyto modely a má následující funkce:

* **Číst data:** Škálujte modely jednotek pomocí T-SQL s fondem SQL.
* **Zapisovat data:** Potvrďte změny ze všech modelů zpátky do fondu SQL.

Další informace najdete v tématu věnovaném [integraci s Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics je složitá plně spravovaná infrastruktura pro zpracování a využívání dat událostí generovaných z centra událostí Azure.  Integrace s fondem SQL umožňuje efektivně zpracovávat a ukládat streamovaná data společně s relačními daty, což umožňuje hlubší a pokročilejší analýzu.  

* **Výstup úlohy:** Odeslání výstupu z Stream Analytics úloh přímo do fondu SQL.

Další informace najdete v tématu věnovaném [integraci s Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).


