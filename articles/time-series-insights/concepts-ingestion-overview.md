---
title: Přehled ingestování – Azure Time Series Insights | Microsoft Docs
description: Přečtěte si informace o přijímání dat do Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: ecee6817b58639d1b60264c818ab9517e5d133e0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049838"
---
# <a name="azure-time-series-insights-data-ingestion-overview"></a>Přehled ingestování dat Azure Time Series Insights

Prostředí Azure Time Series Insights obsahuje *modul* ingestování, který umožňuje shromažďovat, zpracovávat a ukládat data časových řad streamování. Když data dorazí do vašich zdrojů událostí, Azure Time Series Insights budou spotřebovávat a ukládat data téměř v reálném čase.

[![Přehled příjmu](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Témata pro přijímání zpráv

Následující články zahrnují zpracování dat v hloubkě, včetně osvědčených postupů, které je potřeba provést:

* Přečtěte si o [zdrojích událostí](concepts-streaming-ingestion-event-sources.md) a návodech k výběru časového razítka zdroje události.

* Kontrola podporovaných [datových typů](concepts-supported-data-types.md)

* Seznamte se s tím, jak modul pro příjem dat pro vaše vlastnosti JSON použije sadu [pravidel](./concepts-json-flattening-escaping-rules.md) pro vytvoření sloupců účtu úložiště.

* Projděte si [omezení propustnosti](concepts-streaming-throughput-limitations.md) vašeho prostředí a naplánujte požadavky na škálování.

## <a name="next-steps"></a>Další kroky

* Pokračujte na Další informace o [zdrojích událostí](concepts-streaming-ingestion-event-sources.md) pro Azure Time Series Insights prostředí. 
