---
title: Postup konfigurace uchovávání informací ve vašem prostředí Azure časové řady Insights | Microsoft Docs
description: Tento článek popisuje postup konfigurace uchovávání informací ve vašem prostředí Statistika Azure časové řady.
ms.service: time-series-insights
services: time-series-insights
author: anshan
ms.author: anshan
manager: kfile
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 9a236ba500af36e8fb1c8c089389ffd74b9d2abb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653915"
---
# <a name="configuring-retention-in-time-series-insights"></a>Konfigurace uchování v přehledy časové řady
Tento článek popisuje postup konfigurace **doby uchování dat** a **chování k překročení limitu úložiště** v Azure časové řady přehledy.

Každé prostředí časové řady přehledy (TSI) má nastavení konfigurace **doby uchování dat**. Rozdělena na hodnotu od 1 do 400 dnů. Data jsou odstraněna podle prostředí úložiště kapacity nebo uchovávání doba trvání (1-400), nastane dříve.

Každé prostředí TSI má další nastavení **chování k překročení limitu úložiště**. Toto nastavení řídí chování příjem příchozích dat a mazání po dosažení maximální kapacity prostředí. Existují dvě chování můžete vybrat ze:
- **Vymazat stará data** (výchozí)  
- **Pozastavení příjem příchozích dat**

Podrobné informace pro lepší pochopení těchto nastavení najdete v tématu [Principy uchování v časové řady přehledy](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurace uchovávání dat

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte existující časové řady Statistika prostředí. Vyberte **všechny prostředky** v nabídce na levé straně na portálu Azure. Vyberte vaše prostředí Time Series Insights.

3. V části **nastavení** záhlaví, vyberte **konfigurace**.

4. Vyberte **doby uchování dat** konfigurovat uchování pomocí posuvníku nebo zadejte číslo do textového pole.

5. Poznámka: **kapacity** nastavení, protože tato konfigurace má dopad na maximální množství dat událostí a kapacity celkové úložiště pro ukládání dat. 

6. Přepnutí **chování k překročení limitu úložiště** nastavení. Vyberte **vymazat stará data** nebo **pozastavit příjem příchozích dat** chování.

7. Vyberte **Uložit** změny konfigurace.

## <a name="next-steps"></a>Další postup
Další informace najdete v tématu [Principy uchování v časové řady přehledy](time-series-insights-concepts-retention.md).
