---
title: Úvod do funkce okno Stream Analytics | Microsoft Docs
description: Další informace o tři funkce okna v Stream Analytics (přeskakujícího, vše, klouzavé).
keywords: přeskakujícího okna, posuvném okně, posílání okna
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: 0d8d8717-5d23-43f0-b475-af078ab4627d
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: 3757834ff816fffc4571aeef8c164c2230c0f7c2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="introduction-to-stream-analytics-window-functions"></a>Úvod do okna Stream Analytics funkce
V mnoha reálném čase streamování scénáře je potřeba provádět operace pouze na data obsažená v dočasné systému windows. Nativní podpora pro oddílová funkce je klíčovou funkcí Azure Stream Analytics, která přemísťuje se ručička na vývojáře produktivitu při vytváření úlohy zpracování komplexní datového proudu. Stream Analytics umožňuje vývojářům používat [ **Přeskakujícího**](https://msdn.microsoft.com/library/dn835055.aspx), [ **Hopping** ](https://msdn.microsoft.com/library/dn835041.aspx) a [ **posuvné** ](https://msdn.microsoft.com/library/dn835051.aspx) windows k provádění dočasné operací na datový proud. Je to, že všechny [okno](https://msdn.microsoft.com/library/dn835019.aspx) operations výstup výsledků na **end** okna. Výstup okna bude jedinou událost podle používá agregační funkci. Události budou mít časové razítko konce okna a všechny funkce okna jsou definovány s pevnou délkou. Nakonec je důležité si uvědomit, že je třeba používat všechny funkce okno v [ **GROUP BY** ](https://msdn.microsoft.com/library/dn835023.aspx) klauzule.

![Stream Analytics okno funkce koncepty](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Přeskakující okno
Přeskakujícího okna, které funkce se používají pro segment datový proud do různých čas segmentů a provádět funkce proti, jako je například následující příklad. Klíče differentiators z Přeskakující okno se, že zopakují, se nepřekrývají, a událost nemůže patřit do více než jeden přeskakující okno.

![Funkce okno analýzy datového proudu přeskakujícího Úvod](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Skákající okno
Skákající okno funkce směrování dál v čase prostřednictvím uplynutí určité doby. To může být snadno si je představit jako Přeskakující windows, které může dojít k překrytí, takže události můžou patřit do více než jednu sadu výsledků Hopping okno. Vytvoření okna Hopping stejné jako Přeskakující okno jeden by jednoduše zadejte velikost skoku tak, aby byla stejná jako velikost okna. 

![Stream Analytics okno funkce skákající Úvod](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Posuvné okno
Posuvné okno funkce, na rozdíl od Přeskakujícího nebo posílání windows, vytvořit výstup **pouze** při výskytu události. Každý okno bude mít aspoň jednu událost a okna nepřetržitě přesune dál € (Epsilon –). Jako posílání Windows události může patřit do více než jeden klouzavé okna.

![Stream Analytics okno funkce klouzavé Úvod](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>Získání nápovědy k okno funkce
Další podporu naleznete v našem [fóru služby Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

