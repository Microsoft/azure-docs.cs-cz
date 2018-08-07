---
title: Migrovat klasické zásady, která vyžaduje ověření službou Multi-Factor Authentication na webu Azure Portal | Dokumentace Microsoftu
description: Tento článek ukazuje, jak migrovat klasické zásady, která vyžaduje ověření službou Multi-Factor Authentication na webu Azure Portal.
services: active-directory
keywords: podmíněný přístup pro aplikace, podmíněný přístup s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
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
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 8d57f06a2b1554014d101088f3743d16e6352051
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581199"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Migrovat klasické zásady, která vyžaduje ověření službou Multi-Factor Authentication na webu Azure Portal 

Tento článek popisuje, jak migrovat klasické zásady, které vyžadují **ověřování službou Multi-Factor Authentication** pro cloudové aplikace. I když není požadována, doporučujeme, abyste si přečetli [migrace klasických zásad na webu Azure Portal](policy-migration.md) předtím, než začnete s migrací klasické zásady.


 
## <a name="overview"></a>Přehled 

Scénář v tomto článku ukazuje, jak migrovat klasické zásady, které vyžadují **ověřování službou Multi-Factor Authentication** pro cloudové aplikace. 

![Azure Active Directory](./media/policy-migration/33.png)


Proces migrace se skládá z následujících kroků:

1. [Otevřete klasické zásady](#open-a-classic-policy) zobrazíte nastavení konfigurace.
2. Vytvoření nové zásady podmíněného přístupu Azure AD k nahrazení klasické zásady. 
3. Zakážete klasické zásady.



## <a name="open-a-classic-policy"></a>Otevřete klasické zásady

1. V [webu Azure portal](https://portal.azure.com), v levém navigačním panelu klikněte na **Azure Active Directory**.

    ![Azure Active Directory](./media/policy-migration-mfa/01.png)

2. Na **Azure Active Directory** stránku, **spravovat** klikněte na tlačítko **podmíněného přístupu**.

    ![Podmíněný přístup](./media/policy-migration-mfa/02.png)

3. V **spravovat** klikněte na tlačítko **klasické zásady (preview)**.

    ![Klasické zásady](./media/policy-migration-mfa/12.png)

4. V seznamu klasické zásady, klikněte na zásadu, která vyžaduje **ověřování službou Multi-Factor Authentication** pro cloudové aplikace.

    ![Klasické zásady](./media/policy-migration-mfa/13.png)


## <a name="create-a-new-conditional-access-policy"></a>Vytvořit novou zásadu podmíněného přístupu


1. V [webu Azure portal](https://portal.azure.com), v levém navigačním panelu klikněte na **Azure Active Directory**.

    ![Azure Active Directory](./media/policy-migration/01.png)

2. Na **Azure Active Directory** stránku, **spravovat** klikněte na tlačítko **podmíněného přístupu**.

    ![Podmíněný přístup](./media/policy-migration/02.png)



3. Na **podmíněného přístupu** stránky, otevřete **nový** klikněte na stránku, na panelu nástrojů v horní části **přidat**.

    ![Podmíněný přístup](./media/policy-migration/03.png)

4. Na **nový** stránku, **název** textového pole zadejte název zásady.

    ![Podmíněný přístup](./media/policy-migration/29.png)

5. V **přiřazení** klikněte na tlačítko **uživatelů a skupin**.

    ![Podmíněný přístup](./media/policy-migration/05.png)

    a. Pokud máte vybrané všechny uživatele v klasické zásady, klikněte na tlačítko **všichni uživatelé**. 

    ![Podmíněný přístup](./media/policy-migration/35.png)

    b. Pokud máte vybrané v klasické zásady skupiny, klikněte na tlačítko **výběr uživatelů a skupin**a potom vyberte požadované uživatele a skupiny.

    ![Podmíněný přístup](./media/policy-migration/36.png)

    c. Pokud máte vyloučené skupiny, klikněte na tlačítko **vyloučit** kartu a potom vyberte požadované uživatele a skupiny. 

    ![Podmíněný přístup](./media/policy-migration/37.png)

6. Na **nový** stránky, otevřete **cloudové aplikace** stránku, **přiřazení** klikněte na tlačítko **cloudové aplikace**.

8. Na **cloudové aplikace** stránce, proveďte následující kroky:

    ![Podmíněný přístup](./media/policy-migration/08.png)

    a. Klikněte na tlačítko **vyberte aplikace**.

    b. Klikněte na **Vybrat**.

    c. Na **vyberte** stránce vyberte cloudové aplikace a pak klikněte na **vyberte**.

    d. Na **cloudové aplikace** klikněte na **provádí**.



9. Pokud máte **vyžadovat vícefaktorové ověřování** vybrané:

    ![Podmíněný přístup](./media/policy-migration/26.png)

    a. V **ovládací prvky přístupu** klikněte na tlačítko **udělení**.

    ![Podmíněný přístup](./media/policy-migration/27.png)

    b. Na **udělení** klikněte na **udělit přístup**a potom klikněte na tlačítko **vyžadovat vícefaktorové ověřování**.

    c. Klikněte na **Vybrat**.


10. Klikněte na tlačítko **na** povolit vaše zásady.

    ![Podmíněný přístup](./media/policy-migration/30.png)



## <a name="disable-the-classic-policy"></a>Zakázat klasické zásady

Chcete-li zakázat klasické zásady, klikněte na tlačítko **zakázat** v **podrobnosti** zobrazení.

![Klasické zásady](./media/policy-migration-mfa/14.png)



## <a name="next-steps"></a>Další postup

- Další informace o migraci klasické zásady, najdete v části [migrace klasických zásad na webu Azure Portal](policy-migration.md).


- Pokud chcete vědět, jak nakonfigurovat zásady podmíněného přístupu najdete v tématu [vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](app-based-mfa.md).

- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory](best-practices.md). 
