---
title: Migrace classic zásadu, která vyžaduje vícefaktorové ověřování na webu Azure portal | Microsoft Docs
description: Tento článek ukazuje, jak migrovat classic zásady, které vyžadují služby Multi-Factor authentication na portálu Azure.
services: active-directory
keywords: podmíněný přístup k aplikacím, podmíněného přístupu s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 52d4297d2e86ad67672d38f1c527041b813f8d61
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723683"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Migrace classic zásady, které vyžadují služby Multi-Factor authentication na portálu Azure 

Tento článek ukazuje, jak migrovat classic zásady, které vyžadují **služby Multi-Factor authentication** pro cloudové aplikace. I když není předpokladem, doporučujeme, abyste si přečetli [migraci na portálu Azure classic zásad](active-directory-conditional-access-migration.md) předtím, než začnete s migrací classic zásad.


 
## <a name="overview"></a>Přehled 

Tento scénář v tomto článku ukazuje, jak migrovat classic zásady, které vyžadují **služby Multi-Factor authentication** pro cloudové aplikace. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


Proces migrace se skládá z následujících kroků:

1. [Otevřete classic zásady](#open-a-classic-policy) získat nastavení konfigurace.
2. Vytvoření nové zásady podmíněného přístupu Azure AD classic zásady nahradit. 
3. Zakažte classic zásadu.



## <a name="open-a-classic-policy"></a>Otevřete zásadu classic

1. V [portál Azure](https://portal.azure.com), v levém navigační panel, klikněte na tlačítko **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration-mfa/01.png)

2. Na **Azure Active Directory** stránky v **spravovat** klikněte na tlačítko **podmíněného přístupu**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-migration-mfa/02.png)

3. V **spravovat** klikněte na tlačítko **zásady Classic (preview)**.

    ![Klasické zásady](./media/active-directory-conditional-access-migration-mfa/12.png)

4. V seznamu classic zásad, klikněte na zásadu, která vyžaduje **služby Multi-Factor authentication** pro cloudové aplikace.

    ![Klasické zásady](./media/active-directory-conditional-access-migration-mfa/13.png)


## <a name="create-a-new-conditional-access-policy"></a>Vytvořit novou zásadu podmíněného přístupu


1. V [portál Azure](https://portal.azure.com), v levém navigační panel, klikněte na tlačítko **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. Na **Azure Active Directory** stránky v **spravovat** klikněte na tlačítko **podmíněného přístupu**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-migration/02.png)



3. Na **podmíněného přístupu** stránky, otevřete **nový** stránky, na panelu nástrojů v horní části klikněte na tlačítko **přidat**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-migration/03.png)

4. Na **nový** stránky v **název** textovému poli, zadejte název zásady.

    ![Podmíněný přístup](./media/active-directory-conditional-access-migration/29.png)

5. V **přiřazení** klikněte na tlačítko **uživatelů a skupin**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-migration/05.png)

    a. Pokud máte všechny vybrané v classic zásady uživatele, klikněte na tlačítko **všichni uživatelé**. 

    ![Podmíněný přístup](./media/active-directory-conditional-access-migration/35.png)

    b. Pokud máte vybrané v classic zásady skupiny, klikněte na tlačítko **vyberte uživatele a skupiny**a potom vyberte požadovaných uživatelů a skupin.

    ![Podmíněný přístup](./media/active-directory-conditional-access-migration/36.png)

    c. Pokud máte vyloučené skupiny, klikněte na tlačítko **vyloučit** a potom vyberte požadované uživatele a skupiny. 

    ![Podmíněný přístup](./media/active-directory-conditional-access-migration/37.png)

6. Na **nový** stránky, otevřete **cloudových aplikací** stránky v **přiřazení** klikněte na tlačítko **cloudových aplikací**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. Na **cloudových aplikací** proveďte následující kroky:

    ![Podmíněný přístup](./media/active-directory-conditional-access-migration/08.png)

    a. Klikněte na tlačítko **vybrat aplikace**.

    b. Klikněte na **Vybrat**.

    c. Na **vyberte** vyberte cloudové aplikace a pak klikněte na tlačítko **vyberte**.

    d. Na **cloudových aplikací** klikněte na tlačítko **provádí**.



9. Pokud máte **vyžadovat vícefaktorové ověřování** vybrané:

    ![Podmíněný přístup](./media/active-directory-conditional-access-migration/26.png)

    a. V **přístup k ovládacím prvkům** klikněte na tlačítko **Grant**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-migration/27.png)

    b. Na **Grant** klikněte na tlačítko **udělit přístup**a potom klikněte na **vyžadovat vícefaktorové ověřování**.

    c. Klikněte na **Vybrat**.


10. Klikněte na tlačítko **na** povolit vaše zásady.

    ![Podmíněný přístup](./media/active-directory-conditional-access-migration/30.png)



## <a name="disable-the-classic-policy"></a>Zakažte zásadu classic

Chcete-li zakázat classic zásady, klikněte na tlačítko **zakázat** v **podrobnosti** zobrazení.

![Klasické zásady](./media/active-directory-conditional-access-migration-mfa/14.png)



## <a name="next-steps"></a>Další postup

- Další informace o migraci classic zásad najdete v tématu [migraci na portálu Azure classic zásad](active-directory-conditional-access-migration.md).


- Pokud chcete vědět, jak konfigurovat zásadu podmíněného přístupu, najdete v článku [Začínáme s podmíněným přístupem v Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup v Azure Active Directory](active-directory-conditional-access-best-practices.md). 
