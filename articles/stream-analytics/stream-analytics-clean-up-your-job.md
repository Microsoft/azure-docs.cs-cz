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
ms.openlocfilehash: 124d2cf4380153bd3ea68de9233f6a1b5eb6ba78
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130693"
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

K zastavení úlohy pomocí sady Azure SDK pro .NET použijte metodu [StreamingJobsOperationsExtensions. BeginStop](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) . Odstranění úlohy pomocí sady Azure SDK pro .NET, [StreamingJobsOperationsExtensions. BeginDelete](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet)

## <a name="stop-or-delete-a-job-using-rest-api"></a>Zastavení nebo odstranění úlohy pomocí REST API

Chcete-li zastavit úlohu pomocí REST API, přečtěte si metodu [stop](/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob#stop) . Pokud chcete úlohu odstranit pomocí REST API, přečtěte si metodu [Delete](/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob#delete) .