---
title: Vyčištění Azure Stream Analytics úlohy
description: Tento článek popisuje různé způsoby, jak odstranit úlohy Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: f078ab217a5c4911fbd132338883504897db899f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86044596"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Zastavení nebo odstranění úlohy Azure Stream Analytics

Azure Stream Analytics úlohy je možné snadno zastavit nebo odstranit pomocí Azure Portal, Azure PowerShell, sady Azure SDK pro .NET nebo REST API. Po odstranění nelze úlohu Stream Analytics obnovit.

>[!NOTE] 
>Když zastavíte úlohu Stream Analytics, data budou nadále jenom ve vstupním a výstupním úložišti, jako je Event Hubs nebo Azure SQL Database. Pokud potřebujete odebrat data z Azure, postupujte podle kroků pro vložení vstupních a výstupních prostředků vaší Stream Analytics úlohy.

## <a name="stop-a-job-in-azure-portal"></a>Zastavení úlohy v Azure Portal

Po zastavení úlohy dojde ke zrušení zřízení prostředků a zastavení zpracování událostí. Poplatky související s touto úlohou se také zastaví. Veškerá vaše konfigurace je ale zachovaná a později ji můžete restartovat. 

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). 

2. Vyhledejte spuštěnou úlohu Stream Analytics a vyberte ji.

3. Na stránce Stream Analytics úlohy vyberte **zastavit** a zastavte úlohu. 

   ![Zastavit úlohu Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Odstranění úlohy v Azure Portal

>[!WARNING] 
>Po odstranění nelze úlohu Stream Analytics obnovit.

1. Přihlaste se k portálu Azure. 

2. Vyhledejte existující úlohu Stream Analytics a vyberte ji.

3. Na stránce Stream Analytics úlohy vyberte **Odstranit** , aby se úloha odstranila. 

   ![Odstranit úlohu Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Zastavení nebo odstranění úlohy pomocí prostředí PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K zastavení úlohy pomocí prostředí PowerShell použijte rutinu [stop-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) . K odstranění úlohy pomocí prostředí PowerShell použijte rutinu [Remove-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) .

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Zastavení nebo odstranění úlohy pomocí sady Azure SDK pro .NET

K zastavení úlohy pomocí sady Azure SDK pro .NET použijte metodu [StreamingJobsOperationsExtensions. BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) . Odstranění úlohy pomocí sady Azure SDK pro .NET, [StreamingJobsOperationsExtensions. BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet)

## <a name="stop-or-delete-a-job-using-rest-api"></a>Zastavení nebo odstranění úlohy pomocí REST API

Chcete-li zastavit úlohu pomocí REST API, přečtěte si metodu [stop](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) . Pokud chcete úlohu odstranit pomocí REST API, přečtěte si metodu [Delete](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) .
