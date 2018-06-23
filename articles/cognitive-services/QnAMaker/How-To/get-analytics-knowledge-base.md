---
title: Jak získat analýzy vašich znalostí základní - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Jak získat analytics na znalostní báze
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 1588d0c5a8eaf4e161b5319c9f33a772dc56b247
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343237"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Získat analytics na znalostní báze

QnA Maker ukládá všechny protokoly chatu a další telemetrií, pokud jste povolili App Insights během [vytvoření služby QnA Maker](./set-up-qnamaker-service-azure.md). Spuštění ukázkových dotazů k získání protokolů chat z Statistika aplikace.

1. Přejděte do zdroje App Insights.

    ![Vyberte přehledy prostředek vaší aplikace](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Vyberte **Analytics**. Nové okno otevře kde se můžete dotazovat QnA Maker telemetrie.

    ![Vyberte Analytics](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. Vložte následující dotaz a spusťte ji.

    ```query
        requests
        | where url endswith "generateAnswer"
        | project timestamp, id, name, resultCode, duration
        | parse name with *"/knowledgebases/"KbId"/generateAnswer"
        | join kind= inner (
        traces | extend id = operation_ParentId
        ) on id
        | extend question = tostring(customDimensions['Question'])
        | extend answer = tostring(customDimensions['Answer'])
        | project KbId, timestamp, resultCode, duration, question, answer
    ```

    Vyberte **spustit** spusťte dotaz.

    ![Spuštění dotazu](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Spuštění dotazů pro další analýzu na QnA Maker znalostní báze

### <a name="total-90-day-traffic"></a>Celkový provoz-90denní

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
``` 

### <a name="total-question-traffic-in-a-given-time-period"></a>Celkový počet otázek provoz v daném časovém období

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Provozu generovaného uživateli

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse name with *"/knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Distribuce latence otázek

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Správa klíčů](./key-management.md)
