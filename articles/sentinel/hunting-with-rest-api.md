---
title: Spravujte lovecké a živě dotazy ve službě Azure Sentinel pomocí REST API | Microsoft Docs
description: Tento článek popisuje, jak funkce pro lov v Azure Sentinel umožňují využít výhod Log Analytics "REST API ke správě loveckých a živě dotazů.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2020
ms.author: yelevin
ms.openlocfilehash: 64f05e18ff757d9f086cf06d74109bf64e32a05c
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98795684"
---
# <a name="manage-hunting-and-livestream-queries-in-azure-sentinel-using-rest-api"></a>Spravujte lovecké a živě dotazy ve službě Azure Sentinel pomocí REST API

Azure Sentinel, který je součástí služby Azure Monitor Log Analytics, vám umožňuje používat REST API Log Analytics ke správě loveckých a živě dotazů. V tomto dokumentu se dozvíte, jak vytvářet a spravovat lovecké dotazy pomocí REST API.  Dotazy vytvořené tímto způsobem se zobrazí v uživatelském rozhraní Azure Sentinel.

Další informace o [rozhraní API uložených hledání](/rest/api/loganalytics/savedsearches)najdete v referenčních informacích o konečném REST API.

## <a name="api-examples"></a>Příklady rozhraní API

V následujících příkladech nahraďte tyto zástupné symboly náhradou stanovenou v následující tabulce:

| Zástupný symbol | Nahradit hodnotou |
|-|-|
| **SubscriptionId** | název předplatného, na které aplikujete dotaz pro lov nebo živě. |
| **ResourceGroupName** | název skupiny prostředků, na kterou aplikujete dotaz na lov nebo živě. |
| **{savedSearchId}** | jedinečné ID (GUID) pro každý lovecký dotaz. |
| **WorkspaceName** | název pracovního prostoru Log Analytics, který je cílem dotazu. |
| **DisplayName** | Zobrazovaný název, který si zvolíte pro dotaz. |
| **Název** | Popis loveckého nebo živěho dotazu. |
| **Taktiku** | příslušné MITRE ATT&CK taktiku, které se vztahují na dotaz. |
| **Zadávání** | výraz dotazu pro dotaz |
|  

### <a name="example-1"></a>Příklad 1

V tomto příkladu se dozvíte, jak vytvořit nebo aktualizovat lovecký dotaz pro daný pracovní prostor Azure Sentinel.  V případě dotazu živě nahraďte *"Category": "lovecké dotazy"* *kategorií ":" živě dotazy "* v **textu žádosti**: 

#### <a name="request-header"></a>Hlavička požadavku

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>Text požadavku

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>Příklad 2

V tomto příkladu se dozvíte, jak odstranit lovecký nebo živě dotaz pro daný pracovní prostor Azure Sentinel:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>Příklad 3

V tomto příkladu se dozvíte, jak načíst lovecký nebo živě dotaz pro daný pracovní prostor:

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak spravovat lovecké a živě dotazy ve službě Azure Sentinel pomocí rozhraní Log Analytics API. Další informace o Sentinel Azure najdete v následujících článcích:

- [Proaktivní vylovení hrozeb](hunting.md)
- [Použití poznámkových bloků ke spouštění automatizovaných loveckých kampaní](notebooks.md)