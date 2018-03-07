---
title: "Řešení potíží s chybná vstupní události v Azure Stream Analytics | Microsoft Docs"
description: "Jak lze zjistit, která událost Moje vstupní data způsobuje problém v úloze Stream Analytics"
keywords: 
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/19/2018
ms.author: sngun
ms.openlocfilehash: 2b4f82bae20c3cb398848a89715bfdc1316e1ba1
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2018
---
# <a name="troubleshoot-for-malformed-input-events"></a>Řešení potíží s chybná vstupní události

Pokud vstupní datový proud úlohy Stream Analytics obsahuje poškozených zpráv, budou problémy serializace. Chybná zprávy obsahují nesprávné serializace, například chybějící nebo v objektu JSON nebo nesprávná časová razítka formát. Když úloha Stream Analytics přijme zprávu o poškozený, zahodí zprávy a upozorní uživatele s upozorněním. Symbol upozornění se zobrazí na **vstupy** dlaždice úlohy Stream Analytics:

![Vstupy dlaždice](media/stream-analytics-malformed-events/inputs_tile.png)

## <a name="troubleshooting-steps"></a>Řešení potíží

1. Přejděte na dlaždici vstupní a klikněte na tlačítko Zobrazit upozornění.
2. Na dlaždici vstupní podrobnosti zobrazí sadu upozornění s podrobnostmi o problému. Toto je upozornění na příkladu, upozornění se zobrazuje oddíl, posun a pořadová čísla je poškozená data JSON. 

   ![Upozornění s posunem](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Chcete-li získat data JSON, která nemá správný formát, spusťte následující fragment kódu. Tento blok kódu přečte oddílu Id, posun a zobrazí data. Můžete získat v celé ukázce z [úložiště GitHub ukázky](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Jakmile si přečíst data, můžete analyzovat a opravte formát serializace.

```csharp
static void PrintMessages(string partitionId, long offset, int numberOfEvents)
        {
            EventHubReceiver receiver;
            
            try
            {
                foreach (var e in receiver.Receive(numberOfEvents, TimeSpan.FromMinutes(1)))
                {
                    Console.WriteLine(Encoding.UTF8.GetString(e.GetBytes()));
                    Console.WriteLine("----");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.ToString());
                return;
            }

            receiver.Close();
        }
```
## <a name="next-steps"></a>Další postup

* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
