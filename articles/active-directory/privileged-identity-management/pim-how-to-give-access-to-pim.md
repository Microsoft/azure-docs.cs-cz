---
title: Udělení přístupu ke správě PIM – Azure Active Directory | Dokumenty společnosti Microsoft
description: Zjistěte, jak udělit přístup jiným správám ke správě privilegované správy identit Azure AD (PIM).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022118"
---
# <a name="grant-access-to-other-administrators-to-manage-privileged-identity-management"></a>Udělit přístup ostatním správcům ke správě privilegovaných identit

Globální správce, který umožňuje správu privilegovaných identit (PIM) pro organizaci, automaticky získá přiřazení rolí a přístup ke správě privilegovaných identit. Nikdo jiný ve vaší organizaci Azure Active Directory (Azure AD) nezíská přístup pro zápis ve výchozím nastavení, včetně dalších globálních správců. Ostatní globální správci, správci zabezpečení a čtenáři zabezpečení mají přístup ke správě privilegovaných identit jen pro čtení. Chcete-li udělit přístup ke správě privilegovaných identit, může první uživatel přiřadit roli **Správce privilegovaných rolí** ostatním uživatelům.

> [!NOTE]
> Správa správy privilegovaných identit vyžaduje azure multifaktorové ověřování. Vzhledem k tomu, že účty Microsoft se nemohou zaregistrovat pro azure vícefaktorové ověřování, uživatel, který se přihlásí pomocí účtu Microsoft, nemá přístup ke správě privilegovaných identit.

Ujistěte se, že v roli správce privilegované role jsou vždy alespoň dva uživatelé v případě, že je jeden uživatel uzamčen nebo je odstraněn jejich účet.

## <a name="grant-access-to-manage-pim"></a>Udělit přístup ke správě PIM

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Ve službě Azure AD otevřete **správu privilegovaných identit**.

1. Vyberte **role Azure AD**.

1. Vyberte **role**.

    ![Role Azure AD správy privilegovaných identit – role](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Chcete-li otevřít stránku členů, vyberte roli **Správce privilegovaných rolí.**

    ![Správce privilegovaných rolí - členové](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Výběrem **možnosti Přidat člena** otevřete podokno Přidat spravované členy.

1. Výběrem **možnosti Vybrat členy** otevřete podokno Vybrat členy.

    ![Správce privilegovaných rolí – výběr členů](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Vyberte člena a klepněte na tlačítko **Vybrat**.

1. Vyberte **OK,** aby byl člen způsobilý pro roli **Správce privilegovaných rolí.**

    Když někomu ve správě privilegovaných identit přiřadíte novou roli, bude automaticky nakonfigurován jako **způsobilý** k aktivaci role.

1. Chcete-li, aby byl člen trvalý, vyberte uživatele v seznamu členů správce privilegovaných rolí.

1. Vyberte **Více** a pak **make permanentní,** aby se přiřazení trvalé.

    ![Správce privilegovaných rolí – nastavení trvalého](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Odešlete uživateli odkaz na [začít používat správu privilegovaných identit](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Odebrání přístupu ke správě PIM

Před odebráním uživatele z role Správce privilegovaných rolí vždy zkontrolujte, zda k němu budou stále přiřazeni alespoň dva uživatelé.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **role Azure AD**.

1. Vyberte **role**.

1. Chcete-li otevřít stránku členů, vyberte roli **Správce privilegovaných rolí.**

1. Zaškrtněte políčko vedle uživatele, kterého chcete odebrat, a pak vyberte **Odebrat člena**.

    ![Správce privilegovaných rolí – odebrání člena](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Pokud budete vyzváni k potvrzení, že chcete odebrat člena z role, vyberte **ano**.

## <a name="next-steps"></a>Další kroky

- [Zahájení využití technologie Privileged Identity Management](pim-getting-started.md)
