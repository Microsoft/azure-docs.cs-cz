---
title: Užitečné prostředky při práci s Azure Sentinel| Dokumenty společnosti Microsoft
description: Tento dokument poskytuje seznam užitečných prostředků při práci s Azure Sentinel.
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
ms.openlocfilehash: 2110d3319cebf693ef06deec26a29fa655e35035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585267"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Užitečné prostředky pro práci s Azure Sentinelem



Tento článek uvádí prostředky, které vám můžou pomoct získat další informace o práci s Azure Sentinelem.

Konektory Azure Logic Apps:<https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Audit a podávání zpráv
Protokoly auditu Azure Sentinelu se udržují v [protokolech aktivit Azure](../azure-monitor/platform/platform-logs-overview.md).

Auditovat lze následující podporované operace.

|Název operace|    Typ prostředku|
|----|----|
|Vytvoření nebo aktualizace sešitu  |Microsoft.Insights/sešity|
|Odstranit sešit    |Microsoft.Insights/sešity|
|Nastavit pracovní postup   |Microsoft.Logic/pracovní postupy|
|Odstranit pracovní postup    |Microsoft.Logic/pracovní postupy|
|Vytvořit uložené hledání    |Microsoft.OperationalInsights/pracovní prostory/uloženáVyhledávání|
|Odstranit uložené hledání    |Microsoft.OperationalInsights/pracovní prostory/uloženáVyhledávání|
|Aktualizovat pravidla výstrah |Microsoft.SecurityInsights/alertRules|
|Odstranit pravidla výstrah |Microsoft.SecurityInsights/alertRules|
|Aktualizovat akce odpovědi na pravidlo výstrahy |Microsoft.SecurityInsights/alertRules/actions|
|Odstranit akce odpovědi na pravidlo výstrahy |Microsoft.SecurityInsights/alertRules/actions|
|Aktualizovat záložky   |Microsoft.SecurityInsights/záložky|
|Odstranit záložky   |Microsoft.SecurityInsights/záložky|
|Aktualizovat případy   |Microsoft.SecurityInsights/Případy|
|Aktualizovat vyšetřování případu  |Microsoft.SecurityInsights/Případy/vyšetřování|
|Vytvořit poznámky k případu   |Microsoft.SecurityInsights/Případy/komentáře|
|Aktualizovat datové konektory |Microsoft.SecurityInsights/dataConnectors|
|Odstranění datových konektorů |Microsoft.SecurityInsights/dataConnectors|
|Aktualizovat nastavení    |Microsoft.SecurityInsights/nastavení|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Zobrazení dat auditu a sestav v Azure Sentinelu

Tato data můžete zobrazit tak, že je streamujete z protokolu aktivit Azure do Azure Sentinelu, kde pak můžete provádět výzkum a analýzy na něm.

1. Připojte zdroj dat [aktivity Azure.](connect-azure-activity.md) Poté se události auditu streamují do nové tabulky na obrazovce **Protokoly** s názvem AzureActivity.
2. Potom dotaz na data pomocí KQL, stejně jako u jakékoli jiné tabulky.



## <a name="vendor-documentation"></a>Dokumentace dodavatele

| **Dodavatel**  | **Použití incidentu v Azure Sentinelu** | **Odkaz**|
|----|----|----|
| GitHub| Slouží k přístupu na stránku komunity| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| Konfigurace CeF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Kurz dotazovacího jazyka Kusto| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogy a fóra

Zveřejněte své dotazy v [oblasti TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) pro Azure Sentinel.

Prohlédněte si blogové příspěvky Azure Sentinelu z [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) a [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/).


## <a name="next-steps"></a>Další kroky
V tomto dokumentu máte seznam prostředků, které jsou užitečné při práci s Azure Sentinel. Další informace o zabezpečení Azure a dodržování předpisů najdete na [blogu Microsoft Azure Security and Compliance](https://blogs.msdn.com/b/azuresecurity/).
