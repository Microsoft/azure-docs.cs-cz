---
title: Nastavení prostředí pro operátora podrobného plánu
description: Zjistěte, jak nakonfigurovat prostředí Azure pro použití s integrovanou rolí řízení přístupu (RBAC) na základě ovládacího prvku Na základě rolí (Blueprint Operator).
ms.date: 08/26/2019
ms.topic: how-to
ms.openlocfilehash: fba0dd3f2eeb69f768800d1d04640510462d3c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873212"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>Konfigurace prostředí pro operátora podrobného plánu

Správu definic podrobného plánu a přiřazení podrobného plánu lze přiřadit různým týmům. Je běžné, že architekt nebo tým zásad správného řízení bude zodpovědný za správu životního cyklu definic podrobného plánu, zatímco operační tým je zodpovědný za správu přiřazení těchto centrálně řízených definic podrobných plánů.

**Operátor podrobného plánu** integrované řízení přístupu na základě rolí (RBAC) je navržen speciálně pro použití v tomto typu scénáře. Role umožňuje týmům typu operace spravovat přiřazení definic podrobných plánů organizací, ale nikoli možnost je upravovat. To vyžaduje určitou konfiguraci ve vašem prostředí Azure a tento článek vysvětluje nezbytné kroky.

## <a name="grant-permission-to-the-blueprint-operator"></a>Udělit oprávnění operátorovi podrobného plánu

Prvním krokem je udělení role **operátora podrobného plánu** účtu nebo skupině zabezpečení (doporučeno), která bude přiřazovat podrobné plány. Tato akce by měla být provedena na nejvyšší úrovni v hierarchii skupiny pro správu, která zahrnuje všechny skupiny pro správu a odběry, ke kterým by měl mít operační tým přístup k podrobnému plánu. Při udělování těchto oprávnění doporučujeme dodržovat zásadu nejnižších oprávnění.

1. (Doporučeno) [Vytvoření skupiny zabezpečení a přidání členů](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. [Přidání přiřazení role](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) **operátoru podrobného plánu** k účtu nebo skupině zabezpečení

## <a name="user-assign-managed-identity"></a>Přiřazení spravované identity uživatelem

Definice podrobného plánu můžete použít buď systémem přiřazené nebo uživatelem přiřazené spravované identity. Však při použití role **operátor podrobného plánu,** definice podrobného plánu musí být nakonfigurován tak, aby použití uživatelem přiřazené spravované identity. Kromě toho účtu nebo skupině zabezpečení, které je udělena role **Operátor podrobného plánu,** musí být udělena role **Operátor spravované identity** na spravované identitě přiřazené uživateli. Bez tohoto oprávnění se nezdaří přiřazení podrobného plánu z důvodu nedostatku oprávnění.

1. [Vytvoření spravované identity přiřazené uživatelem](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) pro použití přiřazeným podrobným plánem

1. [Přidejte přiřazení role](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) **operátoru spravované identity** do účtu nebo skupiny zabezpečení. Rozsah přiřazení role k nové spravované identity přiřazené uživateli.

1. Jako **operátor podrobného plánu** [přiřaďte podrobný plán,](../create-blueprint-portal.md#assign-a-blueprint) který používá novou spravovanou identitu přiřazenou uživatelem.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [životním cyklu podrobného plánu](../concepts/lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](../concepts/sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](../concepts/resource-locking.md).
- Vyřešte problémy během přiřazení podrobného plánu s [obecným řešením potíží](../troubleshoot/general.md).