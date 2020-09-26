---
title: Vysvětlení vstupů pro Azure Stream Analytics
description: Tento článek popisuje koncept vstupů v Azure Stream Analytics úlohy, který porovnává vstup streamování s odkazem na vstup dat.
author: jseb225
ms.author: krishmam
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: b58a2b4cb5cd083f837eb598d4e5f330783181c6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91279990"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Vysvětlení vstupů pro Azure Stream Analytics

Úlohy Azure Stream Analytics se připojují k jednomu nebo více vstupům dat. Každé zadání definuje připojení k existujícímu zdroji dat. Stream Analytics přijímá data příchozí z několika druhů zdrojů událostí, včetně Event Hubs, IoT Hub a BLOB Storage. Na vstupy se odkazuje podle názvu v dotazu SQL streamování, který zapisujete pro každou úlohu. V dotazu můžete spojit více vstupů s daty Blendu nebo porovnávat streamovaná data s vyhledáváním na referenční data a předat výsledky do výstupů. 

Stream Analytics má při první třídě integraci se čtyřmi druhy prostředků jako vstupy:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 
- [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) 

Tyto vstupní prostředky můžou být živé ve stejném předplatném Azure jako vaše Stream Analyticsová úloha nebo z jiného předplatného.

Pomocí [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-input),  [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput), rozhraní [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)a sady [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) můžete vytvářet, upravovat a testovat Stream Analytics úlohy.

## <a name="stream-and-reference-inputs"></a>Vstupy datových proudů a odkazů
Při vložení dat do zdroje dat je tato data spotřebována úlohou Stream Analytics a zpracovávána v reálném čase. Vstupy se dělí na dva typy: vstupy streamů a vstupy referenčních dat.

### <a name="data-stream-input"></a>Vstup datového proudu
Datový proud je neohraničená posloupnost událostí v průběhu času. Úlohy Stream Analytics musí obsahovat alespoň jeden vstup streamu. Event Hubs, IoT Hub, Azure Data Lake Storage Gen2 a BLOB Storage jsou podporovány jako vstupní zdroje datového proudu. Event Hubs se používají ke shromažďování datových proudů událostí z více zařízení a služeb. Tyto datové proudy můžou zahrnovat informační kanály o aktivitách sociálních médií, informace o zásobách nebo data ze senzorů. Centra IoT jsou optimalizovaná pro shromažďování dat z připojených zařízení ve scénářích Internet věcí (IoT).  Úložiště objektů BLOB se dá použít jako vstupní zdroj pro ingestování hromadných dat jako datového proudu, jako jsou soubory protokolů.  

Další informace o datových vstupech streamování najdete v tématu [streamování dat jako vstup do Stream Analytics](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Referenční datové vstupy
Stream Analytics podporuje také vstup známý jako *referenční data*. Referenční data jsou buď zcela statická, nebo se mění pomalu. Obvykle se používá k provádění korelace a vyhledávání. Například můžete spojit data datového proudu s daty v referenčních datech, podobně jako byste provedli příkaz SQL JOIN k vyhledání statických hodnot. Úložiště objektů BLOB v Azure, Azure Data Lake Storage Gen2 a Azure SQL Database se v současné době podporují jako vstupní zdroje pro referenční data. Velikost objektů BLOB zdroje referenčních dat má limit až 300 MB, v závislosti na složitosti dotazu a přidělených jednotkách streamování (Další informace najdete v části [omezení velikosti](stream-analytics-use-reference-data.md#size-limitation) v dokumentaci referenčních dat).

Další informace o vstupech referenčních dat naleznete [v tématu použití referenčních dat pro vyhledávání v Stream Analytics](stream-analytics-use-reference-data.md)

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
