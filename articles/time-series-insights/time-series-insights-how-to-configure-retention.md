---
title: Postup konfigurace uchovávání v prostředí Azure Time Series Insights | Microsoft Docs
description: Tento článek popisuje, jak nakonfigurovat uchovávání v prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: e5cc1489af1dce3a9a57b96a3240c63e0395c33a
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947226"
---
# <a name="configuring-retention-in-time-series-insights"></a>Konfigurace uchovávání v Time Series Insights

Tento článek popisuje, jak nakonfigurovat **dobu uchovávání dat** a **limit úložiště překročila chování** v Azure Time Series Insights.

## <a name="summary"></a>Souhrn

Každé prostředí Time Series Insights (TSI) má nastavení pro konfiguraci **času uchovávání dat**. Hodnota zahrnuje 1 až 400 dní. Data se odstraňují na základě kapacity úložiště prostředí nebo doby uchování (1-400), podle toho, co nastane dřív.

Každé prostředí TSI má další nastavení **limit úložiště přesáhlo**. Toto nastavení řídí chování vstupu a vyprázdnění při dosažení maximální kapacity prostředí. Existují dvě možnosti chování:

- **Vyprázdnit stará data** výchozí
- **Pozastavit příchozí přenos dat**

Podrobné informace o tom, jak tato nastavení lépe pochopit, najdete [v Time Series Insights porozumění uchovávání v](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurace uchovávání dat

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyhledejte existující prostředí Time Series Insights. Vyberte **všechny prostředky** v nabídce na levé straně Azure Portal. Vyberte vaše prostředí Time Series Insights.

1. V záhlaví **Nastavení** vyberte **Konfigurovat**.

1. Vyberte **dobu uchovávání dat** pro konfiguraci uchovávání pomocí posuvníku nebo zadejte číslo do textového pole.

1. Všimněte si nastavení **kapacity** , protože tato konfigurace má vliv na maximální množství datových událostí a celkovou kapacitu úložiště pro ukládání dat.

1. Přepínání nastavení **chování při překročení limitu úložiště** Vyberte **Vymazat stará data** nebo **pozastavit** chování příchozího přenosu dat.

1. Vyberte **Uložit** a proveďte konfiguraci změn.

## <a name="next-steps"></a>Další postup

- Další informace najdete [v informacích o uchování v Time Series Insights](time-series-insights-concepts-retention.md).