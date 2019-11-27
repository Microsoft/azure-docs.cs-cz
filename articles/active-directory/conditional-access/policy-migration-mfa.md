---
title: Migrace zásad podmíněného přístupu – Azure Active Directory
description: Tento článek ukazuje, jak migrovat klasické zásady, které vyžadují službu Multi-Factor Authentication v Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca00ae62ba114aecef48117fd8a54b7f2e962dfd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380329"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Migrace klasických zásad, které vyžadují vícefaktorové ověřování v Azure Portal

V tomto kurzu se dozvíte, jak migrovat klasické zásady, které pro cloudovou aplikaci vyžadují službu **Multi-Factor Authentication** . I když to není předpoklad, doporučujeme, abyste před zahájením migrace klasických zásad přečetli [v Azure Portal migrace klasických zásad](policy-migration.md) .

## <a name="overview"></a>Přehled

Scénář v tomto článku ukazuje, jak migrovat klasické zásady, které vyžadují službu **Multi-Factor Authentication** pro cloudovou aplikaci.

![Azure Active Directory](./media/policy-migration/33.png)

Proces migrace se skládá z následujících kroků:

1. [Otevřete zásadu Classic](#open-a-classic-policy) a získejte nastavení konfigurace.
1. Vytvořte nové zásady podmíněného přístupu Azure AD, které nahradí vaše klasické zásady. 
1. Zakáže klasické zásady.

## <a name="open-a-classic-policy"></a>Otevření klasických zásad

1. V [Azure Portal](https://portal.azure.com)klikněte v levém navigačním panelu na možnost **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration-mfa/01.png)

1. Na stránce **Azure Active Directory** v části **Spravovat** klikněte na **podmíněný přístup**.

   ![Podmíněný přístup](./media/policy-migration-mfa/02.png)

1. V části **Spravovat** klikněte na **klasické zásady (Preview)** .

   ![Klasické zásady](./media/policy-migration-mfa/12.png)

1. V seznamu klasických zásad klikněte na zásadu, která vyžaduje službu **Multi-Factor Authentication** pro cloudovou aplikaci.

   ![Klasické zásady](./media/policy-migration-mfa/13.png)

## <a name="create-a-new-conditional-access-policy"></a>Vytvořit nové zásady podmíněného přístupu

1. V [Azure Portal](https://portal.azure.com)klikněte v levém navigačním panelu na možnost **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration/01.png)

1. Na stránce **Azure Active Directory** v části **Spravovat** klikněte na **podmíněný přístup**.

   ![Podmíněný přístup](./media/policy-migration/02.png)

1. Na stránce **podmíněný přístup** otevřete **novou** stránku tak, že na panelu nástrojů v horní části kliknete na **Přidat**.

   ![Podmíněný přístup](./media/policy-migration/03.png)

1. Na **nové** stránce zadejte do textového pole **název** název zásady.

   ![Podmíněný přístup](./media/policy-migration/29.png)

1. V části **přiřazení** klikněte na **Uživatelé a skupiny**.

   ![Podmíněný přístup](./media/policy-migration/05.png)

   1. Pokud máte všechny uživatele vybrané v klasických zásadách, klikněte na **Všichni uživatelé**. 

      ![Podmíněný přístup](./media/policy-migration/35.png)

   1. Pokud máte v klasických zásadách vybrané skupiny, klikněte na **Vybrat uživatele a skupiny**a pak vyberte požadované uživatele a skupiny.

      ![Podmíněný přístup](./media/policy-migration/36.png)

   1. Pokud máte Vyloučené skupiny, klikněte na kartu **vyloučit** a pak vyberte požadované uživatele a skupiny. 

      ![Podmíněný přístup](./media/policy-migration/37.png)

1. Na stránce **Nový** otevřete **cloudové aplikace** v části **přiřazení** klikněte na **cloudové aplikace**.
1. Na stránce **cloudové aplikace** proveďte následující kroky:
   1. Klikněte na **vybrat aplikace**.
   1. Klikněte na **Vybrat**.
   1. Na stránce **Vybrat** Vyberte cloudovou aplikaci a pak klikněte na **Vybrat**.
   1. Na stránce **cloudové aplikace** klikněte na **Hotovo**.
1. Pokud máte vybrané **ověřování Multi-Factor Authentication** :

   ![Podmíněný přístup](./media/policy-migration/26.png)

   1. V části **řízení přístupu** klikněte na **udělit**.

      ![Podmíněný přístup](./media/policy-migration/27.png)

   1. Na stránce **udělení** klikněte na **udělit přístup**a pak klikněte na **vyžadovat službu Multi-Factor Authentication**.
   1. Klikněte na **Vybrat**.
1. Kliknutím **na** zapnout zásadu povolíte.

   ![Podmíněný přístup](./media/policy-migration/30.png)

## <a name="disable-the-classic-policy"></a>Zakázat klasické zásady

Pokud chcete zakázat klasické zásady, klikněte na **Zakázat** v zobrazení **podrobností** .

![Klasické zásady](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Další kroky

- Další informace o migraci klasických zásad najdete v tématu [migrace klasických zásad v Azure Portal](policy-migration.md).
- Pokud chcete zjistit, jak nakonfigurovat zásady podmíněného přístupu, přečtěte si téma [vyžádání MFA pro konkrétní aplikace s Azure Active Directory podmíněný přístup](app-based-mfa.md).
- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, přečtěte si [osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md).
