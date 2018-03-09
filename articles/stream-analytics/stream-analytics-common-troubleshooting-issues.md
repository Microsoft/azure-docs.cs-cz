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
ms.date: 03/05/2018
ms.author: sngun
ms.openlocfilehash: 6b6c154568fe97b7495ae70dc162dc475169afea
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Běžné problémy při Stream Analytics a kroky pro řešení potíží

## <a name="troubleshoot-for-malformed-input-events"></a>Řešení potíží s chybná vstupní události

Pokud vstupní datový proud úlohy Stream Analytics obsahuje poškozených zpráv, budou problémy serializace. Chybná zprávy obsahují nesprávné serializace, například chybějící nebo v objektu JSON nebo nesprávná časová razítka formát. Když úloha Stream Analytics přijme zprávu o poškozený, zahodí zprávy a upozorní uživatele s upozorněním. Symbol upozornění se zobrazí na **vstupy** dlaždice úlohy Stream Analytics (registrace upozornění existuje, dokud je úloha ve stavu spuštění):

![Vstupy dlaždice](media/stream-analytics-malformed-events/inputs_tile.png)

Můžete povolit protokolů diagnostiky k zobrazení podrobností o upozornění. Pro vstupní události malformatted protokoly spouštění obsahují položku s zprávu, která vypadá takto: "zpráva: nešlo deserializovat vstupní událostí z prostředku <blob URI> jako json)". 

### <a name="troubleshooting-steps"></a>Řešení potíží

1. Přejděte na dlaždici vstupní a klikněte na tlačítko Zobrazit upozornění.
2. Na dlaždici vstupní podrobnosti zobrazí sadu upozornění s podrobnostmi o problému. Toto je upozornění na příkladu, upozornění se zobrazuje oddíl, posun a pořadová čísla je poškozená data JSON. 

   ![Upozornění s posunem](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Chcete-li získat data JSON, která nemá správný formát, spustit kód CheckMalformedEvents.cs, můžete získat z it [ukázky úložiště GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Tento kód načte oddílu Id, posun a vytiskne data, která se nachází v tomto posun. 

4. Jakmile si přečíst data, můžete analyzovat a opravte formát serializace. 

## <a name="handling-duplicate-records-when-using-azure-sql-database-as-output-for-a-stream-analytics-job"></a>Zpracování duplicitní záznamy při používání Azure SQL Database jako výstup úlohy Stream Analytics

Při konfiguraci Azure SQL database jako výstup do úlohy Stream Analytics, hromadně vloží záznamy do cílové tabulky. Obecně platí, služby Azure stream analytics zaručuje [alespoň jednou doručení]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) do výstupní jímku jeden stále [dosáhnout přesně-jednou doručení]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) do SQL výstup, když SQL tabulka má jedinečné omezení definovaná. 

Jakmile jsou jedinečné klíče omezení Instalační program na tabulku SQL a existují duplicitní záznamy vkládání do tabulky SQL, Azure Stream Analytics odebere duplicitní záznam rozdělením dat do dávek a rekurzivně vkládání dávkách až do jedné duplicitní záznam nachází. Existuje velký počet duplicitní řádky v svůj kanál, rozdělení a vkládání dat ignoruje duplicitní hodnoty jeden po druhém rekurzivně je časově náročné. Pokud se zobrazí více klíčů porušení upozornění v protokolu aktivit v rámci poslední hodinu, je pravděpodobné, že výstupu SQL je zpomalení celé úlohy. Chcete-li vyřešit tento problém, měli byste [konfigurace index]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) , který způsobuje selhání klíče povolením možnosti IGNORE_DUP_KEY. Tuto možnost povolíte duplicitní hodnoty, který se má ignorovat SQL během hromadné vložení a SQL Azure jednoduše vytvoří zprávu s upozorněním místo k chybě. Azure Stream Analytics už nevytváří chyby porušení primárního klíče.

Všimněte si následujících připomínky při konfiguraci IGNORE_DUP_KEY pro několik typů indexy:

* Na primární klíč nebo jedinečné omezení, které používá příkaz ALTER INDEX nelze nastavit IGNORE_DUP_KEY, je třeba vyřadit a znovu vytvořte index.  
* Můžete nastavit možnost IGNORE_DUP_KEY pomocí příkazu ALTER INDEX pro jedinečný index, který se liší od primární klíč nebo jedinečné omezení a vytvořit pomocí definice CREATE INDEX nebo INDEX.  
* IGNORE_DUP_KEY se nevztahuje na indexy úložiště sloupců, protože nelze vynutit jedinečnost tyto indexy.  

## <a name="next-steps"></a>Další postup

* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

