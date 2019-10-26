---
title: Použití vlastních rolí pro prostředky Azure v Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Naučte se používat vlastní role pro prostředky Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e940dd0e4c653ed4921a9e8f245aab34107ba95
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895717"
---
# <a name="use-custom-roles-for-azure-resources-in-privileged-identity-management"></a>Použití vlastních rolí pro prostředky Azure v Privileged Identity Management

Je možné, že budete muset použít nastavení striktní Privileged Identity Management (PIM) pro některé uživatele v privilegované roli ve vaší organizaci Azure Active Directory (Azure AD) a zajistit tak větší autonomii pro ostatní. Vezměte v úvahu například scénář, ve kterém vaše organizace zajímá několik smluvních partnerů, které pomáhají při vývoji aplikace, která se spustí v rámci předplatného Azure.

Jako správce prostředků chcete, aby zaměstnanci měli nárok na přístup bez nutnosti schválení. Všichni smluvní přidruží se ale musí schválit, když požadují přístup k prostředkům organizace.

Postupujte podle kroků uvedených v následující části a nastavte cílené Privileged Identity Management nastavení pro role prostředků Azure.

## <a name="create-the-custom-role"></a>Vytvoření vlastní role

Pokud chcete vytvořit vlastní roli pro prostředek, postupujte podle kroků popsaných v tématu [Vytvoření vlastních rolí pro Azure založené na rolích Access Control](../role-based-access-control-custom-roles.md).

Když vytváříte vlastní roli, zahrňte popisný název, abyste si mohli snadno zapamatovat, která integrovaná role má být duplicitní.

> [!NOTE]
> Ujistěte se, že vlastní role je duplikátem předdefinované role, kterou chcete duplikovat, a že její obor odpovídá předdefinované roli.

## <a name="apply-pim-settings"></a>Použít nastavení PIM

Po vytvoření role ve vaší organizaci Azure AD se v Azure Portal na stránce **Privileged Identity Management – prostředky Azure** . Vyberte prostředek, na který se role vztahuje.

![Podokno "Privileged Identity Management – prostředky Azure"](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Nakonfigurujte Privileged Identity Management nastavení rolí](pim-resource-roles-configure-role-settings.md) , která by se měla vztahovat na tyto členy role.

Nakonec [přiřaďte role](pim-resource-roles-assign-roles.md) k jedinečné skupině členů, u kterých chcete toto nastavení cílit.

## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role prostředků Azure v Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Vlastní role v Azure](../../role-based-access-control/custom-roles.md)
