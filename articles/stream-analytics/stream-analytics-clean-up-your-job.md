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
ms.openlocfilehash: 2652b97c9392d3016bbc52209d4b2bda81c31706
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022077"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Zastavení nebo odstranění úlohy Azure Stream Analytics

Azure Stream Analytics úlohy je možné snadno zastavit nebo odstranit pomocí Azure Portal, Azure PowerShell, sady Azure SDK pro .NET nebo REST API. Po odstranění nelze úlohu Stream Analytics obnovit.

>[!NOTE] 
>Když zastavíte úlohu Stream Analytics, data budou nadále jenom ve vstupním a výstupním úložišti, jako je Event Hubs nebo Azure SQL Database. Pokud potřebujete odebrat data z Azure, postupujte podle kroků pro vložení vstupních a výstupních prostředků vaší Stream Analytics úlohy.

## <a name="stop-a-job-in-azure-portal"></a>Zastavení úlohy v Azure Portal

Po zastavení úlohy dojde ke zrušení zřízení prostředků a zastavení zpracování událostí. Poplatky související s touto úlohou se také zastaví. Veškerá vaše konfigurace je ale zachovaná a později ji můžete restartovat. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 

2. Vyhledejte spuštěnou úlohu Stream Analytics a vyberte ji.

3. Na stránce Stream Analytics úlohy vyberte **zastavit** a zastavte úlohu. 

   ![Zastavit úlohu Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Odstranění úlohy v Azure Portal

>[!WARNING] 
>Po odstranění nelze úlohu Stream Analytics obnovit.

1. Přihlaste se k webu Azure Portal. 

2. Vyhledejte existující úlohu Stream Analytics a vyberte ji.

3. Na stránce Stream Analytics úlohy vyberte **Odstranit** , aby se úloha odstranila. 

   ![Odstranit úlohu Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Zastavení nebo odstranění úlohy pomocí prostředí PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K zastavení úlohy pomocí prostředí PowerShell použijte rutinu [stop-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) . K odstranění úlohy pomocí prostředí PowerShell použijte rutinu [Remove-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) .

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Zastavení nebo odstranění úlohy pomocí sady Azure SDK pro .NET

K zastavení úlohy pomocí sady Azure SDK pro .NET použijte metodu [StreamingJobsOperationsExtensions. BeginStop](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop) . Odstranění úlohy pomocí sady Azure SDK pro .NET, [StreamingJobsOperationsExtensions. BeginDelete](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete)

## <a name="stop-or-delete-a-job-using-rest-api"></a>Zastavení nebo odstranění úlohy pomocí REST API

Chcete-li zastavit úlohu pomocí REST API, přečtěte si metodu [stop](/rest/api/streamanalytics/2016-03-01/streamingjobs/stop) . Pokud chcete úlohu odstranit pomocí REST API, přečtěte si metodu [Delete](/rest/api/streamanalytics/2016-03-01/streamingjobs/delete) .