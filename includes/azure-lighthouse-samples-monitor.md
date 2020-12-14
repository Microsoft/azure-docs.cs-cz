---
title: zahrnout soubor
description: zahrnout soubor
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/11/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 0056e18b6cb3aad2a4504bbe20b3b3421793489e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356259"
---
Tyto ukázky ukazují, jak používat Azure Monitor k vytváření upozornění pro předplatná, která jsou nasazená pro správu delegovaných prostředků Azure.

| **Šablona** | **Popis** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | Dotazuje se na poslední 1 den aktivity ve spravujícím tenantovi a [hlásí všechna přidaná nebo odebraná delegování](../articles/lighthouse/how-to/monitor-delegation-changes.md) (nebo pokusy, které nebyly úspěšné).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | Tato šablona vytvoří upozornění Azure a připojí ho k existující skupině akcí.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | Vytvoří několik upozornění protokolu na základě dotazů Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | Nasadí upozornění do tenanta, když uživatel deleguje předplatné spravovanému tenantovi.|
| [workbook-activitylogs-by-domain](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) | Zobrazuje protokoly aktivit Azure napříč předplatnými s možností filtrování podle názvu domény. |
