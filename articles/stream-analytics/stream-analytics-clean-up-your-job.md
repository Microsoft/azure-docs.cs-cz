---
title: Vyčištění úlohu Azure Stream Analytics
description: Tento článek je Průvodce pro odstranění úlohy Azure Stream Analytics.
services: stream-analytics
author: mamccrea
manager: kfile
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 4773f542f12ae1773e881106bc8948c663bfd1e3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660767"
---
# <a name="clean-up-your-azure-stream-analytics-job"></a>Vyčištění úlohu Azure Stream Analytics

Úlohy Azure Stream Analytics lze snadno odstranit prostřednictvím portálu Azure, Azure PowerShell, Azure SDK pro .net nebo REST API.

>[!NOTE] 
>Pokud zastavíte úlohu služby Stream Analytics, data trvá jenom ve vstupní a výstupní úložiště, jako je například služba Event Hubs nebo Azure SQL Database. Pokud je nutné odebrat data z Azure, nezapomeňte postupujte podle procesu odebrání pro vstupní a výstupní prostředky. úlohy Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Zastavení úlohy v portálu Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. Vaše spuštěná úloha Stream Analytics vyhledejte a vyberte ho.

3. Na stránce úlohy Stream Analytics vyberte **Zastavit** k zastavení úlohy. 

   ![Zastavit úlohu](./media/stream-analytics-clean-up-your-job/stop-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Odstranit úlohu na portálu Azure

1. Přihlaste se k portálu Azure. 

2. Vyhledejte existující úlohu služby Stream Analytics a vyberte ho.

3. Na stránce úlohy Stream Analytics vyberte **odstranit** odstranit úlohu. 

   ![Odstranit úlohu](./media/stream-analytics-clean-up-your-job/delete-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Zastavení nebo odstranit úlohu pomocí prostředí PowerShell

Chcete-li zastavit úlohu pomocí prostředí PowerShell, použijte [Stop-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob?view=azurermps-5.7.0) rutiny. Pokud chcete odstranit úlohu pomocí prostředí PowerShell, použijte [odebrat AzureRmStreamAnalyticsJob](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/Remove-AzureRmStreamAnalyticsJob?view=azurermps-5.7.0) rutiny.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Zastavení nebo odstranit úlohu pomocí sady Azure SDK pro .NET

Chcete-li zastavit úlohu pomocí sady Azure SDK pro .NET, použijte [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) metoda. Chcete-li odstranit úlohu pomocí sady Azure SDK pro platformu .NET, [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) metoda.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Zastavení nebo odstranit úlohu pomocí rozhraní REST API

Pokud chcete zastavit úlohu pomocí rozhraní REST API, naleznete [Zastavit](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-job#stop) metoda. Pokud chcete odstranit úlohu pomocí rozhraní REST API, naleznete [odstranit](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-job#delete) metoda.