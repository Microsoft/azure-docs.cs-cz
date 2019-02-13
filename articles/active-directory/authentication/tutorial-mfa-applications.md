---
title: Zapnutí pilotního nasazení služby Azure Multi-Factor Authentication
description: V tomto kurzu zapnete vícefaktorové ověřování služby Azure AD pro pilotní skupinu uživatelů.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fdf88ed6cedaa38676a56536ff1eda7ee6bca66
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204793"
---
# <a name="tutorial-complete-an-azure-multi-factor-authentication-pilot-roll-out"></a>Kurz: Dokončení Azure Multi-Factor Authentication pilotní zavedení

V tomto kurzu si projdete konfigurací zásad podmíněného přístupu, kterou povolíte vícefaktorové ověřování Azure Multi-Factor Authentication (Azure MFA) při přihlašování k webu Azure Portal. Zásady se nasadí pro konkrétní skupinu pilotních uživatelů, která je otestuje. Nasazení Azure MFA pomocí podmíněného přístupu poskytuje organizacím i správcům v porovnání s tradiční metodou vynucení významnou flexibilitu.

> [!div class="checklist"]
> * Povolení služby Azure Multi-Factor Authentication
> * Testování služby Azure Multi-Factor Authentication

## <a name="prerequisites"></a>Požadavky

* Funkční tenant Azure AD, který má přiřazenou alespoň zkušební licenci.
* Účet s oprávněními globálního správce.
* Bez oprávnění správce testovacího uživatele s heslem znáte pro testování, pokud je potřeba vytvořit uživatele najdete v článku [rychlý start: Přidání nových uživatelů do služby Azure Active Directory](../add-users-azure-active-directory.md).
* Pilotní skupina pro testování, ve které je uživatel bez oprávnění správce členem. Pokud potřebujete skupinu vytvořit, podívejte se do článku [Vytvoření skupiny a přidání členů ve službě Azure AD](../active-directory-groups-create-azure-portal.md).

## <a name="enable-azure-multi-factor-authentication"></a>Povolení služby Azure Multi-Factor Authentication

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce.
1. Přejděte na **Azure Active Directory** a potom na **Podmíněný přístup**.
1. Vyberte **Nová zásada**.
1. Zásadu pojmenujte **MFA Pilot**.
1. V části **Uživatelé a skupiny** zvolte přepínač **Vyberte uživatele a skupiny**.
    * Vyberte pilotní skupinu, kterou jste vytvořili jako součást požadavků tohoto článku.
    * Klikněte na **Hotovo**.
1. V části **Cloudové aplikace** zvolte přepínač **Vybrat aplikace**.
    * Cloudovou aplikací pro web Azure Portal je **Portál pro správu Microsoft Azure**.
    * Klikněte na **Vybrat**.
    * Klikněte na **Hotovo**.
1. Oddíl **Podmínky** přeskočte.
1. Přesvědčte se, že přepínač **Udělit přístup** v části **Udělení** je vybraný.
    * Zaškrtněte políčko **Vyžadovat vícefaktorové ověřování**.
    * Klikněte na **Vybrat**.
1. Oddíl **Relace** přeskočte.
1. **Zapněte** přepínač **Povolit zásadu**.
1. Klikněte na **Vytvořit**

## <a name="test-azure-multi-factor-authentication"></a>Testování služby Azure Multi-Factor Authentication

Funkčnost zásad podmíněného přístupu můžete otestovat tak, že se přihlásíte k prostředku, který vícefaktorové ověřování nevyžaduje, a potom se přihlásíte k webu Azure Portal, který vícefaktorové ověřování vyžaduje.

1. Otevřete nové okno prohlížeče v režimu InPrivate nebo Incognito a přejděte na adresu [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Přihlaste se pomocí testovacího účtu uživatele, který jste vytvořili jako součást požadavků tohoto článku, a všimněte si, že by se neměla zobrazit výzva k vícefaktorovému ověřování.
   * Zavřete okno prohlížeče.
2. Otevřete nové okno prohlížeče v režimu InPrivate nebo Incognito a přejděte na adresu [https://portal.azure.com](https://portal.azure.com).
   * Přihlaste se pomocí testovacího účtu uživatele, který jste vytvořili jako součást požadavků tohoto článku, a všimněte si, že nyní by se výzva k vícefaktorovému ověřování zobrazit měla.
   * Zavřete okno prohlížeče.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se rozhodnete, že už funkci nakonfigurovanou jako součást tohoto kurzu používat nechcete, proveďte následující.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte na **Azure Active Directory** a potom na **Podmíněný přístup**.
1. Vyberte zásady podmíněného přístupu, které jste vytvořili.
1. Klikněte na **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zapnuli službu Azure Multi-Factor Authentication. Pokračujte k dalšímu kurzu, ve kterém se dozvíte, jak můžete integrovat Azure Identity Protection do prostředí pro samoobslužné resetování hesla a vícefaktorové ověřování.

> [!div class="nextstepaction"]
> [Hodnocení rizika při přihlášení](tutorial-risk-based-sspr-mfa.md)
