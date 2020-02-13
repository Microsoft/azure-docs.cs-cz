---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 645494688ed1fb2a29efa083d50e9456f6d2b1b4
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172727"
---
|Název |Popis |Vliv (s) |Verze |
|---|---|---|---|
|[Měly by být povolené diagnostické protokoly v Azure Data Lake Store.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |Audituje povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |2.0.0 |
|[Měly by být povolené diagnostické protokoly v Data Lake Analytics.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |Audituje povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |2.0.0 |
|[Vyžadovat šifrování u Data Lake Store účtů](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |Tato zásada zajistí, že se na všech účtech Data Lake Store povolí šifrování. |odmítnout |1.0.0 |
