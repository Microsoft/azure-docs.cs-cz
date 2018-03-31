---
title: Vizualizace a řešení potíží s úlohy Stream Analytics | Microsoft Docs
description: Zjistěte, jak k vizualizaci kanálu úlohy Stream Analytics pro samoobslužné řešení problémů pomocí funkce diagram diagnostiky.
keywords: ''
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: d87841cd-c59f-4a46-b46e-8b904fdc12e9
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: eae43a6a444514855229af760de6aa1cbec7840a
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Vizualizace a řešení potíží s úlohy Stream Analytics
V Stream Analytics stejně jako u jiných cloudových technologií, řešení potíží se někdy potřeba, aby viděl proč úlohu nevytváří očekávaný výstup (nebo žádný výstup k tomuto účelu). U této koncepce pamatovat Stream Analytics poskytuje možnost pro vizualizaci úlohu streamování. Toto je také užitečné jako nástroj pro modelování a má na straně benefit pro tyto vyžadují dokumentaci práci.

V panelu vizualizace jsou viditelné a také spouštěna dotaz a pak všechny výstupy nakonfigurovaný vstupní hodnoty. Problémy s připojením nebo konfigurace se může stát více zřejmá a může být také vhodné najdete v části vizuální reprezentace konfiguraci.

## <a name="using-the-diagnosis-diagram-tool"></a>Pomocí nástroje diagram diagnostiku
Pro přístup k této vizualizér, jednoduše klikněte na tlačítko "Diagnostiku diagram" v části "Nastavení" úloha Stream Analytics.

![stream-analytics-troubleshoot-visualization-diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Každý vstup a výstup je barevně označíte, aktuální stav této součásti, jak je znázorněno v následujícím způsobem.

![stream-analytics-troubleshoot-visualization-input-map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Pokud chce uživatel podívejte se na postup zprostředkující dotazu pochopit vzory toku dat uvnitř úlohy, nástroj pro vizualizaci poskytuje zobrazení obsahuje rozpis dotazu do jeho součásti kroky a pořadí toku. Kliknutím na každého kroku dotazu ukazuje odpovídající oddíl v dotazu úpravy podokně podle pokynů. 

![stream-analytics-troubleshoot-visualization-intermediate-steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

