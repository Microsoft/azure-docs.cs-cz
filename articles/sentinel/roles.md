---
title: Oprávnění v Azure Sentinel | Microsoft Docs
description: Tento článek vysvětluje, jak Azure Sentinel používá řízení přístupu na základě role Azure k přiřazování oprávnění uživatelům a identifikuje povolené akce pro jednotlivé role.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/11/2021
ms.author: yelevin
ms.openlocfilehash: b64adbb63efaa4ce4781474f732bc9509d51029e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310321"
---
# <a name="permissions-in-azure-sentinel"></a>Oprávnění ve službě Azure Sentinel

Služba Azure Sentinel používá [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) k poskytování [integrovaných rolí](../role-based-access-control/built-in-roles.md) , které se dají přiřadit uživatelům, skupinám a službám v Azure.

Pomocí Azure RBAC můžete vytvářet a přiřazovat role v rámci vašeho týmu zabezpečení provozu a udělit tak odpovídající přístup k Azure Sentinel. Různé role poskytují přesnější kontrolu nad tím, co můžou uživatelé Azure Sentinel zobrazit a dělat. Role Azure se dají přiřadit přímo v pracovním prostoru Azure Sentinel (viz poznámku níže) nebo v předplatném nebo skupině prostředků, do které pracovní prostor patří. to znamená, že Azure Sentinel zdědí.

## <a name="roles-for-working-in-azure-sentinel"></a>Role pro práci v režimu Sentinel Azure

### <a name="azure-sentinel-specific-roles"></a>Role specifické pro Azure Sentinel

**Všechny předdefinované role Azure Sentinel udělují přístup pro čtení k datům v pracovním prostoru Sentinel Azure.**

- [Čtenář služby Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) může zobrazit data, incidenty, sešity a další prostředky služby Azure Sentinel.

- [Reagující služby Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-responder) může kromě výše uvedeného spravovat incidenty (přiřazovat je, zavírat je atd.).

- [Přispěvatel služby Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) může kromě výše uvedeného vytvářet a upravovat sešity, analytická pravidla a další prostředky služby Azure Sentinel.

- [Přispěvatel Azure Sentinel Automation](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) umožňuje, aby Azure Sentinel přidal playbooky do pravidel automatizace. Není určeno pro uživatelské účty.

> [!NOTE]
>
> - Nejlepších výsledků dosáhnete, když tyto role přiřadíte do **skupiny prostředků** , která obsahuje pracovní prostor Azure Sentinel. Tímto způsobem budou role platit pro všechny prostředky, které jsou nasazené pro podporu Sentinel Azure, protože tyto prostředky by se měly umístit taky do stejné skupiny prostředků.
>
> - Další možností je přiřadit role přímo do **pracovního prostoru** Azure Sentinel samotného. Pokud to uděláte, musíte také přiřadit stejné role k **prostředku řešení** SecurityInsights v daném pracovním prostoru. Je možné, že je budete muset přiřadit i k jiným prostředkům a budete muset průběžně spravovat přiřazení rolí u prostředků.

### <a name="additional-roles-and-permissions"></a>Další role a oprávnění

Uživatelům s konkrétními požadavky na úlohy může být potřeba přiřadit další role nebo specifická oprávnění, aby mohli plnit své úkoly.

