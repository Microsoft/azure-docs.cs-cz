---
title: Přehled ingestování – Azure Time Series Insights Gen2 | Microsoft Docs
description: Přečtěte si informace o přijímání dat do Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: b688bbf454b3df9f6ae368c66a62657a5522d720
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505446"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Přehled příjmu dat Azure Time Series Insights Gen2

Prostředí Azure Time Series Insights Gen2 obsahuje *modul* ingestování, který umožňuje shromažďovat, zpracovávat a ukládat data časových řad streamování. Když se data dostanou do vašich zdrojů událostí, Azure Time Series Insights Gen2 spotřebuje a uloží vaše data téměř v reálném čase.

[![Přehled příjmu dat](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Témata pro přijímání zpráv

Následující články zahrnují zpracování dat v hloubkě, včetně osvědčených postupů, které je potřeba provést:

* Přečtěte si o [zdrojích událostí](./concepts-streaming-ingestion-event-sources.md) a návodech k výběru časového razítka zdroje události.

* Kontrola podporovaných [datových typů](./concepts-supported-data-types.md)

* Seznamte se s tím, jak modul pro příjem dat pro vaše vlastnosti JSON použije sadu [pravidel](./concepts-json-flattening-escaping-rules.md) pro vytvoření sloupců účtu úložiště.

* Projděte si [omezení propustnosti](./concepts-streaming-ingress-throughput-limits.md) vašeho prostředí a naplánujte požadavky na škálování.

## <a name="next-steps"></a>Další kroky

* Pokračujte na Další informace o [zdrojích událostí](./concepts-streaming-ingestion-event-sources.md) pro prostředí Azure Time Series Insights Gen2.
