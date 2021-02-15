---
title: Užitečné prostředky při práci se službou Azure Sentinel | Microsoft Docs
description: Tento dokument vám poskytne seznam užitečných prostředků při práci s Sentinel Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2021
ms.author: yelevin
ms.openlocfilehash: 2a6e31115fb2548f8248f741213970605f230036
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390721"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Užitečné prostředky pro práci s Sentinel Azure



V tomto článku najdete seznam prostředků, které vám pomůžou získat další informace o práci s Sentinel Azure Sentinel.

- **Konektory Azure Logic Apps**: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Auditování a vytváření sestav
Protokoly auditu Azure Sentinel se udržují v [protokolech aktivit Azure](../azure-monitor/platform/platform-logs-overview.md).

Můžete auditovat následující podporované operace.

|Název operace|    Typ prostředku|
|----|----|
|Vytvořit nebo aktualizovat sešit  |Microsoft. Insights/sešity|
|Odstranit sešit    |Microsoft. Insights/sešity|
|Nastavit pracovní postup   |Microsoft. Logic/Workflows|
|Odstranit pracovní postup    |Microsoft. Logic/Workflows|
|Vytvořit uložené výsledky hledání    |Microsoft. OperationalInsights/pracovní prostory/savedSearches|
|Odstranit uložené výsledky hledání    |Microsoft. OperationalInsights/pracovní prostory/savedSearches|
|Aktualizovat pravidla upozornění |Microsoft. SecurityInsights/alertRules|
|Odstranit pravidla výstrah |Microsoft. SecurityInsights/alertRules|
|Aktualizace akcí reakce na pravidlo upozornění |Microsoft. SecurityInsights/alertRules/Actions|
|Odstranit akce reakce pravidla výstrahy |Microsoft. SecurityInsights/alertRules/Actions|
|Aktualizovat záložky   |Microsoft. SecurityInsights/záložky|
|Odstranit záložky   |Microsoft. SecurityInsights/záložky|
|Aktualizační případy   |Microsoft. SecurityInsights/případy|
|Aktualizovat případové šetření  |Microsoft. SecurityInsights/případy/vyšetřování|
|Vytvořit komentáře k případu   |Microsoft. SecurityInsights/případy/komentáře|
|Aktualizace datových konektorů |Microsoft. SecurityInsights/dataconnects|
|Odstranit datové konektory |Microsoft. SecurityInsights/dataconnects|
|Aktualizovat nastavení    |Microsoft. SecurityInsights/Settings|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Zobrazení dat pro audit a vytváření sestav v Azure Sentinel

Tato data můžete zobrazit jejich streamování z protokolu aktivit Azure do služby Azure Sentinel, kde pak můžete na svém základě provádět výzkum a analýzu.

1. Připojte zdroj dat o [aktivitě Azure](connect-azure-activity.md) . Po provedení tohoto postupu se události auditu streamují do nové tabulky na obrazovce **protokoly** s názvem AzureActivity.

1. Pak Dotazujte data pomocí KQL, stejně jako u jakékoli jiné tabulky.

    Chcete-li například zjistit, kdo byl posledním uživatelem pro úpravu konkrétního analytického pravidla, použijte následující dotaz (nahraďte `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ID pravidla pravidla, které chcete ověřit):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```


## <a name="blogs-and-forums"></a>Blogy a fóra

Rádi uslyšíme vaše uživatele!

- **Vystavte své dotazy** na [TechCommunity místo](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) pro Azure Sentinel. 

- **Pošlete návrhy na vylepšení** pomocí našeho [hlasového](https://feedback.azure.com/forums/920458-azure-sentinel) programu pro uživatele.

- **Zobrazit a komentovat** příspěvky na blogu Azure Sentinel:

    - [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) 
    - [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/)


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získat certifikaci](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [Přečíst příběhy o případech použití zákazníků](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)

