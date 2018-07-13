---
title: Vyčištění vaší úlohy Azure Stream Analytics
description: Tento článek představuje příručku pro odstranění úlohy Azure Stream Analytics.
services: stream-analytics
author: mamccrea
manager: kfile
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 580d05909ff3c94c982be5353b3b5e86a78fc43f
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969336"
---
# <a name="clean-up-your-azure-stream-analytics-job"></a>Vyčištění vaší úlohy Azure Stream Analytics

Úlohy Azure Stream Analytics můžete snadno odstranit prostřednictvím webu Azure portal, prostředí Azure PowerShell, sady Azure SDK for .net nebo REST API.

>[!NOTE] 
>Při zastavení vaší úlohy Stream Analytics, data se uchovávají pouze ve vstupní a výstupní úložiště, jako jsou Event Hubs nebo Azure SQL Database. V případě potřeby k odebrání dat z Azure je potřeba provést proces odebrání za vstupní a výstupní prostředky vaší úlohy Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Zastavení úlohy na webu Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. Vyhledat běžící úlohy Stream Analytics a vyberte ji.

3. Na stránce úlohy Stream Analytics, vyberte možnost **Zastavit** o zastavení úlohy. 

   ![Zastavit úlohu](./media/stream-analytics-clean-up-your-job/stop-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Odstraňování úlohy na webu Azure portal

1. Přihlaste se k portálu Azure. 

2. Vyhledejte existující úlohy Stream Analytics a vyberte ho.

3. Na stránce úlohy Stream Analytics, vyberte možnost **odstranit** odstraňte úlohu. 

   ![Odstranit úlohu](./media/stream-analytics-clean-up-your-job/delete-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Zastavení nebo odstranění úlohy pomocí Powershellu

Chcete-li zastavit úlohu pomocí prostředí PowerShell, použijte [Stop-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob?view=azurermps-5.7.0) rutiny. Pokud chcete odstranit úlohu pomocí prostředí PowerShell, použijte [Remove-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/Remove-AzureRmStreamAnalyticsJob?view=azurermps-5.7.0) rutiny.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Zastavit nebo odstranit úlohu pomocí sady Azure SDK pro .NET

Chcete-li zastavit úlohu pomocí sady Azure SDK pro .NET, použijte [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) metody. Chcete-li odstranit úlohu pomocí sady Azure SDK pro .NET, [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) metody.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Zastavit nebo odstranit úlohu pomocí rozhraní REST API

Pokud chcete zastavit úlohu pomocí rozhraní REST API, přečtěte si [Zastavit](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) metody. Pokud chcete odstranit úlohu pomocí rozhraní REST API, přečtěte si [odstranit](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) metody.