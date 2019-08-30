---
title: Vyčištění vaší úlohy Azure Stream Analytics
description: Tento článek popisuje různé metody pro odstranění vaší úlohy Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 093ada2b2751540b986154be3a2f333784c1259f
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173291"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Zastavení nebo odstranění úlohy Azure Stream Analytics

Azure Stream Analytics úlohy je možné snadno zastavit nebo odstranit pomocí Azure Portal, Azure PowerShell, sady Azure SDK pro .NET nebo REST API. Po odstranění nelze úlohu Stream Analytics obnovit.

>[!NOTE] 
>Při zastavení vaší úlohy Stream Analytics, data se uchovávají pouze ve vstupní a výstupní úložiště, jako jsou Event Hubs nebo Azure SQL Database. V případě potřeby k odebrání dat z Azure je potřeba provést proces odebrání za vstupní a výstupní prostředky vaší úlohy Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Zastavení úlohy na webu Azure portal

Po zastavení úlohy dojde ke zrušení zřízení prostředků a zastavení zpracování událostí. Poplatky související s touto úlohou se také zastaví. Veškerá vaše konfigurace je ale zachovaná a později ji můžete restartovat. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. Vyhledat běžící úlohy Stream Analytics a vyberte ji.

3. Na stránce úlohy Stream Analytics, vyberte možnost **Zastavit** o zastavení úlohy. 

   ![Zastavit úlohu Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Odstraňování úlohy na webu Azure portal

>[!WARNING] 
>Po odstranění nelze úlohu Stream Analytics obnovit.

1. Přihlaste se k portálu Azure. 

2. Vyhledejte existující úlohy Stream Analytics a vyberte ho.

3. Na stránce úlohy Stream Analytics, vyberte možnost **odstranit** odstraňte úlohu. 

   ![Odstranit úlohu Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Zastavení nebo odstranění úlohy pomocí Powershellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K zastavení úlohy pomocí prostředí PowerShell použijte rutinu [stop-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) . K odstranění úlohy pomocí prostředí PowerShell použijte rutinu [Remove-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) .

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Zastavit nebo odstranit úlohu pomocí sady Azure SDK pro .NET

Chcete-li zastavit úlohu pomocí sady Azure SDK pro .NET, použijte [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) metody. Chcete-li odstranit úlohu pomocí sady Azure SDK pro .NET, [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) metody.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Zastavit nebo odstranit úlohu pomocí rozhraní REST API

Pokud chcete zastavit úlohu pomocí rozhraní REST API, přečtěte si [Zastavit](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) metody. Pokud chcete odstranit úlohu pomocí rozhraní REST API, přečtěte si [odstranit](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) metody.
