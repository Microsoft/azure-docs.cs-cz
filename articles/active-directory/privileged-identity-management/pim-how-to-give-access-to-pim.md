---
title: Udělení přístupu na jiné správce ke správě PIM – Azure | Dokumentace Microsoftu
description: Zjistěte, jak udělit přístup k jiné správy pro správu Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/29/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0d8cb77cda97c3141987fa8b0ea39541ace6db3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191729"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Udělení přístupu na jiné správce ke správě PIM

Globální správce, který umožňuje Azure AD Privileged Identity Management (PIM) pro organizaci automaticky získat přiřazení rolí a přístupu k PIM. Nikdo jiný získá oprávnění k zápisu ve výchozím nastavení, i když, včetně další globální správce. Jiné globální správci, správci zabezpečení a čtenáři zabezpečení mají přístup jen pro čtení k PIM. K udělení přístupu k PIM, můžete přiřadit na prvního uživatele ostatním uživatelům **správce privilegovaných rolí** role.

> [!NOTE]
> Správa PIM vyžaduje Azure MFA. Protože účty Microsoft nelze zaregistrovat pro Azure MFA, nemůže uživatel, který se přihlásí pomocí účtu Microsoft k PIM.

Ujistěte se, že jsou vždy alespoň dva uživatelé v roli správce privilegovaných rolí, v případě, že jeden uživatel je uzamčen nebo se odstranil jeho účet.

## <a name="grant-access-to-manage-pim"></a>Udělit přístup ke správě PIM

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **role adresáře Azure AD**.

1. Klikněte na tlačítko **role**.

    ![Role adresáře PIM Azure AD - role](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Klikněte na tlačítko **správce privilegovaných rolí** role a otevřete stránku členy.

    ![Správce privilegovaných rolí – členy](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Klikněte na tlačítko **přidat člena** a otevřete tak podokno Přidat spravované členy.

1. Klikněte na tlačítko **výběr členů** a otevřete tak podokno výběr členů.

    ![Správce privilegovaných rolí – výběr členů](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Vyberte člena a potom klikněte na tlačítko **vyberte**.

1. Klikněte na tlačítko **OK** provést člen nárok **správce privilegovaných rolí** role.

    Při přiřazení nové role někomu v PIM, se automaticky nakonfiguruje jako **oprávněné** aktivaci této role.

1. Chcete-li člen trvalé, klikněte na uživatele v seznamu členů správce privilegovaných rolí.

1. Klikněte na tlačítko **Další** a potom **trvalé** na trvalé přiřazení.

    ![Správce privilegovaných rolí – trvalé](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Poslat uživateli odkaz [začít používat PIM](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Odebrat přístup ke správě PIM

Před odebráním uživatele z role správce privilegovaných rolí, vždy ujistěte se, že nadále bude alespoň dva uživatele přiřadit k ní.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **role adresáře Azure AD**.

1. Klikněte na tlačítko **role**.

1. Klikněte na tlačítko **správce privilegovaných rolí** role a otevřete stránku členy.

1. Přidat značku zaškrtnutí vedle uživatele, kterou chcete odebrat a pak klikněte na tlačítko **člena odebrat**.

    ![Správce privilegovaných rolí - odebrat člena](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Ve zprávě, která se zobrazí dotaz, pokud chcete odebrání člena z role, klikněte na tlačítko **Ano**.

## <a name="next-steps"></a>Další postup

- [Zahájení práce s PIM](pim-getting-started.md)
