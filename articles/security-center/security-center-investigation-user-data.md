---
title: Správa uživatelských dat nalezených v Azure Security Center šetření
description: " Naučte se spravovat uživatelská data zjištěná ve vyšetřovací funkci Azure Security Center. "
services: operations-management-suite
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: memildin
ms.openlocfilehash: 82c800df03826386c8b79d45584f4b26b0bc45e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91440661"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Správa uživatelských dat nalezených v Azure Security Center šetření
Tento článek poskytuje informace o tom, jak spravovat uživatelská data zjištěná v rámci vyšetřovací funkce Azure Security Center. Data o vyšetřování jsou uložená v [protokolech Azure monitor](../log-analytics/log-analytics-overview.md) a zpřístupněna v Security Center. Správa uživatelských dat zahrnuje možnost odstraňovat nebo exportovat data.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Vyhledávání a identifikace osobních údajů
V Azure Portal můžete k hledání osobních údajů použít funkci šetření Security Center. Funkce šetření je k dispozici v části **výstrahy zabezpečení**.

Funkce šetření zobrazuje všechny entity, informace o uživateli a data na kartě **entity** .

## <a name="securing-and-controlling-access-to-personal-information"></a>Zabezpečení a řízení přístupu k osobním údajům
Uživatel Security Center přiřazený k roli Čtenář, vlastník, přispěvatel nebo účet má přístup k zákaznickým datům v nástroji.

Další informace o rolích čtenářů, vlastníků a přispěvatelů najdete v tématu [předdefinované role pro řízení přístupu na základě role v Azure](../role-based-access-control/built-in-roles.md) . Další informace o roli správce účtu najdete v tématu [Správci předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) .

## <a name="deleting-personal-data"></a>Odstranění osobních dat
Uživatel Security Center přiřazený k roli vlastníka, přispěvatele nebo správce účtu může odstranit informace o šetření.

Chcete-li odstranit šetření, můžete odeslat `DELETE` žádost na Azure Resource Manager REST API:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

`incidentName`Vstup lze najít výpisem všech incidentů pomocí `GET` žádosti:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Export osobních dat
Security Center uživatel, kterému byla přiřazena role vlastníka, přispěvatel nebo správce účtu, může exportovat informace o šetření. Pokud chcete exportovat informace o šetření, klikněte na kartu **entity** a zkopírujte a vložte relevantní informace.

## <a name="next-steps"></a>Další kroky
Další informace o správě uživatelských dat najdete v tématu [Správa uživatelských dat v Azure Security Center](security-center-privacy.md).
Další informace o odstraňování privátních dat v protokolu Azure Monitor najdete v tématu [Jak exportovat a odstranit privátní data](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).
