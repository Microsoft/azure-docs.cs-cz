---
title: Postup konfigurace uchovávání ve vašem prostředí – Azure Time Series Insights | Microsoft Docs
description: Naučte se konfigurovat uchovávání v prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 468b4f7ca7b0af4abc32df5d9ef64a74154d3de1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91569399"
---
# <a name="configuring-retention-in-azure-time-series-insights-gen1"></a>Konfigurace uchovávání v Azure Time Series Insights Gen1

> [!CAUTION]
> Toto je Gen1 článek.

Tento článek popisuje, jak nakonfigurovat **dobu uchovávání dat** a **limit úložiště překročila chování** v Azure Time Series Insights.

## <a name="summary"></a>Souhrn

Každé Azure Time Series Insights prostředí má nastavení pro konfiguraci **času uchovávání dat**. Hodnota zahrnuje 1 až 400 dní. Data se odstraňují na základě kapacity úložiště prostředí nebo doby uchování (1-400), podle toho, co nastane dřív.

Každé Azure Time Series Insights prostředí má další nastavení **limit úložiště přesáhlo**. Toto nastavení řídí chování vstupu a vyprázdnění při dosažení maximální kapacity prostředí. Existují dvě možnosti chování:

- **Vyprázdnit stará data** (výchozí)
- **Pozastavit příchozí přenos dat**

Podrobné informace o tom, jak tato nastavení lépe pochopit, najdete [v Azure Time Series Insights porozumění uchovávání v](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurace uchovávání dat

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyhledejte existující Azure Time Series Insights prostředí. Vyberte **všechny prostředky** v nabídce na levé straně Azure Portal. Vyberte prostředí Azure Time Series Insights.

1. V záhlaví **Nastavení** vyberte **Konfigurace úložiště**.

    [![V části nastavení vyberte konfigurace úložiště.](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. Vyberte **dobu uchovávání dat (ve dnech)** pro konfiguraci uchovávání pomocí posuvníku nebo zadejte číslo do textového pole.

1. Všimněte si nastavení **kapacity** , protože tato konfigurace má vliv na maximální množství datových událostí a celkovou kapacitu úložiště pro ukládání dat.

1. Přepínání nastavení **chování při překročení limitu úložiště** Vyberte **Vymazat stará data** nebo **pozastavit** chování příchozího přenosu dat.

    [![Pozastavení příchozího přenosu dat: přijmout a uložit.](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. Přečtěte si dokumentaci, abyste pochopili potenciální rizika ztráty dat. Vyberte **Uložit** a proveďte konfiguraci změn.

## <a name="next-steps"></a>Další kroky

- Další informace najdete [v informacích o uchování v Azure Time Series Insights](time-series-insights-concepts-retention.md).

- Naučte [se škálovat Azure Time Series Insights prostředí](time-series-insights-how-to-scale-your-environment.md).

- Seznamte [se s plánováním svého prostředí](time-series-insights-environment-planning.md).
