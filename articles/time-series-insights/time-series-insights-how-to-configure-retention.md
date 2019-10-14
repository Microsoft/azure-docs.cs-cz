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
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 9986f57b05032c1e12769d59781e8b7aca443abb
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298975"
---
# <a name="configuring-retention-in-time-series-insights"></a>Konfigurace uchovávání v Time Series Insights

Tento článek popisuje, jak nakonfigurovat **dobu uchovávání dat** a **limit úložiště překročila chování** v Azure Time Series Insights.

## <a name="summary"></a>Souhrn

Každé Azure Time Series Insights prostředí má nastavení pro konfiguraci **času uchovávání dat**. Hodnota zahrnuje 1 až 400 dní. Data se odstraňují na základě kapacity úložiště prostředí nebo doby uchování (1-400), podle toho, co nastane dřív.

Každé prostředí TSI má další nastavení **limit úložiště přesáhlo**. Toto nastavení řídí chování vstupu a vyprázdnění při dosažení maximální kapacity prostředí. Existují dvě možnosti chování:

- **Vyprázdnit stará data** (výchozí)
- **Pozastavit příchozí přenos dat**

Podrobné informace o tom, jak tato nastavení lépe pochopit, najdete [v Time Series Insights porozumění uchovávání v](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurace uchovávání dat

1. Přihlaste se k [Azure Portal](https://portal.azure.com).

1. Vyhledejte existující Time Series Insights prostředí. Vyberte **všechny prostředky** v nabídce na levé straně Azure Portal. Vyberte prostředí Time Series Insights.

1. V záhlaví **Nastavení** vyberte **Konfigurovat**.

    [@no__t – nastavení 1Select a pak konfigurace](media/data-retention/1-configure-data-retention.png)](media/data-retention/1-configure-data-retention.png#lightbox)

1. Vyberte **dobu uchovávání dat (ve dnech)** pro konfiguraci uchovávání pomocí posuvníku nebo zadejte číslo do textového pole.

1. Všimněte si nastavení **kapacity** , protože tato konfigurace má vliv na maximální množství datových událostí a celkovou kapacitu úložiště pro ukládání dat.

1. Přepínání nastavení **chování při překročení limitu úložiště** Vyberte **Vymazat stará data** nebo **pozastavit** chování příchozího přenosu dat.

    [@no__t – přijetí a uložení 1Data uchování.](media/data-retention/2-accept-and-save.png)](media/data-retention/2-accept-and-save.png#lightbox)

1. Přijměte zaškrtávací políčko s oznámením, že jste si přečetli dokumentaci a rozumíte potenciálním rizikům ztráty dat. Vyberte **Uložit** a proveďte konfiguraci změn.

## <a name="next-steps"></a>Další kroky

- Další informace najdete [v informacích o uchování v Time Series Insights](time-series-insights-concepts-retention.md).