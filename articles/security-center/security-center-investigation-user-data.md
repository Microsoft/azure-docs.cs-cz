---
title: Spravovat uživatelská data v Azure Security Center šetření | Dokumentace Microsoftu
description: " Další informace o správě dat uživatele v Azure Security Center funkci šetření. "
services: operations-management-suite
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: operations-management-suite
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: rkarlin
ms.openlocfilehash: d9bf3618b32ce7c5ba7a300a929428dd0614743d
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52276240"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Spravovat uživatelská data v Azure Security Center šetření
Tento článek obsahuje informace o tom, jak spravovat uživatelská data v Azure Security Center funkci šetření. Šetření data jsou uložena v [Azure Log Analytics](../log-analytics/log-analytics-overview.md) a vystavené ve službě Security Center. Správa uživatelských dat zahrnuje možnost odstranit nebo exportovat data.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Hledání a identifikovat osobní údaje
Na webu Azure Portal, můžete použít Centrum zabezpečení [pročtěte](../security-center/security-center-investigation.md) k vyhledání osobních údajů. Funkce šetření je k dispozici v rámci **výstrahy zabezpečení**.

Funkce šetření zobrazí všechny entity, informace o uživateli a data v rámci **entity** kartu.

## <a name="securing-and-controlling-access-to-personal-information"></a>Zabezpečení a řízení přístupu k osobním údajům
Security Center uživatel přiřazenou roli Čtenář, vlastník, Přispěvatel nebo správce účtu může přístup k zákaznickým datům v rámci nástroje.

Zobrazit [předdefinované role pro řízení přístupu na základě rolí Azure](../role-based-access-control/built-in-roles.md) Další informace o rolích Čtenář, vlastníka a Přispěvatel. Zobrazit [správci předplatného Azure](../billing/billing-add-change-azure-subscription-administrator.md) získat další informace o roli správce účtu.

## <a name="deleting-personal-data"></a>Odstraňování osobních údajů
Security Center uživatel přiřazenou roli vlastník, Přispěvatel, nebo účet správce může odstranit informace šetření.

Pokud chcete odstranit šetření, můžete odeslat `DELETE` požadavek na rozhraní REST API Azure Resource Manageru:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

`incidentName` Vstup můžete zobrazit tak seznam všech incidentů pomocí `GET` žádosti:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Exportování osobních údajů
Security Center uživatel přiřazenou roli vlastník, Přispěvatel, nebo účet správce může exportovat informace šetření. Pokud chcete exportovat informace šetření, přejděte na **entity** kartu kopírovat a vkládat příslušné informace.

## <a name="next-steps"></a>Další postup
Další informace o správě uživatelských dat, naleznete v tématu [správu dat uživatele ve službě Azure Security Center](security-center-privacy.md).
Další informace o odstraňování privátních dat v Log Analytics najdete v tématu [jak exportovat a odstranění dat soukromých](../log-analytics/log-analytics-personal-data-mgmt.md#how-to-export-and-delete-private-data).
