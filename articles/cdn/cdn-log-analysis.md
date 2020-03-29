---
title: Analýza vzorců využití Azure CDN | Dokumenty společnosti Microsoft
description: Tento článek popisuje různé typy sestav analýzy, které jsou k dispozici pro produkty Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: magattus
ms.openlocfilehash: 238dea3c136daf13d3db7be41bed103a0cbf7636
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593689"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analýza vzorů využití Azure CDN

Po povolení CDN pro vaši aplikaci můžete sledovat využití CDN, zkontrolovat stav doručení a řešit potenciální problémy. Azure CDN poskytuje tyto funkce následujícími způsoby: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Základní analýzy prostřednictvím diagnostických protokolů Azure

Základní analýza je k dispozici pro koncové body CDN pro všechny cenové úrovně. Protokoly diagnostiky Azure umožňují exportování základních analýz do úložiště Azure, center událostí nebo protokolů Azure Monitoru. Protokoly Azure Monitor nabízí řešení s grafy, které jsou uživatelsky konfigurovatelné a přizpůsobitelné. Další informace o diagnostických protokolech Azure najdete v [tématu Diagnostické protokoly Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon základní zprávy

Jako uživatel Azure CDN s **Azure CDN Standard od Verizonu** nebo **Azure CDN Premium z** profilu Verizon, můžete zobrazit základní sestavy Verizon na doplňkovém portálu Verizon. Základní sestavy Verizonu jsou dostupné prostřednictvím možnosti **Spravovat** z portálu Azure a nabízejí celou řadu grafů a zobrazení. Další informace naleznete [v tématu Core Reports from Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Vlastní sestavy společnosti Verizon

Jako uživatel Azure CDN s **Azure CDN Standard od Verizonu** nebo **Azure CDN Premium z** profilu Verizon, můžete zobrazit verizon vlastní sestavy na doplňkovém portálu Verizon. Vlastní sestavy Verizonu jsou přístupné prostřednictvím možnosti **Spravovat** z webu Azure Portal. Stránka vlastní sestavy Verizon zobrazuje počet přístupů nebo dat přenesených pro každou hraniční CName patřící do profilu Azure CDN. Data mohou být seskupena podle kódu odpovědi HTTP nebo stavu mezipaměti za jakékoli časové období. Další informace naleznete [v tématu Vlastní sestavy společnosti Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Sestavy Azure CDN Premium od společnosti Verizon

S **Azure CDN Premium od verizonu**máte také přístup k následujícím sestavům:
   * [Rozšířené sestavy HTTP](cdn-advanced-http-reports.md)
   * [Statistiky v reálném čase](cdn-real-time-stats.md)
   * [Výkon hraničních uzlů](cdn-edge-performance.md)

