---
title: Vyčištění vaší úlohy Azure Stream Analytics
description: Tento článek popisuje různé metody pro odstranění vaší úlohy Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: e43e1034abe4bbe3d31a46ab3b98b0efe612b852
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159456"
---
# <a name="clean-up-your-azure-stream-analytics-job"></a>Vyčištění vaší úlohy Azure Stream Analytics

Úlohy Azure Stream Analytics můžete snadno odstranit prostřednictvím webu Azure portal, prostředí Azure PowerShell, sady Azure SDK for .net nebo REST API. Úlohu Stream Analytics nelze obnovit, jakmile je Odstraněná.

>[!NOTE] 
>Při zastavení vaší úlohy Stream Analytics, data se uchovávají pouze ve vstupní a výstupní úložiště, jako jsou Event Hubs nebo Azure SQL Database. V případě potřeby k odebrání dat z Azure je potřeba provést proces odebrání za vstupní a výstupní prostředky vaší úlohy Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Zastavení úlohy na webu Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. Vyhledat běžící úlohy Stream Analytics a vyberte ji.

3. Na stránce úlohy Stream Analytics, vyberte možnost **Zastavit** o zastavení úlohy. 

   ![Zastavit úlohu Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Odstraňování úlohy na webu Azure portal

1. Přihlaste se k portálu Azure. 

2. Vyhledejte existující úlohy Stream Analytics a vyberte ho.

3. Na stránce úlohy Stream Analytics, vyberte možnost **odstranit** odstraňte úlohu. 

   ![Odstranit úlohu Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Zastavení nebo odstranění úlohy pomocí Powershellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Chcete-li zastavit úlohu pomocí prostředí PowerShell, použijte [Stop-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) rutiny. Pokud chcete odstranit úlohu pomocí prostředí PowerShell, použijte [odebrat AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) rutiny.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Zastavit nebo odstranit úlohu pomocí sady Azure SDK pro .NET

Chcete-li zastavit úlohu pomocí sady Azure SDK pro .NET, použijte [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) metody. Chcete-li odstranit úlohu pomocí sady Azure SDK pro .NET, [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) metody.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Zastavit nebo odstranit úlohu pomocí rozhraní REST API

Pokud chcete zastavit úlohu pomocí rozhraní REST API, přečtěte si [Zastavit](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) metody. Pokud chcete odstranit úlohu pomocí rozhraní REST API, přečtěte si [odstranit](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) metody.
