---
title: Analýza vzorů využití Azure CDN
description: Tento článek popisuje různé typy sestav analýzy, které jsou k dispozici pro Azure CDN Products.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/22/2020
ms.author: allensu
ms.openlocfilehash: 169889dbb87d00fdde44ff72c0d2004c331604ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073035"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analýza vzorů využití Azure CDN

Po povolení CDN pro vaši aplikaci můžete monitorovat využití CDN, kontrolovat stav doručování a řešit potenciální problémy. Azure CDN poskytují tyto možnosti následujícími způsoby: 

## <a name="raw-logs-for-azure-cdn-from-microsoft"></a>Nezpracované protokoly pro Azure CDN od Microsoftu
Se standardním profilem Microsoftu můžete povolit nezpracované protokoly a vybrat streamování protokolů do:

* Azure Storage
* Event Hubs
* Azure Log Analytics

Pomocí Azure Log Analytics můžete zobrazit metriky monitorování a nastavit výstrahy. 

Další informace najdete v tématu [Azure CDN nezpracovaných protokolů HTTP](enable-raw-logs.md).


## <a name="core-analytics-via-azure-diagnostic-logs"></a>Základní analýzy prostřednictvím diagnostických protokolů Azure

Základní analýza je dostupná pro koncové body CDN pro všechny cenové úrovně. Protokoly diagnostiky Azure umožňují exportovat základní analýzy do úložiště Azure, do Center událostí nebo do protokolů Azure Monitor. Protokoly Azure Monitor nabízí řešení s grafy, které jsou uživatelsky konfigurovatelné a přizpůsobitelné. Další informace o diagnostických protokolech Azure najdete v tématu [diagnostické protokoly Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Základní sestavy Verizon

Základní sestavy nabízí **Azure CDN Standard od Verizon** nebo **Azure CDN Premium od profilů Verizon** . Základní sestavy si můžete zobrazit na doplňkovém portálu Verizon. Sestavy Verizon Core jsou přístupné prostřednictvím možnosti **Spravovat** z Azure Portal a nabízejí různé druhy grafů a zobrazení. Další informace najdete v tématu [základní sestavy z Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Vlastní sestavy Verizon

**Azure CDN Standard z** profilů Verizon z Verizon **Azure CDN nebo Premium z profilů** poskytuje vlastní sestavy. Vlastní sestavy můžete zobrazit na doplňkovém portálu Verizon. Vlastní sestavy Verizon jsou přístupné prostřednictvím možnosti **Spravovat** z Azure Portal. 

Vlastní sestavy zobrazují počet přístupů nebo dat přenesených u každého okraje CNAME. Data jsou seskupena podle kódu odpovědi HTTP nebo stavu mezipaměti v časovém intervalu. Další informace najdete v tématu [vlastní sestavy z Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium ze sestav Verizon

S **Azure CDN Premium z Verizon**můžete také získat přístup k následujícím sestavám:
   * [Rozšířené sestavy HTTP](cdn-advanced-http-reports.md)
   * [Statistiky v reálném čase](cdn-real-time-stats.md)
   * [Azure CDN výkon hraničního uzlu](cdn-edge-performance.md)

## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli o různých možnostech sestav analýzy pro Azure CDN.

Další informace o Azure CDN a dalších službách Azure, které jsou uvedené v tomto článku, najdete v tématech:

* [Co je Azure CDN?](cdn-overview.md)
* [Azure CDN nezpracovaných protokolů HTTP](enable-raw-logs.md)


