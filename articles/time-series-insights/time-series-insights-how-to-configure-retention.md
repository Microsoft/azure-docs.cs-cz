---
title: Konfigurace uchovávání informací ve vašem prostředí Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje, jak nakonfigurovat uchovávání dat ve vašem prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: a87a12e08e78cf8a7f0b4d2af3e8029b233cf02b
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629884"
---
# <a name="configuring-retention-in-time-series-insights"></a>Konfigurace uchovávání naleznete v Time Series Insights
Tento článek popisuje, jak nakonfigurovat **doby uchování dat** a **překročil limit úložiště chování** v Azure Time Series Insights.

Každé prostředí Time Series Insights (TSI) má nastavení můžete konfigurovat **doby uchování dat**. Hodnota sahá od 1 do 400 dnů. Data se odstraní podle prostředí kapacity nebo uchovávání doba trvání úložiště (1 až 400), podle toho, co nastane dřív.

Každé prostředí TSI má další nastavení **překročil limit úložiště chování**. Toto nastavení řídí chování příchozího přenosu dat a mazání po dosažení maximální kapacity prostředí. Existují dva chování lze vybírat:
- **Vymazat stará data** (výchozí)  
- **Pozastavit příchozího přenosu dat**

Podrobné informace pro lepší pochopení těchto nastavení najdete v tématu [vysvětlení uchovávání informací v Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurace uchovávání dat

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte existující prostředí Time Series Insights. Vyberte **všechny prostředky** v nabídce na levé straně webu Azure portal. Vyberte vaše prostředí Time Series Insights.

3. V části **nastavení** záhlaví, vyberte **konfigurovat**.

4. Vyberte **doby uchování dat** konfigurace uchovávání pomocí posuvníku nebo zadejte číslo v textovém poli.

5. Poznámka: **kapacity** nastavení, protože tato konfigurace má vliv na maximální množství dat událostí a kapacita celkový úložiště pro ukládání dat. 

6. Přepnout **překročil limit úložiště chování** nastavení. Vyberte **vymazat stará data** nebo **pozastavit příchozího přenosu dat** chování.

7. Vyberte **Uložit** změny konfigurace.

## <a name="next-steps"></a>Další postup
Další informace najdete v tématu [vysvětlení uchovávání informací v Time Series Insights](time-series-insights-concepts-retention.md).