- **Práce s playbooky pro automatizaci reakcí na hrozby**

    Sentinel Azure používá **playbooky** pro automatickou reakci na hrozby. Playbooky jsou postavené na **Azure Logic Apps** a jsou samostatným prostředkem Azure. Je možné, že budete chtít přiřadit konkrétním členům vašeho týmu operací zabezpečení možnost použít Logic Apps pro operace orchestrace, automatizace a odezva zabezpečení (společnosti). Roli [Přispěvatel aplikace logiky](../role-based-access-control/built-in-roles.md#logic-app-contributor) můžete použít k přiřazení explicitního oprávnění pro použití playbooky.

- **Připojení zdrojů dat ke službě Azure Sentinel**

    Aby mohl uživatel přidat **datové konektory**, je nutné přiřadit oprávnění pro zápis uživatele v pracovním prostoru Azure Sentinel. Také si poznamenejte požadovaná další oprávnění pro každý konektor, jak je uvedeno na příslušné stránce konektoru.

- **Uživatelé typu Host přiřazují incidenty**

    Pokud uživatel typu Host potřebuje, aby mohl přiřadit incidenty, pak kromě role respondérů služby Azure Sentinel bude muset uživateli také přiřadit roli [čtečky adresářů](../active-directory/roles/permissions-reference.md#directory-readers). Všimněte si, že tato *role není role* Azure, ale role **Azure Active Directory** a tento běžný uživatel (bez hosta) má tuto roli přiřazenou ve výchozím nastavení.

- **Vytváření a odstraňování sešitů**

    Aby uživatel mohl vytvořit a odstranit sešit s Sentinelou v Azure, bude muset být také přiřazen k roli Azure Monitor [Přispěvatel monitorování](../role-based-access-control/built-in-roles.md#monitoring-contributor). Tato role není nutná pro *používání* sešitů, ale pouze pro vytvoření a odstranění.

### <a name="other-roles-you-might-see-assigned"></a>Další role, které se můžou zobrazit

V rámci přiřazování rolí Azure pro konkrétní službu Azure Sentinel můžete přijít do jiných rolí Azure a Log Analytics Azure, které mohou být přiřazeny uživatelům pro jiné účely. Měli byste si uvědomit, že tyto role poskytují širší sadu oprávnění, která zahrnuje přístup k pracovnímu prostoru Azure Sentinel a dalším prostředkům:

- **Role Azure:** [vlastník](../role-based-access-control/built-in-roles.md#owner), [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor)a [Čtenář](../role-based-access-control/built-in-roles.md#reader). Role Azure udělují přístup napříč všemi prostředky Azure, včetně Log Analyticsch pracovních prostorů a prostředků Sentinel Azure.

- **Log Analytics role:** [Log Analytics Přispěvatel](../role-based-access-control/built-in-roles.md#log-analytics-contributor) a [Log Analytics Reader](../role-based-access-control/built-in-roles.md#log-analytics-reader). Role Log Analytics poskytují přístup k pracovním prostorům Log Analytics.

Například uživatel, který má přiřazenou roli správce **Sentinel Azure** , ale ne roli **Přispěvatel Sentinel Azure** , bude i nadále moci upravovat položky v Azure Sentinel, pokud má přiřazenou roli **Přispěvatel** na úrovni Azure. Proto pokud chcete uživateli udělit oprávnění pouze v rámci Azure Sentinel, měli byste pečlivě odebrat předchozí oprávnění tohoto uživatele a ujistit se, že nebudete mít potřebný přístup k jinému prostředku.

## <a name="azure-sentinel-roles-and-allowed-actions"></a>Role Azure Sentinelu a povolené akce

Následující tabulka shrnuje role Sentinel Azure a jejich povolené akce v Azure Sentinel.

| Role | Vytváření a spouštění playbooků| Vytvoření a úprava pravidel pro analýzu a dalších prostředků Sentinel Azure [*](#workbooks) | Spravovat incidenty (zavřít, přiřadit atd.) | Zobrazení dat, incidentů, sešitů a dalších prostředků Sentinel Azure |
|---|---|---|---|---|
| Azure Sentinel Reader (čtenář) | -- | -- | -- | &#10003; |
| Azure Sentinel Responder | -- | -- | &#10003; | &#10003; |
| Azure Sentinel Contributor (přispěvatel) | -- | &#10003; | &#10003; | &#10003; |
| Přispěvatel Azure Sentinel + Přispěvatel aplikace logiky | &#10003; | &#10003; | &#10003; | &#10003; |
| | | | | |

<a name=workbooks></a>* Vytváření a odstraňování sešitů vyžaduje další roli [Přispěvatel monitorování](../role-based-access-control/built-in-roles.md#monitoring-contributor) . Další informace najdete v tématu [Další role a oprávnění](#additional-roles-and-permissions).
## <a name="custom-roles-and-advanced-azure-rbac"></a>Vlastní role a pokročilé řízení přístupu k Azure na základě role

- **Vlastní role**. Kromě toho můžete pomocí předdefinovaných rolí Azure vytvořit vlastní role Azure pro Sentinel. Vlastní role Azure Sentinel pro Azure Sentinel jsou vytvořené stejným způsobem jako jiné [vlastní role](../role-based-access-control/custom-roles-rest.md#create-a-custom-role)Azure, a to na základě [konkrétních oprávnění pro službu Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) a [prostředků Azure Log Analytics](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

- **Log Analytics RBAC**. Můžete použít Log Analytics pokročilé řízení přístupu na základě role Azure napříč daty v pracovním prostoru Azure Sentinel. To zahrnuje jak Azure RBAC, tak i kontextu prostředků Azure RBAC v závislosti na typu dat. Další informace naleznete v tématu:

    - [Správa dat protokolu a pracovních prostorů v Azure Monitor](../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions)

    - [Zdroj-kontext RBAC pro službu Azure Sentinel](resource-context-rbac.md)
    - [Řízení přístupu na základě role (RBAC) na úrovni tabulky](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)

    V kontextu prostředků a RBAC na úrovni tabulky jsou dvě metody poskytnutí přístupu ke konkrétním datům v pracovním prostoru Sentinel Azure bez povolení přístupu k celému prostředí Sentinel Azure.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak pracovat s rolemi pro uživatele Azure Sentinel a co jednotlivé role umožňují uživatelům.

Projděte si blogové příspěvky o zabezpečení a dodržování předpisů Azure na [blogu Sentinel Azure](https://aka.ms/azuresentinelblog).
