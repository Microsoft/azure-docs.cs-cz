---
title: Začínáme s podmíněným přístupem v Azure Active Directory | Microsoft Docs
description: Otestujte podmíněný přístup pomocí podmínku umístění.
services: active-directory
keywords: podmíněný přístup k aplikacím, podmíněného přístupu s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: cd701b8c6f015afc60b434317d0d7fef0560e9af
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723700"
---
# <a name="get-started-with-conditional-access-in-azure-active-directory"></a>Začínáme s podmíněným přístupem v Azure Active Directory

Podmíněný přístup je funkce služby Azure Active Directory, který umožňuje definovat podmínky, za kterých oprávněným uživatelům přístup k vaší aplikace. 

Toto téma obsahuje pokyny pro testování podmíněného přístupu na základě podmínky umístění ve vašem prostředí.  


## <a name="scenario-description"></a>Popis scénáře

Jeden požadavek na běžné v mnoha organizacích je pouze vyžadovat vícefaktorové ověřování pro přístup k aplikacím, který neprovádí k podnikovému intranetu. S Azure Active Directory můžete snadno dosažení tohoto cíle konfigurací zásady podmíněného přístupu na základě polohy. Toto téma poskytuje podrobné pokyny ke konfiguraci související zásad. Využívá zásady [důvěryhodné IP adresy](authentication/howto-mfa-mfasettings.md#trusted-ips) k rozlišení mezi pokusy o přístup z podnikové síti je intranetu a všechny ostatní umístění.


## <a name="prerequisites"></a>Požadavky

Scénáře uvedené v tomto tématu se předpokládá, že jste obeznámeni s koncepty uvedených v [podmíněného přístupu Azure Active Directory](active-directory-conditional-access-azure-portal.md).

Chcete-li tento scénář otestovat, je potřeba:

- Vytvoření zkušebního uživatele 

- Přiřazení licence Azure AD Premium k testovacího uživatele

- Konfigurace spravované aplikace a přiřadit testovacího uživatele

- Konfigurovat důvěryhodné IP adresy

Pokud potřebujete další podrobnosti o důvěryhodné IP adresy, přečtěte si [důvěryhodné IP adresy](authentication/howto-mfa-mfasettings.md#trusted-ips).


## <a name="policy-configuration-steps"></a>Postup konfigurace zásad

**Zásady podmíněného přístupu, postupujte při konfiguraci:**

1. Na portálu Azure v levé navigační panel, klikněte na tlačítko **Azure Active Directory**. 

    ![Podmíněný přístup](./media/active-directory-conditional-access-azure-portal-get-started/01.png)

2. Na **Azure Active Directory** okno v **spravovat** klikněte na tlačítko **podmíněného přístupu**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-azure-portal-get-started/02.png)
 
3. Na **podmíněného přístupu** okno Otevřít **nový** , na panelu nástrojů v horní části klikněte na **přidat**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-azure-portal-get-started/03.png)

4. Na **nový** okno v **název** textovému poli, zadejte název zásady.

    ![Podmíněný přístup](./media/active-directory-conditional-access-azure-portal-get-started/04.png)

5. V **přiřazení** klikněte na tlačítko **uživatelů a skupin**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-azure-portal-get-started/05.png)

6. Na **uživatelů a skupin** okno, proveďte následující kroky:

    ![Podmíněný přístup](./media/active-directory-conditional-access-azure-portal-get-started/06.png)

    a. Klikněte na tlačítko **vyberte uživatele a skupiny**.

    b. Klikněte na **Vybrat**.

    c. Na **vyberte** okně vyberte svého testovacího uživatele a pak klikněte na tlačítko **vyberte**.

    d. Na **uživatelů a skupin** okně klikněte na tlačítko **provádí**.

7. Na **nový** okno Otevřít **cloudových aplikací** okno v **přiřazení** klikněte na tlačítko **cloudových aplikací**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. Na **cloudových aplikací** okno, proveďte následující kroky:

    ![Podmíněný přístup](./media/active-directory-conditional-access-azure-portal-get-started/08.png)

    a. Klikněte na tlačítko **vybrat aplikace**.

    b. Klikněte na **Vybrat**.

    c. Na **vyberte** okně vyberte cloudové aplikace a pak klikněte na tlačítko **vyberte**.

    d. Na **cloudových aplikací** okno, klikněte na **provést**.

9. Na **nový** okno Otevřít **podmínky** okno v **přiřazení** klikněte na tlačítko **podmínky**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-azure-portal-get-started/09.png)

10. Na **podmínky** okno Otevřít **umístění** okně klikněte na tlačítko **umístění**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-azure-portal-get-started/10.png)

11. Na **umístění** okno, proveďte následující kroky:

    ![Podmíněný přístup](./media/active-directory-conditional-access-azure-portal-get-started/11.png)

    a. V části **konfigurace**, klikněte na tlačítko **Ano**.

    b. V části **zahrnout**, klikněte na tlačítko **všech umístění**.

    c. Klikněte na tlačítko **vyloučit**a potom klikněte na **všechny důvěryhodné IP adresy**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-azure-portal-get-started/12.png)

    d. Klikněte na **Done** (Hotovo).

12. Na **podmínky** okně klikněte na tlačítko **provádí**.

13. Na **nový** okno Otevřít **Grant** okno v **ovládací prvky** klikněte na tlačítko **Grant**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. Na **Grant** okno, proveďte následující kroky:

    ![Podmíněný přístup](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Vyberte **vyžadovat vícefaktorové ověřování**.

    b. Klikněte na **Vybrat**.

15. Na **nový** okno, v části **povolit zásady**, klikněte na tlačítko **na**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. Na **nový** okně klikněte na tlačítko **vytvořit**.


## <a name="testing-the-policy"></a>Testování zásad

K otestování vaše zásady, musí aplikace přístup ze zařízení, která: 

1. IP adresu, která je v rámci vaší nakonfigurované důvěryhodné IP adresy 

1. IP adresu, která není v rámci vaší nakonfigurované důvěryhodné IP adresy

Služba Multi-Factor authentication musí být pouze požadované při pokusu o připojení, která byla vytvořená ze zařízení, které není v rámci vaší nakonfigurované důvěryhodné IP adresy. 


## <a name="next-steps"></a>Další postup

Pokud chcete získat další informace o podmíněného přístupu, přečtěte si téma [podmíněného přístupu Azure Active Directory](active-directory-conditional-access-azure-portal.md).

