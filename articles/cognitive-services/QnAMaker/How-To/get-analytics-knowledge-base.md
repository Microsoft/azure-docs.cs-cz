---
title: Analýzy na webech
titleSuffix: Azure Cognitive Services
description: Nástroj QnA Maker ukládá všechny protokoly chatu a další telemetrie, pokud je povoleno App Insights při vytváření služby QnA Maker. Spuštění ukázkových dotazů k získání protokolů chatu z App Insights.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 28dd34cc49d0004dd434a54d53f3f27f7c7d80e7
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542698"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Získání analýz pro znalostní bázi

Nástroj QnA Maker ukládá všechny protokoly chatu a další telemetrie, pokud je povoleno App Insights během [vytvoření vaší služby QnA Maker](./set-up-qnamaker-service-azure.md). Spuštění ukázkových dotazů k získání protokolů chatu z App Insights.

1. Přejděte na váš prostředek App Insights.

    ![Vyberte váš prostředek application insights](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Vyberte **Analytics**. Nové okno otevře ve kterém můžete dát dotaz na telemetrii nástroj QnA Maker.

    ![Vyberte Analytics](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. Vložte do ní následující dotaz a spustíme ji.

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

    Vyberte **spustit** ke spuštění dotazu.

    ![Spuštění dotazu](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Spouštějte dotazy pro další analýzy na bázi QnA Maker

### <a name="total-90-day-traffic"></a>Celkového provozu, kterým 90 dní

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
``` 

### <a name="total-question-traffic-in-a-given-time-period"></a>Celkový počet otázku provoz v daném časovém období

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

### <a name="user-traffic"></a>Uživatelský provoz

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

### <a name="latency-distribution-of-questions"></a>Distribuce latence u dotazů

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
