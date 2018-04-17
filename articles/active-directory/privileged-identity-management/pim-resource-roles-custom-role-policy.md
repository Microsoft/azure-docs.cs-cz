---
title: Privileged Identity Management pro prostředky Azure – vlastní role použijte k nastavení PIM target | Microsoft Docs
description: Popisuje, jak používat vlastní role v PIM pro prostředky Azure.
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
ms.openlocfilehash: 6336d99df1bbdd71c66a9757af1d9fb356a91bf6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-custom-roles-to-target-pim-settings"></a>Použít vlastní role nastavení PIM cíl

Může být nutné použít nastavení striktní PIM pro některé členy role, při současném poskytování větší nezávislé pro ostatní. Vezměte v úvahu scénář, kde vaše organizace najímá sevral kontrakt partnerů jako pomůcku při vývoji aplikace, která se spustí v předplatné Azure. 

Jako správce prostředků chcete zaměstnanci vaší organizace tak, aby měl oprávněné přístup bez schválení vyžadované, ale všechny smlouvy partnerů musí požadovat schválení, když zadají žádost o aktivaci. Postupujte podle těchto kroků outline proces povolení cílové nastavení PIM pro role prostředků Azure.

## <a name="create-the-custom-role"></a>Vytvořit vlastní role

[Tohoto průvodce použijte, chcete-li vytvořit vlastní role pro prostředek](../../role-based-access-control/custom-roles.md).

Zahrnovat popisný název, takže můžete snadno Zapamatovat které předdefinovaná role, které byste chtěli duplicitní.

>[!NOTE]
>Ujistěte se, že vlastní roli, je duplicitní role, kterou jste měli v úmyslu a jeho oboru odpovídá předdefinovaná role.

## <a name="apply-pim-settings"></a>Použít nastavení PIM

Po vytvoření role ve vašem klientovi přejděte do PIM a vyberte roli je vymezen na prostředek.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[Konfigurace nastavení role PIM](pim-resource-roles-configure-role-settings.md) , by se měly používat pro tyto členy

Nakonec [přiřadit role](pim-resource-roles-assign-roles.md) ke skupině odlišné členů, které chcete cílit s těmito nastaveními.

## <a name="next-steps"></a>Další postup

[Zkontrolujte vlastníky předplatného](pim-resource-roles-perform-access-review.md)
