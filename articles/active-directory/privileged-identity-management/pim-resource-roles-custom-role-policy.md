---
title: Použití vlastních rolí pro prostředky Azure v PIM – Azure Active Directory | Dokumentace Microsoftu
description: Další informace o použití vlastních rolí pro prostředky Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e814cde49374b52266f725b4d57657a507874ab
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602055"
---
# <a name="use-custom-roles-for-azure-resources-in-pim"></a>Použití vlastních rolí pro prostředky Azure v PIM

Můžete potřebovat k aplikování nastavení striktní Azure Active Directory (Azure AD) Privileged Identity Management (PIM) pro některé členy role, poskytují větší volnost pro ostatní. Představte si třeba situaci, ve kterém vaše organizace zaměstná několik smlouvy associates, které pomáhají při vývoji aplikace, který se spustí v rámci předplatného Azure.

Jako správce prostředků chcete zaměstnanci mají oprávnění pro přístup bez vyžadování schválení. Všechny smlouvy přidruží však musí být schválen, když zadají žádost o přístup k prostředkům ve vaší organizaci.

Postupujte podle kroků uvedených v další části a nastavení cílové nastavení PIM pro role prostředků Azure.

## <a name="create-the-custom-role"></a>Vytvořit vlastní roli

Chcete-li vytvořit vlastní role pro určitý prostředek, postupujte podle kroků popsaných v [vytváření vlastních rolí pro řízení přístupu](../role-based-access-control-custom-roles.md).

Když vytvoříte vlastní roli, obsahují popisný název, takže si snadno zapamatovali, které předdefinovaná role, které máte v úmyslu duplicitní.

> [!NOTE]
> Ujistěte se, že vlastní roli je duplikátem předdefinovaná role, které mají být duplicitní a že jeho rozsah odpovídá předdefinovaná role.

## <a name="apply-pim-settings"></a>Použít nastavení PIM

Po vytvoření role ve vašem tenantovi, na webu Azure Portal, přejděte **Privileged Identity Management – prostředky Azure** podokně. Vyberte prostředek, který odpovídá roli.

!["Privileged Identity Management – prostředky Azure" podokno](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Konfigurace nastavení role PIM](pim-resource-roles-configure-role-settings.md) těmto členům role, která by se měly používat.

Nakonec [přiřazení rolí](pim-resource-roles-assign-roles.md) do odlišné skupiny členy, které chcete cílit s těmito nastaveními.

## <a name="next-steps"></a>Další postup

- [Konfigurace nastavení role prostředků Azure v PIM](pim-resource-roles-configure-role-settings.md)
- [Vlastní role v Azure](../../role-based-access-control/custom-roles.md)
