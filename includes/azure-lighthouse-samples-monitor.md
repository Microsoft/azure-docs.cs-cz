---
title: zahrnout soubor
description: zahrnout soubor
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 03/30/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: aacf1c287dca01b5993d35b311eed22e88ef7e87
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421139"
---
Tyto ukázky ukazují, jak používat Azure Monitor k vytváření upozornění pro předplatná, která jsou nasazená pro správu delegovaných prostředků Azure.

| **Šablona** | **Popis** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/monitor-delegation-changes) | Dotazuje se na poslední 1 den aktivity ve spravujícím tenantovi a [hlásí všechna přidaná nebo odebraná delegování](../articles/lighthouse/how-to/monitor-delegation-changes.md) (nebo pokusy, které nebyly úspěšné).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Tato šablona vytvoří upozornění Azure a připojí ho k existující skupině akcí.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Vytvoří několik upozornění protokolu na základě dotazů Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Nasadí upozornění do tenanta, když uživatel deleguje předplatné spravovanému tenantovi.|
