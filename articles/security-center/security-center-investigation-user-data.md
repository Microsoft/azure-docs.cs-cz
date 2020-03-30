---
title: Správa uživatelských dat nalezených v šetření Azure Security Center
description: " Zjistěte, jak spravovat uživatelská data nalezená v funkci šetření Centra zabezpečení Azure. "
services: operations-management-suite
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: memildin
ms.openlocfilehash: 9e4c6577f0b8b18aff343ac54b31ff292632f5d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979244"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Správa uživatelských dat nalezených v šetření Azure Security Center
Tento článek obsahuje informace o tom, jak spravovat uživatelská data nalezená v azure security center vyšetřovací funkce. Vyšetřovací data jsou uložená v [protokolech Azure Monitor](../log-analytics/log-analytics-overview.md) a vystavena v Centru zabezpečení. Správa uživatelských dat zahrnuje možnost odstranit nebo exportovat data.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Vyhledávání a identifikace osobních údajů
Na webu Azure Portal můžete k vyhledávání osobních údajů použít [funkci šetření](../security-center/security-center-investigation.md) Centra zabezpečení. Funkce šetření je k dispozici v části **Výstrahy zabezpečení**.

Funkce šetření zobrazuje všechny entity, informace o uživateli a data na kartě **Entity.**

## <a name="securing-and-controlling-access-to-personal-information"></a>Zabezpečení a řízení přístupu k osobním údajům
Uživatel Centra zabezpečení, který má přiřazenou roli čtenáře, vlastníka, přispěvatele nebo správce účtu, má v nástroji přístup k zákaznickým datům.

Další informace o rolích Čtečky, Vlastníka a Přispěvatel najdete v tématu [Předdefinované role pro řízení přístupu na základě rolí Azure.](../role-based-access-control/built-in-roles.md) Další informace o roli Správce účtu najdete v [tématu Správci předplatného Azure.](../cost-management-billing/manage/add-change-subscription-administrator.md)

## <a name="deleting-personal-data"></a>Odstranění osobních dat
Uživatel Centra zabezpečení, který přiřadil roli vlastníka, přispěvatele nebo správce účtu, může informace o šetření odstranit.

Chcete-li odstranit šetření, `DELETE` můžete odeslat požadavek na rozhraní REST Azure Resource Manager:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

Vstup `incidentName` lze nalézt výpisem všech `GET` incidentů pomocí požadavku:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Export osobních dat
Uživatel Centra zabezpečení, který má přiřazenou roli vlastníka, přispěvatele nebo správce účtu, může informace o šetření exportovat. Chcete-li exportovat informace o šetření, přejděte na kartu **Entity** a zkopírujte příslušné informace.

## <a name="next-steps"></a>Další kroky
Další informace o správě uživatelských dat najdete [v tématu Správa uživatelských dat v Centru zabezpečení Azure](security-center-privacy.md).
Další informace o odstraňování privátních dat v protokolech Azure Monitoru najdete v tématu [Export a odstranění soukromých dat](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).
