---
title: Jak nakonfigurovat uchovávání informací ve vašem prostředí – Azure Time Series Insights | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat uchovávání informací v prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: 524e47e5be142b720687ad48ee9407da23284bd4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605055"
---
# <a name="configuring-retention-in-time-series-insights"></a>Konfigurace uchovávání informací v přehledech časové řady

Tento článek popisuje, jak nakonfigurovat **dobu uchovávání dat** a limit úložiště **překročenchování** v Azure Time Series Insights.

## <a name="summary"></a>Souhrn

Každé prostředí Azure Time Series Insights má nastavení pro konfiguraci **doby uchovávání dat**. Hodnota se rozprostírá od 1 do 400 dnů. Data jsou odstraněna na základě kapacity úložiště prostředí nebo doby uchovávání (1-400), podle toho, co nastane dříve.

Každé prostředí Time Series Insights má další nastavení **Limit úložiště překročenchování**. Toto nastavení řídí příchozí přenos dat a vyprázdnění chování při dosažení maximální kapacity prostředí. Můžete si vybrat ze dvou chování:

- **Vyčistit stará data** (výchozí)
- **Pozastavení příchozího přenosu dat**

Podrobné informace, které chcete lépe porozumět těmto nastavením, najdete v části [Principy uchovávání informací v přehledech časových řad](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurace uchovávání dat

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyhledejte stávající prostředí Time Series Insights. Vyberte **všechny prostředky** v nabídce na levé straně portálu Azure. Vyberte vaše prostředí Time Series Insights.

1. V záhlaví **Nastavení** vyberte **Konfigurace úložiště**.

    [![V části Nastavení vyberte Konfigurace úložiště.](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. Vyberte **čas uchovávání dat (ve dnech)** pro konfiguraci uchovávání pomocí posuvníku nebo zadejte číslo do textového pole.

1. Všimněte si nastavení **Kapacita,** protože tato konfigurace má vliv na maximální množství datových událostí a celkovou kapacitu úložiště pro ukládání dat.

1. Přepíná limit úložiště překročil nastavení **chování.** Vyberte **Vymazat stará data** nebo **Pozastavit chování příchozího přenosu dat.**

    [![Pozastavit příchozí přenos dat - přijmout a uložit.](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. Projděte si dokumentaci a zjistěte potenciální rizika ztráty dat. Chcete-li konfigurovat změny, vyberte **uložit.**

## <a name="next-steps"></a>Další kroky

- Další informace naleznete [v nápovědě k principu uchovávání informací v přehledech time series .](time-series-insights-concepts-retention.md)

- Přečtěte [si, jak škálovat prostředí Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

- Další informace o [plánování prostředí](time-series-insights-environment-planning.md).
