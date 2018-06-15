---
title: Porozumět vstupy pro Azure Stream Analytics
description: Tento článek popisuje koncept vstupy v úlohu služby Azure Stream Analytics porovnávání streamování vstup referenčního datového vstupu.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: 926821e2ba9912ae0140f11c9fe9a2d504609a1e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186058"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Porozumět vstupy pro Azure Stream Analytics

Úlohy Azure Stream Analytics se připojit k jeden nebo více vstupů data. Každý vstup definuje připojení pro existujícího zdroje dat. Stream Analytics přijme příchozí data z několika zdrojů událostí včetně Event Hubs, IoT Hub a objektů Blob storage. Název v streamování dotaz SQL, který můžete psát pro každou úlohu odkazují vstupy. V dotazu můžete připojit k více vstupů přizpůsobte dat nebo porovnat streamování dat pomocí vyhledávání pro referenční data a předat výstupy výsledky. 

Stream Analytics obsahuje prvotřídní integraci od tři druhy prostředků jako vstupy:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Tyto prostředky vstupní může existovat ve stejné předplatné jako váš úlohy služby Stream Analytics, nebo z jiného předplatného.

Můžete použít [portál Azure](stream-analytics-quick-create-portal.md#configure-input-to-the-job), [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput), [.Net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input), a [Visual Studio](stream-analytics-tools-for-visual-studio.md)vytvářet, upravovat a testovat vstupy úlohy Stream Analytics.

## <a name="stream-and-reference-inputs"></a>Vstupní hodnoty datového proudu a referenční dokumentace
Jako data odesílána ke zdroji dat, má spotřebovávají úlohu služby Stream Analytics a zpracovány v reálném čase. Vstupy se dál dělí na dva typy: stream vstupy a referenční data vstupy dat.

### <a name="data-stream-input"></a>Datový proud vstup
Datový proud je už bez vazby pořadí událostí v čase. Úlohy Stream Analytics musí obsahovat alespoň jeden vstup datového streamu. Služba Event Hubs, IoT Hub a úložiště objektů Blob jsou podporovány jako vstupní zdroje dat datového proudu. Služba Event Hubs slouží ke shromažďování streamů událostí z více zařízení a služeb. Tyto datové proudy mohou zahrnovat sociálních médií o aktivitách, informace o uložených obchodní nebo dat ze senzorů. Centra IoT jsou optimalizované pro shromažďování dat z připojených zařízení v scénáře Internetu věcí (IoT).  Úložiště objektů BLOB můžete použít jako vstupní zdroj pro příjem dat hromadné jako datový proud, jako jsou například soubory protokolu.  

Další informace o streamování vstupy dat najdete v tématu [Streamovat data jako vstup do služby Stream Analytics](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Referenčního datového vstupu
Stream Analytics podporuje také označuje jako vstup *referenční data*. Referenční data jsou buď úplně statická nebo pomalu změny. Se obvykle používá k provedení korelaci a vyhledávání. Například můžete spojit data vstup datového streamu k datům v referenční data tak, jak můžete provést připojení SQL k vyhledání statické hodnoty. Azure Blob storage je aktuálně podporované pouze vstupní zdroj pro referenční data. Referenční data zdroje BLOB jsou omezeny na 100 MB velikost.

Další informace o referenční data vstupy najdete v tématu [pomocí referenční data pro vyhledávání v Stream Analytics](stream-analytics-use-reference-data.md)

Tento článek je krok v [Stream Analytics studijní](/documentation/learning-paths/stream-analytics/).

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Rychlý úvod: Vytvoření úlohy Stream Analytics pomocí portálu Azure](stream-analytics-quick-create-portal.md)