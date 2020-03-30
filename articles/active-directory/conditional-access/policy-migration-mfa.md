---
title: Migrace zásad podmíněného přístupu – Azure Active Directory
description: Tento článek ukazuje, jak migrovat klasické zásady, které vyžadují vícefaktorové ověřování na webu Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: d637ac464b689a25ce5d5a79cf47da0c85d38d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74846014"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Migrace klasických zásad na webu Azure Portal

Tento článek ukazuje, jak migrovat klasické zásady, které vyžadují **vícefaktorové ověřování** pro cloudovou aplikaci. I když to není podmínkou, doporučujeme si [přečíst migrovat klasické zásady na webu Azure portal](policy-migration.md) před zahájením migrace klasické zásady.

![Klasické podrobnosti zásad, které vyžadují vícefaktorové informace pro aplikaci Salesforce](./media/policy-migration/33.png)

Proces migrace se skládá z následujících kroků:

1. [Otevřete klasické zásady](#open-a-classic-policy) a získejte nastavení konfigurace.
1. Vytvořte novou zásadu podmíněného přístupu Azure AD, která nahradí klasické zásady. 
1. Zakažte klasické zásady.

## <a name="open-a-classic-policy"></a>Otevření klasické zásady

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na**podmíněný přístup****zabezpečení služby** >  **Azure Active Directory** > .
1. Vyberte **klasické zásady**.

   ![Zobrazení klasických zásad](./media/policy-migration-mfa/12.png)

1. V seznamu klasických zásad vyberte zásady, které chcete migrovat. Zdokumentujte nastavení konfigurace, abyste mohli znovu vytvořit pomocí nové zásady podmíněného přístupu.

## <a name="create-a-new-conditional-access-policy"></a>Vytvoření nové zásady podmíněného přístupu

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na**podmíněný přístup****zabezpečení služby** >  **Azure Active Directory** > .
1. Chcete-li vytvořit novou zásadu podmíněného přístupu, vyberte **možnost Nová zásada**.
1. Na stránce **Nový** zadejte do textového pole **Název** název zásady.
1. V části **Přiřazení** klikněte na **Položku Uživatelé a skupiny**.
   1. Pokud máte v klasických zásadách vybraní všichni uživatelé, klikněte na **Všechny uživatele**. 
   1. Pokud máte v klasických zásadách vybrané skupiny, klikněte na **Vybrat uživatele a skupiny a**vyberte požadované uživatele a skupiny.
   1. Pokud máte vyloučené skupiny, klikněte na kartu **Vyloučit** a vyberte požadované uživatele a skupiny. 
   1. Vybrat **Hotovo**
1. V části **Přiřazení** klikněte na **Cloudové aplikace nebo akce**.
1. Na stránce **Cloudové aplikace nebo akce** proveďte následující kroky:
   1. Klikněte na **Vybrat aplikace**.
   1. Klepněte na **tlačítko Vybrat**.
   1. Na stránce **Vybrat** vyberte cloudovou aplikaci a klikněte na **Vybrat**.
   1. Na stránce **Cloudové aplikace** klikněte na **Hotovo**.
1. Pokud máte **vybrat vyžadovat vícefaktorové ověřování:**
   1. V části **Ovládací prvky aplikace Access** klepněte na tlačítko **Udělit**.
   1. Na stránce **Grant** klikněte na **Udělit přístup**a potom klikněte na **Vyžadovat vícefaktorové ověřování**.
   1. Klepněte na **tlačítko Vybrat**.
1. Klepnutím **na tlačítko Zapnuto** povolíte zásadu a vyberte možnost **Uložit**.

   ![Vytvoření zásad podmíněného přístupu](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>Zakázání klasické zásady

Pokud chcete klasické zásady zakázat, klikněte v zobrazení **Podrobnosti** na **Zakázat.**

![Zakázání klasických zásad](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Další kroky

- Další informace o klasické migraci zásad najdete [v tématu Migrace klasických zásad na webu Azure Portal](policy-migration.md).
- [Použití režimu pouze pro sestavu pro podmíněný přístup k určení dopadu nových rozhodnutí zásad.](concept-conditional-access-report-only.md)