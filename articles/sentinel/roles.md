---
title: Oprávnění v Azure Sentinelu | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak Azure Sentinel používá řízení přístupu na základě rolí k přiřazení oprávnění uživatelům a identifikuje povolené akce pro každou roli.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587171"
---
# <a name="permissions-in-azure-sentinel"></a>Oprávnění v Azure Sentinelu

Azure Sentinel používá [řízení přístupu na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md), k poskytování [předdefinovaných rolí,](../role-based-access-control/built-in-roles.md) které lze přiřadit uživatelům, skupinám a službám v Azure.

Pomocí RBAC můžete použít a vytvořit role v rámci týmu operací zabezpečení udělit odpovídající přístup k Azure Sentinel. Na základě rolí máte jemně odstupňovanou kontrolu nad tím, co uživatelé s přístupem k Azure Sentinelu uvidí. Role RBAC můžete přiřadit přímo v pracovním prostoru Azure Sentinelnebo k předplatnému nebo skupině prostředků, do které pracovní prostor patří.

Existují tři konkrétní integrované role Azure Sentinelu.  
**Všechny předdefinované role Azure Sentinelu udělují přístup pro čtení k datům ve vašem pracovním prostoru Azure Sentinelu.**
- [Čtečka Azure Sentinelu](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel respondér](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Přispěvatel Azure Sentinelu](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Kromě vyhrazených rolí RBAC Azure Sentinelu existují role RBAC Azure a Log Analytics, které můžou udělit širší sadu oprávnění, která zahrnují přístup k vašemu pracovnímu prostoru Azure Sentinelu a dalším prostředkům:

- **Role Azure:** [Vlastník](../role-based-access-control/built-in-roles.md#owner), [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor)a [Reader](../role-based-access-control/built-in-roles.md#reader). Role Azure udělují přístup napříč všemi prostředky Azure, včetně pracovních prostorů Log Analytics a prostředků Azure Sentinelu.

-   **Role Log Analytics:** [Přispěvatel analýzy protokolů](../role-based-access-control/built-in-roles.md#log-analytics-contributor), [Čtečka analýzy protokolů](../role-based-access-control/built-in-roles.md#log-analytics-reader). Role Log Analytics udělují přístup ve všech pracovních prostorech Služby Log Analytics. 

> [!NOTE]
> Role Log Analytics také udělit přístup pro čtení ve všech prostředcích Azure, ale bude pouze přiřadit oprávnění k zápisu prostředkům Log Analytics.


Například uživatel, který je přiřazen ý **čtečku Azure Sentinelu** a **přispěvatel Azure** (ne přispěvatel **Azure )** role, bude moct upravovat data v Azure Sentinelu, i když mají jenom oprávnění pro **čtení Sentinelu.** Proto pokud chcete udělit oprávnění pouze v Azure Sentinelu, měli byste pečlivě odebrat předchozí oprávnění tohoto uživatele a ujistěte se, že nepřerušíte žádnou roli potřebné oprávnění pro jiný prostředek.

> [!NOTE]
>- Azure Sentinel používá playbooky pro automatickou reakci na hrozby. Playbooky využívají Azure Logic Apps a jsou samostatným prostředkem Azure. Můžete chtít přiřadit konkrétní členy vašeho týmu operací zabezpečení s možností použití logic apps pro orchestraci zabezpečení, automatizaci a odpovědi (SOAR) operace. Roli [přispěvatele aplikace Logika app](../role-based-access-control/built-in-roles.md#logic-app-contributor) nebo role [operátoru aplikace logiky](../role-based-access-control/built-in-roles.md#logic-app-operator) můžete přiřadit explicitní oprávnění pro používání playbooků.
>- Chcete-li přidat datové konektory, potřebné role pro každý konektor jsou podle typu konektoru a jsou uvedeny na příslušné stránce konektoru. Kromě toho, aby bylo možné připojit libovolný zdroj dat, musíte mít oprávnění k zápisu do pracovního prostoru Azure Sentinel.



## <a name="roles-and-allowed-actions"></a>Role a povolené akce

V následující tabulce jsou zobrazeny role a povolené akce v Azure Sentinelu. X označuje, že akce je povolena pro tuto roli.

| Role | Vytváření a spouštění playbooků| Vytváření a úpravy řídicích panelů, analytických pravidel a dalších prostředků Azure Sentinelu | Správa incidentů (zavření, přiřazení atd.) | Zobrazení dat, incidentů, řídicích panelů a dalších prostředků Azure Sentinelu |
|--- |---|---|---|---|
| Čtečka Azure Sentinelu | -- | -- | -- | × |
| Azure Sentinel respondér|--|--| × | × |
| Přispěvatel Azure Sentinelu | -- | × | × | × |
| Přispěvatel Azure Sentinel + přispěvatel aplikace logiky | × | × | × | × |


> [!NOTE]
> - Doporučujeme přiřadit uživatelům tu nejvíc omezenou roli, kterou ke své práci potřebují. Roli přispěvatele Azure Sentinelu přiřaďte například jenom uživatelům, kteří potřebují vytvářet pravidla nebo řídicí panely.
> - Doporučujeme nastavit oprávnění pro Azure Sentinel v oboru skupiny prostředků, aby uživatel mohl mít přístup ke všem pracovním prostorům Azure Sentinelu ve stejné skupině prostředků.
>
## <a name="building-custom-rbac-roles"></a>Vytváření vlastních rolí RBAC

Kromě nebo místo použití předdefinovaných rolí RBAC můžete vytvořit vlastní role RBAC pro Azure Sentinel. Vlastní role RBAC pro Azure Sentinel se vytvářejí stejným způsobem, jakým vytváříte další vlastní role [Azure RBAC,](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) na základě [konkrétních oprávnění k Azure Sentinelu](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) a [prostředkům Azure Log Analytics](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Rozšířené řízení přístupu na základě role k datům uloženým ve službě Azure Sentinel
  
Můžete použít Log Analytics pokročilé řízení přístupu na základě rolí napříč daty v pracovním prostoru Azure Sentinel. To zahrnuje řízení přístupu na základě rolí podle datového typu a řízení přístupu založené na rolích na základě prostředků. Další informace o rolích Analýzy protokolů najdete [v tématu Správa dat protokolu a pracovních prostorů v Azure Monitoru](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se naučili pracovat s rolemi pro uživatele Azure Sentinelu a co jednotlivé role umožňuje uživatelům dělat.

* [Blog Azure Sentinel](https://aka.ms/azuresentinelblog). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
