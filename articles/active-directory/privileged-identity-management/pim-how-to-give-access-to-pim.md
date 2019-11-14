---
title: Udělení přístupu ke správě PIM-Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak udělit přístup ostatním správám za účelem správy Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7424e92f8520d13137b6ac8787523095058a005f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022118"
---
# <a name="grant-access-to-other-administrators-to-manage-privileged-identity-management"></a>Udělte ostatním správcům přístup ke správě Privileged Identity Management

Globální správce, který umožňuje Privileged Identity Management (PIM) pro organizaci, automaticky získá přiřazení rolí a přístup k Privileged Identity Management. Nikdo jiný ve vaší Azure Active Directory (Azure AD) nezíská ve výchozím nastavení přístup pro zápis, včetně jiných globálních správců. Jiní globální správci, správci zabezpečení a čtenáři zabezpečení mají přístup jen pro čtení k Privileged Identity Management. Pokud chcete udělit přístup Privileged Identity Management, může první uživatel přiřadit jiné roli **správce privilegované role** .

> [!NOTE]
> Správa Privileged Identity Management vyžaduje Azure Multi-Factor Authentication. Vzhledem k tomu, že účty Microsoft se nemůžou registrovat pro Azure Multi-Factor Authentication, uživatel, který se přihlásí pomocí účet Microsoft, nemá přístup k Privileged Identity Management.

Zajistěte, aby v roli správce privilegované role existovaly vždycky aspoň dva uživatelé, pokud je jeden uživatel uzamčený nebo se jeho účet odstraní.

## <a name="grant-access-to-manage-pim"></a>Udělení přístupu ke správě PIM

1. Přihlásit se na [Azure Portal](https://portal.azure.com/).

1. V Azure AD otevřete **Privileged Identity Management**.

1. Vyberte **role Azure AD**.

1. Vyberte **role**.

    ![Privileged Identity Management rolí Azure AD – role](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Vyberte roli **správce privilegovaných rolí** a otevřete stránku členové.

    ![Správce privilegovaných rolí – členové](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Vyberte **Přidat člena** a otevřete tak podokno přidat spravované členy.

1. Vyberte **Vybrat členy** a otevřete tak podokno vybrat členy.

    ![Správce privilegovaných rolí – výběr členů](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Vyberte člena a pak klikněte na **Vybrat**.

1. Vyberte **OK** , pokud chcete, aby měl člen nárok na roli **správce privilegovaných rolí** .

    Když někomu v Privileged Identity Management přiřadíte novou roli, automaticky se nakonfigurují jako **opravňující** k aktivaci role.

1. Pokud chcete, aby byl člen trvalý, vyberte uživatele v seznamu privilegovaných rolí správce.

1. Vyberte možnost **Další** a poté **proveďte trvalé** přiřazení.

    ![Správce privilegovaných rolí – vytvořit trvalý](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Odeslat uživateli odkaz pro [zahájení používání Privileged Identity Management](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Odebrání přístupu pro správu PIM

Než odeberete někoho z role správce privilegované role, ujistěte se, že se k němu pořád přiřadí aspoň dva uživatelé.

1. Přihlásit se na [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **role Azure AD**.

1. Vyberte **role**.

1. Vyberte roli **správce privilegovaných rolí** a otevřete stránku členové.

1. Zaškrtněte políčko vedle uživatele, kterého chcete odebrat, a pak vyberte **odebrat člena**.

    ![Správce privilegovaných rolí – odebrání člena](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Když se zobrazí výzva k potvrzení, že chcete člena z role odebrat, vyberte **Ano**.

## <a name="next-steps"></a>Další kroky

- [Začněte používat Privileged Identity Management](pim-getting-started.md)
