---
title: Postup konfigurace dat vstupů pro úlohy Azure Stream Analytics
description: Nakonfigurujte výstupy pro úlohy Stream Analytics | učení segmentu cesty.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/26/2017
ms.openlocfilehash: 2e54b7970e4748ff56844fb06fa5177ddb207fa6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>Postup konfigurace dat vstupů pro úlohy Stream Analytics

Úlohy Azure Stream Analytics může být připojen k jeden nebo více výstupů dat, které definuje připojení a jímky existující data. Úlohu Stream Analytics procesy a transformuje příchozích dat, proud dat výstupních událostech je zapsán do výstupu dané úlohy.

Datový proud analytická data výstupy lze použít jako zdroj řídicí panely v reálném čase nebo výstrahy, spusťte pracovní postupy přesun dat nebo jednoduše archivaci dat pro pozdější zpracování dávky. Stream Analytics obsahuje prvotřídní integrace s několik služeb Azure, které jsou popsány zde podrobně.

Přidání výstup do úlohy Stream Analytics:

1. V [portál Azure](https://portal.azure.com), spusťte úlohu a klikněte na tlačítko **výstupy** a pak klikněte na **přidat** v zobrazeném okně výstupy.
   
    ![Přidejte výstupy](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  
   
2. Zadejte popisný název pro tento výstup v **Alias pro výstup** pole. Tento název můžete použít v dotazu vaše úlohy později na odkazovat na výstup.  
   
    Vyplňte zbytek vlastnosti požadované připojení pro připojení k výstupu.  Tato pole se liší podle typu výstupu a jsou definovány zde podrobně.  
   
    ![Výběr typu přesunu dat](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  
   
3. V závislosti na typu výstupu musíte určit, jak je data serializovat nebo formátu. Nastavení konkrétní serializace pro každý typ výstupu jsou popsané v tomto poli.
   
    Vyplňte zbytek vlastnosti požadované připojení pro připojení ke zdroji dat. Tato pole se liší podle typu vstupu a zdroj typu a jsou definovány v podrobně [vytvoření úlohy článku](stream-analytics-create-a-job.md).  

> [!Note]
>
> Všechny element output přidány do úlohy, musí existovat před zahájení úlohy a události spuštěním toku. Například pokud použijete jako výstupu úložiště objektů Blob, úloha nebude vytvořit účet úložiště automaticky. Musí být před spuštěním úlohy ASA vytvořený uživatelem.
> 
 

## <a name="get-help"></a>Podpora
Další podporu naleznete v našem [fóru služby Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

