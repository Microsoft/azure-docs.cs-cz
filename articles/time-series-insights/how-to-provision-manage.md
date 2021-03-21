---
title: Správa prostředí generace 2 – Azure Time Series | Microsoft Docs
description: Naučte se spravovat prostředí Azure Time Series Insights Gen 2.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/15/2020
ms.custom: seodec18
ms.openlocfilehash: cbd28bdf5318bdaf932447f5d1f936d2c614a7f3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461891"
---
# <a name="manage-azure-time-series-insights-gen2"></a>Správa Azure Time Series Insights Gen2

Po vytvoření prostředí Azure Time Series Insights Gen2 pomocí [Azure CLI](./how-to-create-environment-using-cli.md) nebo [Azure Portal](./how-to-create-environment-using-portal.md)můžete upravit zásady přístupu a další atributy prostředí tak, aby vyhovovaly vašim obchodním potřebám.

## <a name="manage-the-environment"></a>Správa prostředí

Prostředí Azure Time Series Insights Gen2 můžete spravovat pomocí [Azure Portal](https://portal.azure.com/). Existuje několik klíčových rozdílů mezi prostředím Gen2 a prostředími Gen1 S1 nebo Gen1 S2, které vám pamatujte při správě prostředí prostřednictvím Azure Portal:

* V podokně s **přehledem** Azure Portal Gen2 jsou tyto změny:

  * Kapacita se odebere, protože se nevztahuje na Gen2 prostředí.
  * Přidá se vlastnost **ID časové řady** . Určuje, jak jsou data rozdělená na oddíly.
  * Referenční sady dat se odeberou.
  * Zobrazovaná adresa URL vás přesměruje do [průzkumníka Azure Time Series Insights](./concepts-ux-panels.md).
  * Zobrazí se název vašeho účtu Azure Storage.

* Odeberou se podokno **konfigurace** Azure Portal, protože jednotky škálování se nevztahují na Azure Time Series Insights Gen2 prostředí. Můžete ale použít **konfiguraci úložiště** a nakonfigurovat nově zavedený obchod za tepla.

* Podokno **referenčních dat** Azure Portal se v Azure Time Series Insights Gen2 odebralo, protože koncept referenčních dat byl nahrazen [modelem Time Series model (TSM)](./concepts-model-overview.md).

:::image type="content" source="media/v2-update-manage/create-and-manage-overview-confirm.png" alt-text="Azure Time Series Insights prostředí Gen2 v Azure Portal":::

## <a name="next-steps"></a>Další kroky

* Projděte si seznam [osvědčených postupů pro přijímání datových proudů](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)
* Vysvětlení, jak [diagnostikovat a řešit potíže](./how-to-diagnose-troubleshoot.md)
