---
title: Vyčištění úlohy Azure Stream Analytics
description: Tento článek ukazuje různé metody pro odstranění úloh Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d99920417f20034da1001a821c02376ac19274d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426488"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Zastavení nebo odstranění úlohy Azure Stream Analytics

Úlohy Azure Stream Analytics se dá snadno zastavit nebo odstranit prostřednictvím portálu Azure Portal, Azure PowerShell, Azure SDK pro rozhraní .Net nebo REST API. Úlohu Stream Analytics nelze obnovit, jakmile byla odstraněna.

>[!NOTE] 
>Když zastavíte úlohu Stream Analytics, data zůstanou zachována jenom ve vstupním a výstupním úložišti, jako jsou centra událostí nebo Azure SQL Database. Pokud jste povinni odebrat data z Azure, nezapomeňte postupovat podle procesu odebrání pro vstupní a výstupní prostředky úlohy Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Zastavení úlohy na Webu Azure Portal

Když zastavíte úlohu, prostředky jsou zrušeny a zastaví zpracování událostí. Poplatky související s touto úlohou jsou také zastaveny. Nicméně všechny vaše konfigurace jsou zachovány a můžete restartovat úlohu později 

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 

2. Vyhledejte spuštěnou úlohu Stream Analytics a vyberte ji.

3. Na stránce úlohy Stream Analytics vyberte **Zastavit,** chcete-li úlohu zastavit. 

   ![Zastavení úlohy Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Odstranění úlohy na webu Azure Portal

>[!WARNING] 
>Úlohu Stream Analytics nelze obnovit, jakmile byla odstraněna.

1. Přihlaste se k portálu Azure. 

2. Vyhledejte stávající úlohu Stream Analytics a vyberte ji.

3. Na stránce Úloha Stream Analytics vyberte **Odstranit,** chcete-li úlohu odstranit. 

   ![Odstranění úlohy Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Zastavení nebo odstranění úlohy pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Chcete-li zastavit úlohu pomocí prostředí PowerShell, použijte rutinu [Stop-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) Chcete-li odstranit úlohu pomocí prostředí PowerShell, použijte rutinu [Remove-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob)

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Zastavení nebo odstranění úlohy pomocí sady Azure SDK pro rozhraní .NET

Chcete-li zastavit úlohu pomocí sady Azure SDK pro rozhraní .NET, použijte metodu [StreamingJobsOperationsExtensions.BeginStop.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) Chcete-li odstranit úlohu pomocí sady Azure SDK pro rozhraní .NET, [metodu StreamingJobsOperationsExtensions.BeginDelete.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet)

## <a name="stop-or-delete-a-job-using-rest-api"></a>Zastavení nebo odstranění úlohy pomocí rozhraní REST API

Chcete-li zastavit úlohu pomocí rozhraní REST API, naleznete [stop](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) metoda. Chcete-li odstranit úlohu pomocí rozhraní REST API, podívejte se na [Delete](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) metoda.
