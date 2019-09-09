---
title: Udělení přístupu jiným správcům pro správu PIM-Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak udělit přístup ostatním správám za účelem správy Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3a0173108b6c884994ca25fd0495e9cb8d45186
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804360"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Udělení přístupu jiným správcům pro správu PIM

Globální správce, který umožňuje Azure Active Directory (Azure AD) Privileged Identity Management (PIM) pro organizaci automaticky získávat přiřazení rolí a přístup k PIM. Nikdo jiný nezíská přístup pro zápis ve výchozím nastavení, ale včetně jiných globálních správců. Jiní globální správci, správci zabezpečení a čtenáři zabezpečení mají přístup jen pro čtení k PIM. Aby mohl uživatel udělit přístup ke PIM, může prvnímu uživateli přiřadit roli **správce privilegovaných rolí** .

> [!NOTE]
> Správa PIM vyžaduje Azure MFA. Vzhledem k tomu, že účty Microsoft se nemůžou registrovat pro Azure MFA, uživatel, který se přihlásí pomocí účet Microsoft, nemá přístup k PIM.

Zajistěte, aby v roli správce privilegované role existovaly vždycky aspoň dva uživatelé, pokud je jeden uživatel uzamčený nebo se jeho účet odstraní.

## <a name="grant-access-to-manage-pim"></a>Udělení přístupu ke správě PIM

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **role Azure AD**.

1. Klikněte na **role**.

    ![Role PIM Azure AD – role](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Kliknutím na roli **správce privilegované role** otevřete stránku členové.

    ![Správce privilegovaných rolí – členové](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Kliknutím na **Přidat člena** otevřete podokno přidat spravované členy.

1. Kliknutím na **Vybrat členy** otevřete podokno vybrat členy.

    ![Správce privilegovaných rolí – výběr členů](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Vyberte člena a pak klikněte na **Vybrat**.

1. Kliknutím na tlačítko **OK** nastavte člena jako opravňujícího pro roli **správce privilegované role** .

    Když někomu v PIM přiřadíte novou roli, automaticky se nakonfigurují jako **oprávněné** k aktivaci role.

1. Pokud chcete, aby byl člen trvalý, klikněte na uživatele v seznamu privilegovaných rolí správce.

1. Klikněte na tlačítko **Další** a potom **vytvořte trvalé** přiřazení.

    ![Správce privilegovaných rolí – vytvořit trvalý](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Pošle uživateli odkaz na [zahájení používání PIM](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Odebrání přístupu pro správu PIM

Než odeberete někoho z role správce privilegované role, ujistěte se, že se k němu pořád přiřadí aspoň dva uživatelé.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **role Azure AD**.

1. Klikněte na **role**.

1. Kliknutím na roli **správce privilegované role** otevřete stránku členové.

1. Zaškrtněte políčko vedle uživatele, kterého chcete odebrat, a poté klikněte na možnost **odebrat člena**.

    ![Správce privilegovaných rolí – odebrání člena](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Ve zprávě, která se zobrazí dotaz, zda chcete člena z role odebrat, klikněte na tlačítko **Ano**.

## <a name="next-steps"></a>Další kroky

- [Zahájení práce s PIM](pim-getting-started.md)
