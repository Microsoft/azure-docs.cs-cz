---
title: Migrace zásad podmíněného přístupu pomocí Multi-Factor Authentication – Azure Active Directory
description: Tento článek ukazuje, jak migrovat klasické zásady, které vyžadují službu Multi-Factor Authentication v Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cc99241f5fa7c09c69db13dcb3f6c3aaa75e376
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87027389"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Migrace klasických zásad v Azure Portal

V tomto článku se dozvíte, jak migrovat klasické zásady, které pro cloudovou aplikaci vyžadují službu **Multi-Factor Authentication** . I když to není předpoklad, doporučujeme, abyste před zahájením migrace klasických zásad přečetli [v Azure Portal migrace klasických zásad](policy-migration.md) .

![Podrobnosti o klasických zásadách vyžadujících MFA pro aplikaci Salesforce](./media/policy-migration/33.png)

Proces migrace se skládá z následujících kroků:

1. [Otevřete zásadu Classic](#open-a-classic-policy) a získejte nastavení konfigurace.
1. Vytvořte nové zásady podmíněného přístupu Azure AD, které nahradí vaše klasické zásady. 
1. Zakáže klasické zásady.

## <a name="open-a-classic-policy"></a>Otevření klasických zásad

1. V [Azure Portal](https://portal.azure.com)přejděte na **Azure Active Directory**  >    >  **podmíněný přístup** zabezpečení.
1. Vyberte **klasické zásady**.

   ![Zobrazení klasických zásad](./media/policy-migration-mfa/12.png)

1. V seznamu klasických zásad vyberte zásadu, kterou chcete migrovat. Nastavení konfigurace můžete zdokumentovat tak, aby se nová zásada podmíněného přístupu mohla znovu vytvořit.

## <a name="create-a-new-conditional-access-policy"></a>Vytvořit nové zásady podmíněného přístupu

1. V [Azure Portal](https://portal.azure.com)přejděte na **Azure Active Directory**  >    >  **podmíněný přístup** zabezpečení.
1. Nové zásady podmíněného přístupu vytvoříte tak, že vyberete **nové zásady**.
1. Na **nové** stránce zadejte do textového pole **název** název zásady.
1. V části **přiřazení** klikněte na **Uživatelé a skupiny**.
   1. Pokud máte všechny uživatele vybrané v klasických zásadách, klikněte na **Všichni uživatelé**. 
   1. Pokud máte v klasických zásadách vybrané skupiny, klikněte na **Vybrat uživatele a skupiny** a pak vyberte požadované uživatele a skupiny.
   1. Pokud máte Vyloučené skupiny, klikněte na kartu **vyloučit** a pak vyberte požadované uživatele a skupiny. 
   1. Vyberte **Hotovo** .
1. V části **přiřazení** klikněte na **cloudové aplikace nebo akce**.
1. Na stránce **cloudové aplikace nebo akce** proveďte následující kroky:
   1. Klikněte na **vybrat aplikace**.
   1. Klikněte na **Vybrat**.
   1. Na stránce **Vybrat** Vyberte cloudovou aplikaci a pak klikněte na **Vybrat**.
   1. Na stránce **cloudové aplikace** klikněte na **Hotovo**.
1. Pokud máte vybrané **ověřování Multi-Factor Authentication** :
   1. V části **řízení přístupu** klikněte na **udělit**.
   1. Na stránce **udělení** klikněte na **udělit přístup** a pak klikněte na **vyžadovat službu Multi-Factor Authentication**.
   1. Klikněte na **Vybrat**.
1. Kliknutím **na zapnout zásadu** povolíte a pak vyberete **Uložit**.

   ![Vytvoření zásady podmíněného přístupu](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>Zakázat klasické zásady

Pokud chcete zakázat klasické zásady, klikněte na **Zakázat** v zobrazení **podrobností** .

![Zakázat klasické zásady](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Další kroky

- Další informace o migraci klasických zásad najdete v tématu [migrace klasických zásad v Azure Portal](policy-migration.md).
- [Použijte režim pouze pro sestavy pro podmíněný přístup k určení dopadu nových rozhodnutí o zásadách.](concept-conditional-access-report-only.md)