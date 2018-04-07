---
title: Přidat vstup data do Azure Stream Analytics úloh
description: Zjistěte, jak spojit zdroje dat do vaší úlohy Stream Analytics jako vysílání datového proudu vstupní data ze služby Event Hubs nebo odkaz na data z úložiště blogu.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 713b830717cce7b4b2b0fb1171596659c2275b85
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Přidat streamování data vstup nebo referenční data do úlohy Stream Analytics
Zjistěte, jak spojit zdroje dat do vaší úlohy Stream Analytics jako vysílání datového proudu vstupní data ze služby Event Hubs nebo odkaz na data z úložiště objektů Blob.

Úlohy Azure Stream Analytics může být připojen k jeden datový vstup nebo informace, z nichž každý definuje připojení a stávajícího zdroje dat. Protože data se odešlou do zdroje dat, je spotřebovávají úlohu služby Stream Analytics a zpracování v reálném čase jako datový proud. Stream Analytics obsahuje prvotřídní integrace s [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) a [úložiště objektů Azure Blob](../storage/blobs/storage-dotnet-how-to-use-blobs.md) v rámci i mimo ni úlohy předplatného.

Tento článek je krok v [Stream Analytics studijní](/documentation/learning-paths/stream-analytics/).

## <a name="data-input-streaming-data-and-reference-data"></a>Vstupní data: streamování dat a referenčních dat
Existují dva odlišné typy vstupů ve Stream Analytics: datové proudy a referenční data.

* **Datové proudy**: úlohy Stream Analytics musí obsahovat alespoň jeden vstup datového streamu spotřebované a transformovat úlohou. Azure Blob storage a Azure Event Hubs se podporují jako vstupní zdroje dat datového proudu. Azure Event Hubs slouží ke shromažďování streamů událostí z připojených zařízení, služeb a aplikací. Azure Blob storage můžete použít jako vstupní zdroj pro příjem dat hromadné jako datový proud.  
* **Referenční data**: Stream Analytics podporuje druhého typu pomocného vstupní volané referenční data.  Oproti dat přesouvaných tato data jsou statická nebo zpomalení změna.  Obvykle se používá pro provádění look-ups a korelací s datové proudy vytvořit bohatší datové sady.  Azure Blob storage je aktuálně podporované pouze vstupní zdroj pro referenční data.  

Chcete-li přidat vstup do úlohy Stream Analytics:

1. V portálu Azure klikněte na **vstupy** a pak klikněte na **přidat vstup** v úloze Stream Analytics.
   
    ![Portál Azure – přidání vstup.](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  
   
    V portálu Azure klikněte na **vstupy** dlaždici v úloze Stream Analytics.  
   
    ![Portál Azure – přidání datového vstupu.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  
2. Zadejte typ vstupu: buď **datový proud** nebo **referenční data**.
   
    ![Přidání správná data vstup, streamování nebo odkaz](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  
   
    ![Přidání správná data vstup, streamování nebo odkaz](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  
3. Pokud vytváříte vstupní datový proud, zadejte typ zdroje pro vstup.  Během vytváření referenční Data jako jenom objekt Blob úložiště je podporována v tuto chvíli můžete tento krok přeskočen.
   
    ![Přidat vstup datového streamu dat](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  
   
    ![Přidat vstup datového streamu dat](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  
4. Zadejte popisný název pro tento vstup do pole vstupní Alias.  Tento název se použije v dotazu vaše úlohy později na odkazovat na vstup.
   
    Vyplňte zbytek vlastnosti požadované připojení pro připojení ke zdroji dat. Tato pole se liší podle typu vstupu a zdroj typu a jsou definovány podrobně [zde](stream-analytics-create-a-job.md).  
   
    ![Přidat vstup datového centra událostí](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  
5. Zadejte nastavení serializace pro vstupní data:
   
   * Abyste měli jistotu, dotazy fungovaly podle očekávání, zadejte **formát serializace událostí** příchozích dat.  Formáty podporované serializace jsou JSON, CSV a Avro.
   * Ověřte **kódování** pro data.  V tuto chvíli je jediným podporovaným formátem kódování UTF-8.
     
     ![Nastavení serializace dat pro vložení dat](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  
     
     ![Nastavení serializace dat pro vložení dat](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  
6. Po dokončení vytvoření vstupní, Stream Analytics ověří, že se můžete připojit k vstupního zdroje.  Stav operace testování připojení můžete zobrazit v centru oznámení.
   
    ![Testovací připojení vstupních dat](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  
   
    ![Testovací připojení vstupních dat](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>Získat pomoc s streamování vstupů dat.
Další podporu naleznete v našem [fóru služby Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

