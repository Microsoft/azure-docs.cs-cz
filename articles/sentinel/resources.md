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
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 54c10d7941e053bec928f8f43310218d89124f9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89461727"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Užitečné prostředky pro práci s Sentinel Azure



V tomto článku najdete seznam prostředků, které vám pomůžou získat další informace o práci s Sentinel Azure Sentinel.

Konektory Azure Logic Apps: <https://docs.microsoft.com/connectors/>


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
2. Pak Dotazujte data pomocí KQL, stejně jako u jakékoli jiné tabulky.



## <a name="vendor-documentation"></a>Dokumentace dodavatele

| **Dodavatel**  | **Použít incident v Azure Sentinel** | **Propojit**|
|----|----|----|
| GitHubu| Používá se pro přístup ke stránce komunity.| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| Konfigurace CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Kurz dotazovacího jazyka Kusto| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogy a fóra

Vystavte své dotazy na [TechCommunity místo](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) pro Azure Sentinel.

Prohlédněte si blogové příspěvky Azure Sentinel z [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) a [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/).


## <a name="next-steps"></a>Další kroky
V tomto dokumentu máte seznam prostředků, které jsou užitečné, když pracujete s ověřovacími službami Azure. Další informace o zabezpečení a dodržování předpisů Azure najdete na [blogu Microsoft Azure zabezpečení a dodržování předpisů](https://docs.microsoft.com/archive/blogs/azuresecurity/).
