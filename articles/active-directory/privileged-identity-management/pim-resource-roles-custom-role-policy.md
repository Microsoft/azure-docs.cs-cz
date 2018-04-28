---
title: Použít vlastní role cílový Privileged Identity Management nastavení pro prostředky Azure | Microsoft Docs
description: Popisuje, jak používat vlastní role pro prostředky Azure s PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: e3df34e761f17aa3c1949af390e57360d84a304f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="use-custom-roles-to-target-privileged-identity-management-settings"></a>Použít vlastní role k nastavení cílového Privileged Identity Management

Budete se muset použít nastavení striktní Privileged Identity Management (PIM) pro některé členy role, při současném poskytování větší nezávislé pro ostatní. Vezměte v úvahu scénář, ve kterém organizaci najímá několik partnerů kontrakt jako pomůcku při vývoji aplikace, která se spustí v předplatné Azure.

Jako správce prostředků chcete zaměstnanců, kteří budou na přístup nárok bez nutnosti schválení. Všechny smlouvy partnerů však musí být schválen, když zadají žádost o přístup k prostředkům organizace.

Postupujte podle kroků uvedených v následující části nastavit cílové PIM nastavení rolí prostředků Azure.

## <a name="create-the-custom-role"></a>Vytvořit vlastní role

Pokud chcete vytvořit vlastní role pro prostředek, postupujte podle kroků popsaných v [vytvářet vlastní role pro řízení přístupu](../role-based-access-control-custom-roles.md).

Když vytvoříte vlastní roli, zahrnují popisný název, můžete snadno Zapamatovat které předdefinovaná role, které byste chtěli duplicitní.

> [!NOTE]
> Ujistěte se, že vlastní roli, je duplicitní předdefinovaná role, kterou chcete duplikovat, a to jeho oboru předdefinovaná role.

## <a name="apply-pim-settings"></a>Použít nastavení PIM

Po vytvoření role v klientovi, na portálu Azure přejděte do **Privileged Identity Management - prostředky Azure** podokně. Vyberte prostředek, který se vztahuje na roli.

!["Privileged Identity Management - prostředky Azure" podokno](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[Konfigurace nastavení role PIM](pim-resource-roles-configure-role-settings.md) , by se měly používat tyto členům role.

Nakonec [přiřadit role](pim-resource-roles-assign-roles.md) ke skupině odlišné členů, které chcete zacílit s těmito nastaveními.

## <a name="next-steps"></a>Další postup

[Zkontrolujte předplatné vlastníků a přístup](pim-resource-roles-perform-access-review.md)
