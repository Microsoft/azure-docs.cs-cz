---
title: Změna plánů licencí pro uživatele a skupiny – Azure AD | Microsoft Docs
description: Postup migrace uživatelů v rámci skupiny do různých plánů služeb pomocí licencování skupin v Azure Active Directory
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 050ae95c79e7ecb98f8508c2fdb41b90fc1b1da0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546535"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Změna přiřazení licencí pro uživatele nebo skupinu v Azure Active Directory

Tento článek popisuje, jak přesunout uživatele a skupiny mezi plány licencí služby v Azure Active Directory (Azure AD). Cílem přístupu ke službě Azure AD je zajistit, aby během změny licence nedošlo ke ztrátě služeb nebo dat. Uživatelé by měli přepínat mezi službami bez problémů. Kroky pro přiřazení licenčního plánu v tomto článku popisují změnu uživatele nebo skupiny v Office 365 E1 na Office 365 E3, ale postup platí pro všechny plány licencí. Když aktualizujete přiřazení licencí pro uživatele nebo skupinu, provedou se odebrání přiřazení licencí a nové přiřazení, aby uživatelé během změn licencí neztratili přístup ke svým službám ani neviděli konflikty licencí mezi plány.

## <a name="before-you-begin"></a>Než začnete

Před aktualizací přiřazení licencí je důležité ověřit, jestli jsou některé předpoklady splněné pro všechny uživatele nebo skupiny, které se mají aktualizovat. Pokud tyto předpoklady nejsou pravdivé pro všechny uživatele ve skupině, může se u některých z nich tato migrace nezdařila. V důsledku toho mohou někteří uživatelé ztratit přístup ke službám nebo datům. Zajistěte, aby:

- Uživatelé mají aktuální licenční plán (v tomto případě je to Office 365 E1), který je přiřazený ke skupině a zděděný uživatelem a který se nepřiřazuje přímo.

- Máte k dispozici dostatek dostupných licencí pro licenční plán, který přiřazujete. Pokud nemáte dostatek licencí, někteří uživatelé nemusí mít přiřazený nový licenční plán. Můžete kontrolovat počet dostupných licencí.

- Uživatelé nemají jiné přiřazené licence služby, které mohou být v konfliktu s požadovanou licencí nebo zabraňují odebrání aktuální licence. Například licence ze služby, jako je například analýza na pracovišti nebo Project Online, která má závislost na jiných službách.

- Pokud spravujete skupiny místně a synchronizujete je do Azure AD prostřednictvím Azure AD Connect, pak můžete přidat nebo odebrat uživatele pomocí místního systému. Může trvat nějakou dobu, než se změny synchronizace se službou Azure AD vybírají licencováním skupin.

- Pokud používáte dynamická členství ve skupině Azure AD, přidáte nebo odeberete uživatele tak, že změníte jejich atributy, ale proces aktualizace pro přiřazení licencí zůstane stejný.

## <a name="change-user-license-assignments"></a>Změna přiřazení uživatelských licencí

Pokud vidíte, že některá zaškrtávací políčka nejsou k dispozici, na stránce **aktualizace přiřazení licencí** se zobrazí služby, které nelze změnit, protože jsou zděděny z licence skupiny.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu správce licencí ve vaší organizaci Azure AD.
1. Vyberte **Azure Active Directory**  >  **Uživatelé** a potom otevřete stránku **profil** pro uživatele.
1. Vyberte **licence**.
1. Vyberte **přiřazení** pro úpravu přiřazení licence pro uživatele nebo skupinu. Na stránce **přiřazení** můžete vyřešit konflikty přiřazení licencí.
1. Zaškrtněte políčko pro Office 365 E3 a ujistěte se, že je vybrána možnost minimálně všechny služby E1, které jsou přiřazeny uživateli.
1. Zrušte zaškrtnutí políčka pro Office 365 E1.

    ![Stránka přiřazení licencí pro uživatele, který uvádí, že sada Office 365 E1 byla vymazána a sada Office 365 E3 vybrána](./media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Vyberte **Uložit**.

Azure AD použije nové licence a současně odebere staré licence, aby bylo možné zajistit kontinuitu služeb.

## <a name="change-group-license-assignments"></a>Změna přiřazení licencí skupin

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu správce licencí ve vaší organizaci Azure AD.
1. Vyberte **Azure Active Directory**  >  **skupiny** a potom otevřete stránku **Přehled** pro skupinu.
1. Vyberte **licence**.
1. Vyberte příkaz **přiřazení** pro úpravu přiřazení licence pro uživatele nebo skupinu.
1. Zaškrtněte políčko pro Office 365 E3. Aby se zachovala kontinuita provozu, ujistěte se, že jste vybrali všechny služby E1, které jsou už k tomuto uživateli přiřazené.
1. Zrušte zaškrtnutí políčka pro Office 365 E1.

    ![Výběr příkazu přiřazení na stránce licence uživatele nebo skupiny](./media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Vyberte **Uložit**.

Pro zajištění kontinuity služeb Azure AD použije nové licence a staré licence odebere současně pro všechny uživatele ve skupině.

## <a name="next-steps"></a>Další kroky

Přečtěte si o dalších scénářích správy licencí prostřednictvím skupin v následujících článcích:

- [Přiřazení licencí ke skupině v Azure Active Directory](licensing-groups-assign.md)
- [Identifikace a řešení potíží s licencemi pro skupinu v Azure Active Directory](licensing-groups-resolve-problems.md)
- [Postup migrace jednotlivých licencovaných uživatelů na skupiny licencování v Azure Active Directory](licensing-groups-migrate-users.md)
- [Azure Active Directory skupin licencování dalších scénářů](licensing-group-advanced.md)
- [Příklady prostředí PowerShell pro licencování skupin v Azure Active Directory](licensing-ps-examples.md)
