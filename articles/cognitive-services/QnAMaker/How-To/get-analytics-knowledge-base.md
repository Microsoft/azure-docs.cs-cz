---
title: Analýzy na základě znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Pokud jste povolili App Insights během vytváření služby QnA Maker, QnA Maker ukládá všechny protokoly chatu a další telemetrie. Spusťte Ukázkové dotazy pro získání protokolů chatu z App Insights.
services: cognitive-services
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 5f149dd6db82b66b45a4c995e2004936481af786
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96352418"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Získání analýz pro znalostní bázi

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

QnA Maker ukládá všechny protokoly chatu a další telemetrii, pokud jste povolili Application Insights při [vytváření služby QnA maker](./set-up-qnamaker-service-azure.md). Spusťte Ukázkové dotazy pro získání protokolů chatu z Application Insights.

1. Přejít na prostředek Application Insights.

    ![Vyberte prostředek Application Insights.](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Vyberte **protokol (Analytics)**. Otevře se nové okno, kde se můžete dotazovat QnA Maker telemetrie.

3. Vložte následující dotaz a spusťte ho.

    ```kusto
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, url, resultCode, duration, performanceBucket
    | parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId
    ```

    Vyberte **Spustit** a dotaz spusťte.

    [![Spustit dotaz pro určení otázek, odpovědí a skóre uživatelů](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

QnA Maker Managed (Preview) používá diagnostické protokolování Azure k ukládání dat telemetrie a protokolů chatu. Pomocí následujících kroků spusťte Ukázkové dotazy, abyste získali analýzy využití QnA Maker znalostní báze.

1. [Povolte protokolování diagnostiky](../../diagnostic-logging.md) pro službu QnA maker Managed (Preview).

2. V předchozím kroku vyberte kromě **auditu, operace RequestResponse a AllMetrics** pro protokolování možnost **trasovat** .

    ![Povolit protokolování trasování ve spravovaném QnA Maker (Preview)](../media/qnamaker-how-to-analytics-kb/qnamaker-v2-enable-trace-logging.png)

---

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Spouštění dotazů pro další analýzy na QnA Maker znalostní bázi Knowledge Base

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

### <a name="total-90-day-traffic"></a>Celkový počet 90 dní provozu

```kusto
//Total Traffic
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Celkový provoz dotazů v daném časovém období

```kusto
//Total Question Traffic in a given time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
requests
| where timestamp <= endDate and timestamp >=startDate
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Přenos uživatelů

```kusto
//User Traffic
requests
| where url endswith "generateAnswer"
| project timestamp, id, url, resultCode, duration
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend UserId = tostring(customDimensions['UserId'])
| summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Latence při distribuci otázek

```kusto
//Latency distribution of questions
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| project timestamp, id, name, resultCode, performanceBucket, KbId
| summarize count() by performanceBucket, KbId
```

### <a name="unanswered-questions"></a>Nezodpovězené otázky

```kusto
// Unanswered questions
requests
| where url endswith "generateAnswer"
| project timestamp, id, url
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend question = tostring(customDimensions['Question'])
| extend answer = tostring(customDimensions['Answer'])
| extend score = tostring(customDimensions['Score'])
| where  score  == "0" and message == "QnAMaker GenerateAnswer"
| project timestamp, KbId, question, answer, score
| order  by timestamp  desc
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

### <a name="all-qna-chat-log"></a>Protokol QnA chat

```kusto
// All QnA Traffic
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer"
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| project question_, answer_, score_, kbId_
```

### <a name="traffic-count-per-knowledge-base-and-user-in-a-time-period"></a>Počet přenosů na znalostní bázi a uživatele za časové období

```kusto
// Traffic count per KB and user in a time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer"
| where TimeGenerated <= endDate and TimeGenerated >=startDate
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| extend userId_ = tostring(parse_json(properties_s).userId)
| summarize ChatCount=count() by bin(TimeGenerated, 1d), kbId_, userId_
```

### <a name="latency-of-generateanswer-api"></a>Latence rozhraní GenerateAnswer API

```kusto
// Latency of GenerateAnswer
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="Generate Answer"
| project TimeGenerated, DurationMs
| render timechart
```

### <a name="average-latency-of-all-operations"></a>Průměrná latence všech operací

```kusto
// Average Latency of all operations
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| project DurationMs, OperationName
| summarize count(), avg(DurationMs) by OperationName
| render barchart
```

### <a name="unanswered-questions"></a>Nezodpovězené otázky

```kusto
// All unanswered questions
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer"
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| where score_ == 0
| project question_, answer_, score_, kbId_
```

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zvolit capactiy](./improve-knowledge-base.md)