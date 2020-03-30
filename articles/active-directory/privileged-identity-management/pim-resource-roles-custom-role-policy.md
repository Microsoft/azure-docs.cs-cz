---
title: Použití vlastních rolí pro prostředky Azure v PIM – Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak používat vlastní role pro prostředky Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbe08cff2b57155f8f3315f5d3454abfbdad47a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847068"
---
# <a name="use-custom-roles-for-azure-resources-in-privileged-identity-management"></a>Použití vlastních rolí pro prostředky Azure ve správě privilegovaných identit

Možná budete muset použít přísné nastavení správy privilegovaných identit (PIM) pro některé uživatele v privilegované roli ve vaší organizaci Azure Active Directory (Azure AD) a zároveň poskytuje větší autonomii pro ostatní. Zvažte například scénář, ve kterém vaše organizace najímá několik spolupracovníků smlouvy, kteří vám pomohou při vývoji aplikace, která bude spuštěna v předplatném Azure.

Jako správce prostředků chcete, aby zaměstnanci měli nárok na přístup bez nutnosti schválení. Všichni smluvní partneři však musí být schváleni, když požadují přístup k prostředkům organizace.

Podle pokynů uvedených v další části nastavte cílené nastavení správy privilegovaných identit pro role prostředků Azure.

## <a name="create-the-custom-role"></a>Vytvoření vlastní role

Chcete-li vytvořit vlastní roli pro prostředek, postupujte podle kroků popsaných v [části Vytvoření vlastních rolí pro řízení přístupu na základě rolí Azure](../role-based-access-control-custom-roles.md).

Při vytváření vlastní role uveďte popisný název, abyste si mohli snadno zapamatovat, kterou předdefinovanou roli jste chtěli duplikovat.

> [!NOTE]
> Ujistěte se, že vlastní role je duplikát předdefinované role, kterou chcete duplikovat, a že její obor odpovídá předdefinované roli.

## <a name="apply-pim-settings"></a>Použít nastavení PIM

Po vytvoření role ve vaší organizaci Azure AD přejděte na stránku **Privilegovaná správa identit – prostředky Azure** na webu Azure Portal. Vyberte prostředek, na který se role vztahuje.

![Podokno Privilegovaná správa identit – prostředky Azure](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Nakonfigurujte nastavení role Správy privilegovaných identit,](pim-resource-roles-configure-role-settings.md) která by se měla vztahovat na tyto členy role.

Nakonec [přiřaďte role](pim-resource-roles-assign-roles.md) odlišné skupině členů, na které chcete s těmito nastaveními cílit.

## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role prostředků Azure ve správě privilegovaných identit](pim-resource-roles-configure-role-settings.md)
- [Vlastní role v Azure](../../role-based-access-control/custom-roles.md)
