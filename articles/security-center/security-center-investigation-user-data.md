---
title: Spravovat uživatelská data v Azure Security Center šetření nalezen | Microsoft Docs
description: " Naučte se spravovat uživatelská data v Azure Security Center šetření funkce najít. "
services: operations-management-suite
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: terrylan
ms.openlocfilehash: 6685db4eeda72928753c74c64b4b26539ccb1eb9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654989"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Spravovat uživatelská data v Azure Security Center šetření nalezen
Tento článek obsahuje informace o tom, jak spravovat data uživatele v Azure Security Center šetření funkce. Zkoumání data jsou uložena v [Azure Log Analytics](../log-analytics/log-analytics-overview.md) a zveřejněný ve službě Security Center. Správa uživatelských dat zahrnuje možnost odstranit nebo exportovat data.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Hledání a identifikaci osobní data
Na portálu Azure Security Center můžete [šetření funkce](../security-center/security-center-investigation.md) k vyhledání osobní data. Zkoumání funkce je k dispozici v části **výstrahy zabezpečení**.

Funkci šetření zobrazuje všechny entity, informace o uživateli a data v části **entity** kartě.

## <a name="securing-and-controlling-access-to-personal-information"></a>Zabezpečení a řízení přístupu k osobním údajům
Security Center uživatel přiřazenou roli čtečky, vlastník, Přispěvatel nebo správce účtu může přistupovat k datům zákazníka v rámci nástroje.

V tématu [předdefinované role pro řízení přístupu Azure na základě rolí](../role-based-access-control/built-in-roles.md) Další informace o rolích čtečky, vlastník a Přispěvatel. V tématu [správci předplatného Azure](../billing/billing-add-change-azure-subscription-administrator.md) Další informace o roli správce účtu.

## <a name="deleting-personal-data"></a>Odstranění osobní data
Security Center uživatel přiřazenou roli vlastník, Přispěvatel, nebo účet správce, můžete odstranit informace šetření.

Pokud chcete odstranit šetření, můžete odeslat `DELETE` požadavku REST API pro Azure Resource Manager:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

`incidentName` Vstup najdete tak, že uvedete všechny incidenty pomocí `GET` žádost:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Export osobní data
Security Center uživatel přiřazenou roli vlastník, Přispěvatel, nebo účet správce může exportovat informace o šetření. Pokud chcete exportovat informace o šetření, přejděte na **entity** a zkopírujte a vložte příslušné informace.

## <a name="next-steps"></a>Další postup
Další informace o správě uživatelských dat najdete v tématu [správu dat uživatele v Azure Security Center](security-center-privacy.md).
