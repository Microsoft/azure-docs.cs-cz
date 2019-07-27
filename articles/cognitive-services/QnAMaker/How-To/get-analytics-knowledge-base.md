---
title: Analýzy na základě znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Pokud jste povolili App Insights během vytváření služby QnA Maker, QnA Maker ukládá všechny protokoly chatu a další telemetrie. Spusťte Ukázkové dotazy pro získání protokolů chatu z App Insights.
services: cognitive-services
author: diberry
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 5c729065076f5dc9f25189632f42ed565a72df8a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563096"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Získání analýz pro znalostní bázi

Pokud jste povolili App Insights během [vytváření služby QnA maker](./set-up-qnamaker-service-azure.md), QnA maker ukládá všechny protokoly chatu a další telemetrie. Spusťte Ukázkové dotazy pro získání protokolů chatu z App Insights.

1. Přejít na prostředek služby App Insights.

    ![Vyberte prostředek Application Insights.](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Vyberte **Analytics**. Otevře se nové okno, kde se můžete dotazovat QnA Maker telemetrie.

    ![Výběr analýzy](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. Vložte následující dotaz a spusťte ho.

    ```query
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration, performanceBucket
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId 
    ```

    Vyberte **Spustit** a spusťte dotaz.

    ![Spustit dotaz](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Spouštění dotazů pro další analýzy na QnA Maker znalostní bázi Knowledge Base

### <a name="total-90-day-traffic"></a>Celkový počet 90 dní provozu

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Celkový provoz dotazů v daném časovém období

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Přenos uživatelů

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Latence při distribuci otázek

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Správa klíčů](./key-management.md)
