---
title: Použití vlastních rolí pro prostředky Azure v PIM-Azure Active Directory | Microsoft Docs
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
ms.date: 03/30/2018
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d36514c97cf1f45ee0a435d3b716019d2762e5a
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804193"
---
# <a name="use-custom-roles-for-azure-resources-in-pim"></a>Použití vlastních rolí pro prostředky Azure v PIM

Je možné, že budete muset pro některé členy role použít možnost striktní Azure Active Directory (Azure AD) Privileged Identity Management (PIM) a zároveň zajistit větší autonomii pro ostatní. Vezměte v úvahu scénář, ve kterém vaše organizace zajímá několik smluvních partnerů, které pomáhají při vývoji aplikace, která se spustí v rámci předplatného Azure.

Jako správce prostředků chcete, aby zaměstnanci měli nárok na přístup bez nutnosti schválení. Všichni smluvní přidruží se ale musí schválit, když požadují přístup k prostředkům organizace.

Postupujte podle kroků uvedených v následující části a nastavte cílené nastavení PIM pro role prostředků Azure.

## <a name="create-the-custom-role"></a>Vytvoření vlastní role

Pokud chcete vytvořit vlastní roli pro prostředek, postupujte podle kroků popsaných v tématu [Vytvoření vlastních rolí pro Azure založené na rolích Access Control](../role-based-access-control-custom-roles.md).

Když vytváříte vlastní roli, zahrňte popisný název, abyste si mohli snadno zapamatovat, která integrovaná role má být duplicitní.

> [!NOTE]
> Ujistěte se, že vlastní role je duplikátem předdefinované role, kterou chcete duplikovat, a že její obor odpovídá předdefinované roli.

## <a name="apply-pim-settings"></a>Použít nastavení PIM

Po vytvoření role ve vašem tenantovi v Azure Portal přejdete do podokna **Privileged Identity Management – prostředky Azure** . Vyberte prostředek, na který se role vztahuje.

![Podokno "Privileged Identity Management – prostředky Azure"](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Nakonfigurujte nastavení role PIM](pim-resource-roles-configure-role-settings.md) , která by se měla vztahovat na tyto členy role.

Nakonec [přiřaďte role](pim-resource-roles-assign-roles.md) k jedinečné skupině členů, u kterých chcete toto nastavení cílit.

## <a name="next-steps"></a>Další postup

- [Konfigurace nastavení role prostředků Azure v PIM](pim-resource-roles-configure-role-settings.md)
- [Vlastní role v Azure](../../role-based-access-control/custom-roles.md)
