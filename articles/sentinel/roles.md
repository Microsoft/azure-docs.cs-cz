---
title: Oprávnění v Azure Sentinel | Microsoft Docs
description: Tento článek vysvětluje, jak Azure Sentinel používá řízení přístupu na základě rolí k přiřazování oprávnění uživatelům a identifikaci povolených akcí pro jednotlivé role.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: yelevin
ms.openlocfilehash: e7629a53190433c6c331ce372476b0ed768fc5eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77587171"
---
# <a name="permissions-in-azure-sentinel"></a>Oprávnění v Azure Sentinel

Služba Azure Sentinel používá [Access Control na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md)k poskytování [předdefinovaných rolí](../role-based-access-control/built-in-roles.md) , které se dají přiřadit uživatelům, skupinám a službám v Azure.

Pomocí RBAC můžete použít a vytvořit role v rámci vašeho týmu zabezpečení provozu a udělit odpovídající přístup k Sentinel Azure. Na základě rolí máte detailní kontrolu nad tím, co můžou uživatelé s přístupem k Azure Sentinel sledovat. Role RBAC můžete přiřadit přímo do pracovního prostoru Azure Sentinel nebo do předplatného nebo skupiny prostředků, do které pracovní prostor patří.

Existují tři konkrétní předdefinované role Sentinel Azure.  
**Všechny předdefinované role Azure Sentinel udělují přístup pro čtení k datům v pracovním prostoru Sentinel Azure.**
- [Čtečka Sentinel Azure](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Respondér služby Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Přispěvatel Sentinel Azure](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Kromě vyhrazených rolí RBAC v Azure jsou k dispozici role RBAC Azure a Log Analytics, které jim umožňují poskytnout širší sadu oprávnění, která zahrnují přístup k vašemu pracovnímu prostoru Azure Sentinel a dalším prostředkům:

- **Role Azure:** [vlastník](../role-based-access-control/built-in-roles.md#owner), [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor)a [Čtenář](../role-based-access-control/built-in-roles.md#reader). Role Azure udělují přístup napříč všemi prostředky Azure, včetně Log Analyticsch pracovních prostorů a prostředků Sentinel Azure.

-   **Role Log Analytics:** [Log Analytics Přispěvatel](../role-based-access-control/built-in-roles.md#log-analytics-contributor), [Log Analytics Reader](../role-based-access-control/built-in-roles.md#log-analytics-reader). Role Log Analytics udělují přístup ve všech vašich pracovních prostorech Log Analytics. 

> [!NOTE]
> Role Log Analytics taky udělují přístup pro čtení napříč všemi prostředky Azure, ale přiřazují jenom oprávnění k zápisu do prostředků Log Analytics.


Například uživatel, který je přiřazený ke **službě Azure Sentinel Reader** a **přispěvatelům Azure** (ne **přispěvateli Azure Sentinel**), bude moct upravovat data ve službě Azure Sentinel, i když mají jenom oprávnění ke **čtení pomocí ověřovacích** dat. Proto pokud chcete udělit oprávnění pouze v rámci Azure Sentinel, měli byste pečlivě odebrat předchozí oprávnění tohoto uživatele, abyste se ujistili, že jste nenarušili žádnou potřebnou roli oprávnění pro jiný prostředek.

> [!NOTE]
>- Sentinel Azure používá playbooky pro automatickou reakci na hrozby. Playbooky využívá Azure Logic Apps a jsou samostatným prostředkem Azure. Můžete chtít přiřadit konkrétní členy týmu operací zabezpečení s možností použití Logic Apps pro operace orchestrace, automatizace a odezva zabezpečení (společnosti). Roli [Přispěvatel aplikace logiky](../role-based-access-control/built-in-roles.md#logic-app-contributor) nebo [operátora aplikace logiky](../role-based-access-control/built-in-roles.md#logic-app-operator) můžete použít k přiřazení explicitního oprávnění pro použití playbooky.
>- Pro přidání datových konektorů jsou potřebné role pro každý konektor na typ konektoru a jsou uvedeny na příslušné stránce konektoru. Aby bylo možné připojit jakýkoliv zdroj dat, musíte mít oprávnění k zápisu do pracovního prostoru Azure Sentinel.



## <a name="roles-and-allowed-actions"></a>Role a povolené akce

V následující tabulce jsou uvedeny role a povolené akce ve službě Azure Sentinel. X označuje, že akce je pro tuto roli povolena.

| Role | Vytvoření a spuštění playbooky| Vytváření a úpravy řídicích panelů, analytických pravidel a dalších prostředků Sentinel Azure | Spravovat incidenty (zavřít, přiřadit atd.) | Zobrazení dat, incidentů, řídicích panelů a dalších prostředků Sentinel Azure |
|--- |---|---|---|---|
| Čtečka Sentinel Azure | -- | -- | -- | × |
| Respondér služby Azure Sentinel|--|--| × | × |
| Přispěvatel Sentinel Azure | -- | × | × | × |
| Přispěvatel Azure Sentinel + Přispěvatel aplikace logiky | × | × | × | × |


> [!NOTE]
> - Doporučujeme přiřadit uživatelům tu nejvíc omezenou roli, kterou ke své práci potřebují. Například můžete přiřadit roli Přispěvatel Sentinel Azure jenom uživatelům, kteří potřebují vytvářet pravidla nebo řídicí panely.
> - Doporučujeme nastavit oprávnění pro službu Azure Sentinel v oboru skupiny prostředků, aby uživatel měl přístup ke všem pracovním prostorům služby Azure Sentinel ve stejné skupině prostředků.
>
## <a name="building-custom-rbac-roles"></a>Vytváření vlastních rolí RBAC

Kromě toho, nebo místo pomocí integrovaných rolí RBAC, můžete vytvořit vlastní role RBAC pro Azure Sentinel. Vlastní role RBAC pro Azure Sentinel jsou vytvořené stejným způsobem jako jiné vlastní role [Azure RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) na základě [konkrétních oprávnění pro Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) a [prostředků Azure Log Analytics](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Rozšířené řízení přístupu na základě role k datům uloženým ve službě Azure Sentinel
  
Můžete použít Log Analytics rozšířené řízení přístupu na základě rolí napříč daty v pracovním prostoru Azure Sentinel. To zahrnuje řízení přístupu na základě role na datový typ i řízení přístupu na základě rolí. Další informace o rolích Log Analytics najdete [v tématu Správa dat protokolů a pracovních prostorů v Azure monitor](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak pracovat s rolemi pro uživatele Azure Sentinel a co jednotlivé role umožňují uživatelům.

* [Blog Sentinel Azure](https://aka.ms/azuresentinelblog). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
