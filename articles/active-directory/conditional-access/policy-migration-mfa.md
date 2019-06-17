---
title: Migrovat klasické zásady, která vyžaduje ověření službou Multi-Factor Authentication na webu Azure Portal
description: Tento článek ukazuje, jak migrovat klasické zásady, která vyžaduje ověření službou Multi-Factor Authentication na webu Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: tutorial
ms.date: 06/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2804a50cc1ef7bb257e1549afabdef466ce3c2f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112200"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Migrovat klasické zásady, která vyžaduje ověření službou Multi-Factor Authentication na webu Azure Portal

Tento kurz ukazuje, jak migrovat klasické zásady, které vyžadují **ověřování službou Multi-Factor Authentication** pro cloudové aplikace. I když není požadována, doporučujeme, abyste si přečetli [migrace klasických zásad na webu Azure Portal](policy-migration.md) předtím, než začnete s migrací klasické zásady.

## <a name="overview"></a>Přehled

Scénář v tomto článku ukazuje, jak migrovat klasické zásady, které vyžadují **ověřování službou Multi-Factor Authentication** pro cloudové aplikace.

![Azure Active Directory](./media/policy-migration/33.png)

Proces migrace se skládá z následujících kroků:

1. [Otevřete klasické zásady](#open-a-classic-policy) zobrazíte nastavení konfigurace.
1. Vytvořte novou zásadu podmíněného přístupu Azure AD k nahrazení klasické zásady. 
1. Zakážete klasické zásady.

## <a name="open-a-classic-policy"></a>Otevřete klasické zásady

1. V [webu Azure portal](https://portal.azure.com), v levém navigačním panelu klikněte na **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration-mfa/01.png)

1. Na **Azure Active Directory** stránku, **spravovat** klikněte na tlačítko **podmíněného přístupu**.

   ![Podmíněný přístup](./media/policy-migration-mfa/02.png)

1. V **spravovat** klikněte na tlačítko **klasické zásady (preview)** .

   ![Klasické zásady](./media/policy-migration-mfa/12.png)

1. V seznamu klasické zásady, klikněte na zásadu, která vyžaduje **ověřování službou Multi-Factor Authentication** pro cloudové aplikace.

   ![Klasické zásady](./media/policy-migration-mfa/13.png)

## <a name="create-a-new-conditional-access-policy"></a>Vytvořit novou zásadu podmíněného přístupu

1. V [webu Azure portal](https://portal.azure.com), v levém navigačním panelu klikněte na **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration/01.png)

1. Na **Azure Active Directory** stránku, **spravovat** klikněte na tlačítko **podmíněného přístupu**.

   ![Podmíněný přístup](./media/policy-migration/02.png)

1. Na **podmíněného přístupu** stránky, otevřete **nový** klikněte na stránku, na panelu nástrojů v horní části **přidat**.

   ![Podmíněný přístup](./media/policy-migration/03.png)

1. Na **nový** stránku, **název** textového pole zadejte název zásady.

   ![Podmíněný přístup](./media/policy-migration/29.png)

1. V **přiřazení** klikněte na tlačítko **uživatelů a skupin**.

   ![Podmíněný přístup](./media/policy-migration/05.png)

   1. Pokud máte vybrané všechny uživatele v klasické zásady, klikněte na tlačítko **všichni uživatelé**. 

   ![Podmíněný přístup](./media/policy-migration/35.png)

   1. Pokud máte vybrané v klasické zásady skupiny, klikněte na tlačítko **výběr uživatelů a skupin**a potom vyberte požadované uživatele a skupiny.

   ![Podmíněný přístup](./media/policy-migration/36.png)

   1. Pokud máte vyloučené skupiny, klikněte na tlačítko **vyloučit** kartu a potom vyberte požadované uživatele a skupiny. 

   ![Podmíněný přístup](./media/policy-migration/37.png)

1. Na **nový** stránky, otevřete **cloudové aplikace** stránku, **přiřazení** klikněte na tlačítko **cloudové aplikace**.

1. Na **cloudové aplikace** stránce, proveďte následující kroky:

   ![Podmíněný přístup](./media/policy-migration/08.png)

   1. Klikněte na tlačítko **vyberte aplikace**.

   1. Klikněte na **Vybrat**.

   1. Na **vyberte** stránce vyberte cloudové aplikace a pak klikněte na **vyberte**.

   1. Na **cloudové aplikace** klikněte na **provádí**.

1. Pokud máte **vyžadovat vícefaktorové ověřování** vybrané:

   ![Podmíněný přístup](./media/policy-migration/26.png)

   1. V **ovládací prvky přístupu** klikněte na tlačítko **udělení**.

   ![Podmíněný přístup](./media/policy-migration/27.png)

   1. Na **udělení** klikněte na **udělit přístup**a potom klikněte na tlačítko **vyžadovat vícefaktorové ověřování**.

   1. Klikněte na **Vybrat**.

1. Klikněte na tlačítko **na** povolit vaše zásady.

   ![Podmíněný přístup](./media/policy-migration/30.png)

## <a name="disable-the-classic-policy"></a>Zakázat klasické zásady

Chcete-li zakázat klasické zásady, klikněte na tlačítko **zakázat** v **podrobnosti** zobrazení.

![Klasické zásady](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Další postup

- Další informace o migraci klasické zásady, najdete v části [migrace klasických zásad na webu Azure Portal](policy-migration.md).
- Pokud chcete vědět, jak nakonfigurovat zásady podmíněného přístupu najdete v tématu [vyžadovat vícefaktorové ověřování pro konkrétní aplikace, pomocí Azure Active Directory podmíněného přístupu](app-based-mfa.md).
- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md).
