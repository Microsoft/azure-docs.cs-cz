---
title: Principy vstupů pro Azure Stream Analytics
description: Tento článek popisuje koncept vstupů v úloze Azure Stream Analytics a porovnává vstup streamování s referenčním vstupem dat.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 6b841d6b47e009c3b01d9925e11d352c00ed5c19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426440"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Principy vstupů pro Azure Stream Analytics

Úlohy Azure Stream Analytics se připojují k jednomu nebo více datovým vstupům. Každý vstup definuje připojení k existujícímu zdroji dat. Stream Analytics přijímá data přicházející z několika druhů zdrojů událostí, včetně centra událostí, služby IoT Hub a úložiště objektů blob. Vstupy jsou odkazovány podle názvu v dotazu sql streamování, který píšete pro každou úlohu. V dotazu můžete spojit více vstupů a prolnout data nebo porovnat streamovaná data s vyhledáváním pro referenční data a předat výsledky výstupům. 

Stream Analytics má prvotřídní integraci se třemi druhy prostředků jako vstupy:
- [Centra událostí Azure](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Úložiště objektů blob Azure](https://azure.microsoft.com/services/storage/blobs/) 

Tyto vstupní prostředky můžou žít ve stejném předplatném Azure jako vaše úloha Stream Analytics nebo z jiného předplatného.

K vytváření, úpravám a testování úloh Stream Analytics můžete použít [portál Azure](stream-analytics-quick-create-portal.md#configure-job-input), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput), [rozhraní .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), ROZHRANÍ REST [API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)a [Visual Studio.](stream-analytics-tools-for-visual-studio-install.md)

## <a name="stream-and-reference-inputs"></a>Datové a referenční vstupy
Když se data předávají do zdroje dat, spotřebovávají je úloha Stream Analytics a zpracovávají se v reálném čase. Vstupy se dělí na dva typy: vstupy streamů a vstupy referenčních dat.

### <a name="data-stream-input"></a>Vstup datového proudu
Datový proud je neohraničená posloupnost událostí v čase. Úlohy Stream Analytics musí obsahovat alespoň jeden vstup streamu. Jako zdroje vstupu streamu se podporují služby Event Hubs, IoT Hub a Blob Storage. Centra událostí se používají ke shromažďování datových proudů událostí z více zařízení a služeb. Tyto streamy mohou zahrnovat informační kanály aktivit sociálních médií, informace o obchodu s akciemi nebo data ze senzorů. IoT Huby jsou optimalizované pro sběr dat z připojených zařízení ve scénářích Internetu věcí (IoT).  Úložiště objektů blob lze použít jako vstupní zdroj pro příjem hromadných dat jako datový proud, jako jsou například soubory protokolu.  

Další informace o vstupech datových proudů najdete v tématu [Streamování dat jako vstupů do Stream Analytics.](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Vstup referenčních dat
Stream Analytics také podporuje vstup známý jako *referenční data*. Referenční data jsou buď zcela statická, nebo se mění pomalu. Obvykle se používá k provádění korelace a vyhledávání. Můžete například spojit data ve vstupu datového proudu k datům v referenčních datech, stejně jako byste provedli spojení SQL a vyhledáte statické hodnoty. Azure Blob storage a Azure SQL Database jsou aktuálně podporované jako vstupní zdroje pro referenční data. Objekty BLOB zdroje referenčního zdroje dat mají v závislosti na složitosti dotazu a přidělených jednotkách streamování limit až 300 MB (další podrobnosti najdete v části [Omezení velikosti](stream-analytics-use-reference-data.md#size-limitation) v dokumentaci k referenčním datům).

Další informace o vstupech referenčních dat najdete [v tématu Použití referenčních dat pro vyhledávání v Stream Analytics.](stream-analytics-use-reference-data.md)

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
